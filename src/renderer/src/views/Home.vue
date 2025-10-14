<template>
  <el-container style="height: 100%">
    <el-header height="auto">
      <div class="header-content">
        <div class="input-group">
          <el-input v-model="dlUrl" placeholder="输入微信文章链接" clearable size="large">
            <template #prefix>
              <el-icon>
                <Link />
              </el-icon>
            </template>
          </el-input>
        </div>
        <div class="button-group">
          <el-button type="primary" size="large" @click="dlOne">
            <el-icon>
              <Download />
            </el-icon>
            单篇下载
          </el-button>
          <el-button type="success" size="large" :disabled="btnInfo.batchBtn" @click="dlBatch">
            <el-icon>
              <FolderOpened />
            </el-icon>
            批量下载
          </el-button>
          <el-button :type="btnInfo.restrictionsClass ? 'danger' : 'info'" size="large"
            :disabled="btnInfo.restrictionsBtn" @click="dlRestrictionsBatch">
            <el-icon>
              <View />
            </el-icon>
            {{ btnInfo.restrictionsClass ? '停止监控' : '监控下载' }}
          </el-button>
        </div>
      </div>
    </el-header>
    <el-main>
      <div v-if="logArr.length === 0" class="welcome-container">
        <div class="welcome-card">
          <h2>欢迎使用微信公众号文章下载工具</h2>
          <el-divider />
          <div class="info-section">
            <div class="info-item">
              <el-icon>
                <CircleCheck />
              </el-icon>
              <span>本软件开源免费</span>
            </div>
            <div class="info-item">
              <el-icon>
                <Link />
              </el-icon>
              <span>源码地址：<a target="_blank" href="https://github.com/musdrop/wechatDownload">wechatDownload</a></span>
            </div>
          </div>
          <el-divider />
          <div class="guide-section">
            <h3><el-icon>
                <Document />
              </el-icon> 使用说明</h3>
            <ul>
              <li>单篇文章下载：输入链接可直接使用</li>
              <li>批量下载和监控下载功能：需要开启代理</li>
              <li>使用代理需要安装证书，证书路径请前往设置中心查看</li>
              <li>证书安装完成请重启软件</li>
            </ul>
          </div>
          <el-divider />
          <div class="tips-section">
            <el-alert type="warning" :closable="false" show-icon>
              <template #title>
                如遇网络问题，请尝试重启软件
              </template>
            </el-alert>
          </div>
        </div>
      </div>
      <div v-else class="log-container">
        <el-scrollbar ref="scrollbarRef">
          <div ref="innerRef" class="log-content">
            <div v-for="(logItem, i) in logArr" :key="i" class="log-item">
              <p v-if="logItem.flgHtml" v-html="logItem.msg"></p>
              <p v-else>{{ logItem.msg }}</p>
            </div>
          </div>
        </el-scrollbar>
      </div>
    </el-main>
  </el-container>
</template>

<script setup lang="ts">
import { ElScrollbar } from 'element-plus';
import { Download, Link, FolderOpened, View, InfoFilled, CircleCheck, Document } from '@element-plus/icons-vue';
import { nextTick, reactive, ref } from 'vue';

const dlUrl = ref('');
const innerRef = ref<HTMLDivElement>();
const scrollbarRef = ref<InstanceType<typeof ElScrollbar>>();
class LogInfo {
  msg: string;
  flgHtml: boolean;

  constructor(msg: string, flgHtml: boolean) {
    this.msg = msg;
    this.flgHtml = flgHtml;
  }
}
const logArr = reactive([] as LogInfo[]);
const btnInfo = reactive({
  batchBtn: false,
  restrictionsBtn: false,
  restrictionsClass: false
});

/**
 * 下载按钮事件
 */
function dlOne() {
  const url = dlUrl.value;
  const checkResult = checkURL(url);
  if (!checkResult) {
    window.api.showMessageBox({
      type: 'warning',
      message: '请输入正确的url'
    });
    return;
  }
  // 下载详情页数据
  window.api.downloadOne(url);
}
/**
 * 批量下载按钮事件
 */
function dlBatch() {
  btnInfo.batchBtn = true;
  window.api.monitorArticle();
}
/**
 * 监控下载按钮事件
 */
function dlRestrictionsBatch() {
  btnInfo.restrictionsClass = !btnInfo.restrictionsClass;
  // 开始监控
  if (btnInfo.restrictionsClass) {
    window.api.monitorLimitArticle();
  } else {
    // 结束监控
    window.api.stopMonitorLimitArticle();
  }
}
/*
 * 校验url
 */
function checkURL(URL): boolean {
  const str = URL;
  const Expression = /^(https?:\/\/)([0-9a-z.]+)(:[0-9]+)?([/0-9a-z.]+)?(\?[0-9a-z&=]+)?(#[0-9-a-z]+)?/i;
  if (Expression.test(str) == true) {
    return true;
  }
  return false;
}

/**
 * 输出日志
 */
window.api.outputLog(async (_event, msg: string, flgAppend = false, flgHtml = false) => {
  outputLog(msg, flgAppend, flgHtml);
});
async function outputLog(msg: string, flgAppend = false, flgHtml = false) {
  if (flgAppend) {
    logArr.push({ msg, flgHtml });
  } else {
    logArr.length = 0;
    logArr.push({ msg, flgHtml });
  }
  await setScrollToBottom();
}
/**
 * 控制滚动条滚动到容器的底部
 */
async function setScrollToBottom() {
  // 注意：需要通过 nextTick 以等待 DOM 更新完成
  await nextTick();
  const max = innerRef.value!.clientHeight;
  scrollbarRef.value!.setScrollTop(max);
}
/**
 * 批量下载完成
 */
window.api.downloadFnish(async () => {
  btnInfo.batchBtn = false;
});
</script>

<style scoped>
.el-header {
  --el-header-padding: 16px 24px;
  background: #fff;
  border-bottom: 1px solid var(--el-border-color-lighter);
}

.header-content {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.input-group {
  width: 100%;
}

.button-group {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}

.el-main {
  --el-main-padding: 24px;
  background: #f5f7fa;
}

/* 欢迎页面样式 */
.welcome-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100%;
}

.welcome-card {
  background: #fff;
  border-radius: 8px;
  padding: 32px;
  max-width: 700px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
}

.welcome-card h2 {
  font-size: 24px;
  font-weight: 500;
  color: #303133;
  margin: 0 0 16px 0;
  text-align: center;
}

.info-section {
  margin: 20px 0;
}

.info-item {
  display: flex;
  align-items: center;
  gap: 8px;
  margin: 12px 0;
  font-size: 15px;
  color: #606266;
}

.info-item .el-icon {
  color: var(--el-color-success);
  font-size: 18px;
}

.info-item a {
  color: var(--el-color-primary);
  text-decoration: none;
}

.info-item a:hover {
  text-decoration: underline;
}

.guide-section {
  margin: 20px 0;
}

.guide-section h3 {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 18px;
  font-weight: 500;
  color: #303133;
  margin: 0 0 12px 0;
}

.guide-section ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.guide-section li {
  padding: 8px 0;
  color: #606266;
  position: relative;
  padding-left: 20px;
}

.guide-section li:before {
  content: "•";
  position: absolute;
  left: 0;
  color: var(--el-color-primary);
  font-weight: bold;
}

.tips-section {
  margin-top: 20px;
}

/* 日志容器样式 */
.log-container {
  background: #fff;
  border-radius: 8px;
  padding: 16px;
  height: 100%;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.log-content {
  padding: 8px;
}

.log-item {
  padding: 6px 0;
  border-bottom: 1px solid var(--el-border-color-lighter);
}

.log-item:last-child {
  border-bottom: none;
}

.log-item p {
  margin: 0;
  color: #606266;
  line-height: 1.6;
  word-break: break-all;
}
</style>
