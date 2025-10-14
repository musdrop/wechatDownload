<template>
  <el-container class="setting-container">

    <el-main class="setting-main">
      <el-row :gutter="16">
        <el-alert type="info" :closable="false" show-icon class="setting-alert">
          <p class="setting-alert-info">首次运行需要安装证书(仅一次)</p>
          <el-button type="primary" size="small" @click="installLicence">
            <el-icon>
              <Download />
            </el-icon>
            安装证书
          </el-button>
          <el-button type="default" size="small" @click="openLicence">
            <el-icon>
              <FolderOpened />
            </el-icon>
            打开证书路径
          </el-button>
        </el-alert>
        <el-col :span="12">
          <!-- 下载来源 -->
          <el-card class="setting-card" shadow="hover">
            <template #header>
              <div class="card-header">
                <el-icon>
                  <Coordinate />
                </el-icon>
                <span>下载来源</span>
              </div>
            </template>
            <el-radio-group v-model="settingInfo.dlSource" size="large">
              <el-radio-button label="网络" value="web" />
              <el-radio-button label="数据库" value="db" />
            </el-radio-group>
          </el-card>
          <!-- 下载选项 -->
          <el-card class="setting-card" shadow="hover">
            <template #header>
              <div class="card-header">
                <el-icon>
                  <Setting />
                </el-icon>
                <span>下载选项</span>
              </div>
            </template>
            <div class="checkbox-grid">
              <el-checkbox v-model="settingInfo.dlHtml" size="large">
                <el-icon>
                  <Document />
                </el-icon>
                下载为HTML
              </el-checkbox>
              <el-checkbox v-model="settingInfo.dlAudio" size="large">
                <el-icon>
                  <Headset />
                </el-icon>
                下载音频
              </el-checkbox>
              <el-checkbox v-model="settingInfo.dlMarkdown" size="large">
                <el-icon>
                  <EditPen />
                </el-icon>
                下载为Markdown
              </el-checkbox>
              <el-checkbox v-model="settingInfo.dlImg" size="large">
                <el-icon>
                  <Picture />
                </el-icon>
                下载图片
              </el-checkbox>
              <el-checkbox v-model="settingInfo.dlPdf" size="large">
                <el-icon>
                  <Document />
                </el-icon>
                下载为PDF
              </el-checkbox>
              <el-checkbox v-model="settingInfo.dlComment" size="large">
                <el-icon>
                  <ChatDotRound />
                </el-icon>
                下载评论
              </el-checkbox>
              <el-checkbox v-model="settingInfo.dlMysql" size="large">
                <el-icon>
                  <Coin />
                </el-icon>
                保存至MySQL
              </el-checkbox>
              <el-checkbox v-model="settingInfo.dlCommentReply" size="large">
                <el-icon>
                  <ChatLineRound />
                </el-icon>
                下载评论回复
              </el-checkbox>
              <el-checkbox v-model="settingInfo.skinExist" size="large">
                <el-icon>
                  <CircleClose />
                </el-icon>
                跳过现有文章
              </el-checkbox>
              <el-checkbox v-model="settingInfo.sourceUrl" size="large">
                <el-icon>
                  <Link />
                </el-icon>
                添加原文链接
              </el-checkbox>
              <el-checkbox v-model="settingInfo.saveMeta" size="large">
                <el-icon>
                  <Files />
                </el-icon>
                保存元数据
              </el-checkbox>
              <el-checkbox v-model="settingInfo.classifyDir" size="large">
                <el-icon>
                  <Folder />
                </el-icon>
                按公众号分类
              </el-checkbox>
            </div>
          </el-card>
          <!-- 过滤规则 -->
          <el-card class="setting-card" shadow="hover">
            <template #header>
              <div class="card-header">
                <el-icon>
                  <Filter />
                </el-icon>
                <span>过滤规则</span>
              </div>
            </template>
            <el-input v-model="settingInfo.filterRule" placeholder="筛选文章 (JSON格式)" :rows="3" type="textarea" />
          </el-card>
        </el-col>

        <el-col :span="12">
          <!-- 下载范围 -->
          <el-card class="setting-card" shadow="hover">
            <template #header>
              <div class="card-header">
                <el-icon>
                  <Calendar />
                </el-icon>
                <span>下载范围</span>
              </div>
            </template>
            <el-radio-group v-model="settingInfo.dlScpoe" size="large" style="margin-bottom: 12px;">
              <el-radio-button label="全部" value="all" />
              <el-radio-button label="今日" value="one" />
              <el-radio-button label="7天内" value="seven" />
              <el-radio-button label="一个月内" value="month" />
              <el-radio-button label="自定义" value="diy" />
            </el-radio-group>
            <div v-if="settingInfo.dlScpoe === 'diy'" class="date-picker-group">
              <el-date-picker v-model="settingInfo.startDate" type="date" placeholder="开始日期" format="YYYY年MM月DD日"
                value-format="YYYY-MM-DD" style="width: 100%;" />
              <el-date-picker v-model="settingInfo.endDate" type="date" placeholder="结束日期" format="YYYY年MM月DD日"
                value-format="YYYY-MM-DD" style="width: 100%;" />
            </div>
          </el-card>

          <!-- 线程配置 -->
          <el-card class="setting-card" shadow="hover">
            <template #header>
              <div class="card-header">
                <el-icon>
                  <Connection />
                </el-icon>
                <span>线程配置</span>
              </div>
            </template>
            <el-radio-group v-model="settingInfo.threadType" size="large" style="margin-bottom: 16px;">
              <el-radio-button label="单线程" value="single" />
              <el-radio-button label="多线程" value="multi" />
            </el-radio-group>
            <div class="number-input-group">
              <div class="input-item">
                <span class="input-label">
                  <el-icon>
                    <Timer />
                  </el-icon>
                  下载间隔 (毫秒)
                </span>
                <el-input-number v-model="settingInfo.dlInterval" controls-position="right" :min="0" size="default" />
              </div>
              <div class="input-item">
                <span class="input-label">
                  <el-icon>
                    <Grid />
                  </el-icon>
                  单批数量
                </span>
                <el-input-number v-model="settingInfo.batchLimit" controls-position="right" :min="0" size="default" />
              </div>
            </div>
          </el-card>

          <!-- MySQL配置 -->
          <el-card v-if="settingInfo.dlMysql" class="setting-card" shadow="hover">
            <template #header>
              <div class="card-header">
                <el-icon>
                  <Coin />
                </el-icon>
                <span>MySQL 配置</span>
                <el-button type="primary" size="small" @click="testConnect" style="margin-left: auto;">
                  测试连接
                </el-button>
              </div>
            </template>
            <el-form :model="settingInfo" label-width="70px" label-position="left">
              <el-form-item label="主机">
                <el-input v-model="settingInfo.mysqlHost" placeholder="localhost" />
              </el-form-item>
              <el-form-item label="端口">
                <el-input v-model="settingInfo.mysqlPort" placeholder="3306" />
              </el-form-item>
              <el-form-item label="用户名">
                <el-input v-model="settingInfo.mysqlUser" />
              </el-form-item>
              <el-form-item label="密码">
                <el-input v-model="settingInfo.mysqlPassword" type="password" show-password />
              </el-form-item>
              <el-form-item label="数据库">
                <el-input v-model="settingInfo.mysqlDatabase" />
              </el-form-item>
              <el-form-item label="表名">
                <el-input v-model="settingInfo.tableName" />
              </el-form-item>
            </el-form>
          </el-card>
        </el-col>
      </el-row>
      <el-card shadow="never">
        <!-- 路径设置 -->
        <div class="footer-section">
          <div class="path-item">
            <span class="path-label">
              <el-icon>
                <FolderOpened />
              </el-icon>
              保存路径
            </span>
            <el-input v-model="settingInfo.savePath" readonly />
            <el-button type="primary" @click="chosePath('savePath')">
              选择路径
            </el-button>
          </div>
          <div class="path-item">
            <span class="path-label">
              <el-icon>
                <Folder />
              </el-icon>
              缓存路径
            </span>
            <el-input v-model="settingInfo.tmpPath" readonly />
            <el-button type="primary" @click="chosePath('tmpPath')">
              选择路径
            </el-button>
          </div>
        </div>

        <!-- 版本信息 -->
        <el-divider />
        <div class="footer-section">
          <div class="version-info">
            <el-tag type="info" size="large">
              <el-icon>
                <InfoFilled />
              </el-icon>
              当前版本：{{ updateInfo.version }}
            </el-tag>
            <div class="version-actions">
              <el-button type="primary" @click="checkUpdate">
                <el-icon>
                  <Refresh />
                </el-icon>
                检查更新
              </el-button>
              <el-button type="default" @click="openLogsDir">
                <el-icon>
                  <Document />
                </el-icon>
                打开日志
              </el-button>
            </div>
          </div>
          <el-progress v-if="updateInfo.code == 3" :percentage="updateInfo.percentage"
            :format="() => updateInfo.percentageMsg" />
          <el-alert v-if="updateInfo.code > 0 && updateInfo.code != 3" :title="updateInfo.msg" type="info"
            :closable="false" />
        </div>
      </el-card>
    </el-main>
  </el-container>
</template>

<script setup lang="ts">
import { reactive, watch } from 'vue';
import { OpenDialogOptions } from 'electron';
import { ProgressInfo } from 'builder-util-runtime';
import {
  Download,
  FolderOpened,
  Coordinate,
  Filter,
  Setting,
  Document,
  Headset,
  EditPen,
  Picture,
  ChatDotRound,
  Coin,
  ChatLineRound,
  CircleClose,
  Link,
  Files,
  Folder,
  Calendar,
  Connection,
  Timer,
  Grid,
  InfoFilled,
  Refresh
} from '@element-plus/icons-vue';

const settingInfo = reactive({
  // 下载来源
  dlSource: 'web',
  // 线程类型
  threadType: 'multi',
  // 下载间隔
  dlInterval: 500,
  // 单批数量
  batchLimit: 5,
  // 下载为html
  dlHtml: true,
  // 下载为markdown
  dlMarkdown: 1,
  // 下载为pdf
  dlPdf: 0,
  // 保存至mysql
  dlMysql: 0,
  // 下载音频到本地
  dlAudio: 0,
  // 下载图片到本地
  dlImg: 0,
  // 跳过现有文章
  skinExist: 1,
  // 是否保存元数据
  saveMeta: 1,
  // 按公众号名字分类
  classifyDir: 1,
  // 添加原文链接
  sourceUrl: 1,
  // 是否下载评论
  dlComment: 0,
  // 是否下载评论回复
  dlCommentReply: 0,
  // 下载范围-7天内
  dlScpoe: 'seven',
  startDate: '',
  endDate: '',
  // 缓存目录
  tmpPath: '',
  // 在安装目录下创建文章的保存路径
  savePath: '',
  mysqlHost: 'localhost',
  mysqlPort: '3306',
  mysqlUser: '',
  mysqlPassword: '',
  mysqlDatabase: '',
  tableName: '',
  filterRule: ''
});
let settingInfoOrigin;

const updateInfo = reactive({
  version: 'v1.0',
  code: 0,
  msg: '',
  percentage: 0,
  percentageMsg: '0/0 M'
});

/**
 * 监听数据变化
 */
watch(settingInfo, async (_oldInfo, newInfo) => {
  for (const settingKey in newInfo) {
    const settingItem = newInfo[settingKey];
    if (settingItem != settingInfoOrigin[settingKey]) {
      storeSet(settingKey, settingItem);
    }
  }
  settingInfoOrigin = Object.assign({}, newInfo);
});
// 初始化所有设置
initSetting();
// 初始化信息
initInfo();

/**
 * 初始化所有设置并赋值
 */
function initSetting() {
  for (const settingItem in settingInfo) {
    let newValue = storeGet(settingItem);
    // 兼容旧版本的checkbox的0/1
    if (newValue && settingItem != 'dlInterval' && settingItem != 'batchLimit') {
      if (newValue == '1') {
        newValue = true;
      } else if (newValue == '0') {
        newValue = false;
      }
    }
    settingInfo[settingItem] = newValue;
  }
  settingInfoOrigin = Object.assign({}, settingInfo);
}
/**
 * 加载初始化信息
 */
function initInfo() {
  const _version: string = window.api.loadInitInfo();
  updateInfo.version = _version;
}

/**
 * 安装证书
 */
function installLicence() {
  window.api.installLicence();
}
/**
 * 打开证书路径
 */
function openLicence() {
  window.api.openPath(storeGet('caPath'));
}
/**
 * 测试mysql连接
 */
function testConnect() {
  window.api.testConnect();
}
/**
 * 选择保存路径/缓存路径
 */
function chosePath(pathKey: string) {
  const options: OpenDialogOptions = {
    title: '请选择保存路径',
    defaultPath: storeGet(pathKey),
    properties: ['openDirectory']
  };
  window.api.showOpenDialog(options, pathKey);
}
/**
 * 检查更新
 */
function checkUpdate() {
  window.api.checkForUpdate();
}
/**
 * 打开日志文件夹
 */
function openLogsDir() {
  window.api.openLogsDir();
}

function storeGet(key: string): any {
  return window.api.store.get(key);
}

async function storeSet(key: string, value: string | number) {
  window.api.store.set(key, value);
}
/**
 * 选择保存路径/缓存路径的回调
 */
window.api.openDialogCallback(async (_event, callbackMsg: string, pathStr: string) => {
  settingInfo[callbackMsg] = pathStr;
});
/**
 * 接收更新信息
 */
window.api.updateMsg((_event, msgObj: any) => {
  updateInfo.code = msgObj.code;
  updateInfo.msg = msgObj.msg;
});
// 接收下载进度
const numM = 1048576;
window.api.downloadProgress(async (_event, progressObj: ProgressInfo) => {
  updateInfo.percentage = progressObj.percent;
  const totalM = (progressObj.total / numM).toFixed(2);
  const transferredM = (progressObj.transferred / numM).toFixed(2);
  updateInfo.percentageMsg = `${transferredM}/${totalM}M`;
});
</script>

<style scoped>
.setting-container {
  height: 100%;
  background: #f5f7fa;
}

.setting-alert {
  background: #f5f7fa;
}

.setting-alert-info {
  display: inline;
  margin-right: 10px;
}

.setting-main {
  --el-main-padding: 24px;
  overflow-y: auto;
}

.setting-card {
  margin-bottom: 16px;
  border-radius: 8px;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: 500;
  font-size: 15px;
}

.card-header .el-icon {
  font-size: 18px;
  color: var(--el-color-primary);
}

.checkbox-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
}

.el-checkbox {
  display: flex;
  align-items: center;
  height: auto;
  margin: 0;
}

.el-checkbox .el-icon {
  margin-right: 4px;
}

.date-picker-group {
  display: flex;
  gap: 12px;
  margin-top: 12px;
}

.number-input-group {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.input-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.input-label {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 14px;
  color: #606266;
}

.input-label .el-icon {
  color: var(--el-color-primary);
}

.el-form-item {
  margin-bottom: 12px;
}

.footer-section {
  margin-bottom: 16px;
}

.footer-section:last-child {
  margin-bottom: 0;
}

.path-item {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 12px;
}

.path-item:last-child {
  margin-bottom: 0;
}

.path-label {
  display: flex;
  align-items: center;
  gap: 6px;
  min-width: 90px;
  font-size: 14px;
  color: #606266;
}

.path-label .el-icon {
  color: var(--el-color-primary);
}

.path-item .el-input {
  flex: 1;
}

.version-info {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 12px;
  flex-wrap: wrap;
  gap: 12px;
}

.version-actions {
  display: flex;
  gap: 8px;
}

.el-tag {
  padding: 8px 12px;
  height: auto;
}

.el-tag .el-icon {
  margin-right: 4px;
}
</style>
