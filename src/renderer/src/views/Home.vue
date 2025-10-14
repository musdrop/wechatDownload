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
      <Guide v-if="logArr.length === 0" />
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
import { Download, Link, FolderOpened, View } from '@element-plus/icons-vue';
import { nextTick, reactive, ref } from 'vue';
import Guide from './Guide.vue';

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
  --el-main-padding: 0;
  background: #f5f7fa;
}

/* 日志容器样式 */
.log-container {
  background: #fff;
  border-radius: 8px;
  padding: 16px;
  height: 100%;
  margin: 24px;
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
