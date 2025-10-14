<template>
  <el-container style="height: 100%" class="epub-container">
    <el-header height="auto" class="epub-header">
      <el-card shadow="never">
        <template #header>
          <div class="card-header">
            <el-icon>
              <Document />
            </el-icon>
            <span>生成 EPUB 电子书</span>
          </div>
        </template>
        <el-form :model="formData" label-width="90px" label-position="left">
          <el-form-item label="文件名" required>
            <el-input v-model="formData.title" placeholder="请填写 EPUB 文件名" clearable size="large">
              <template #prefix>
                <el-icon>
                  <EditPen />
                </el-icon>
              </template>
            </el-input>
          </el-form-item>
          <el-form-item label="数据文件夹" required>
            <el-input v-model="formData.epubDataPath" placeholder="选择包含文章的文件夹" readonly size="large">
              <template #prefix>
                <el-icon>
                  <Folder />
                </el-icon>
              </template>
              <template #append>
                <el-button @click="choseEpubDataPath">
                  <el-icon>
                    <FolderOpened />
                  </el-icon>
                  选择
                </el-button>
              </template>
            </el-input>
          </el-form-item>
          <el-form-item label="封面图片">
            <el-input v-model="formData.epubCover" placeholder="选择 EPUB 封面图片 (可选)" readonly size="large">
              <template #prefix>
                <el-icon>
                  <Picture />
                </el-icon>
              </template>
              <template #append>
                <el-button @click="choseCover">
                  <el-icon>
                    <PictureFilled />
                  </el-icon>
                  选择
                </el-button>
              </template>
            </el-input>
          </el-form-item>
          <el-form-item class="form-btn">
            <el-button type="primary" size="large" @click="onSubmit">
              <el-icon>
                <Finished />
              </el-icon>
              生成 EPUB
            </el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </el-header>
    <el-main class="epub-main">
      <div v-if="epubLogArr.length === 0" class="empty-state">
        <el-empty description="暂无生成记录">
          <template #image>
            <el-icon :size="80" color="#909399">
              <Reading />
            </el-icon>
          </template>
          <template #description>
            <p>填写表单信息后点击"生成 EPUB"按钮开始创建</p>
          </template>
        </el-empty>
      </div>
      <div v-else class="log-container">
        <el-card shadow="never">
          <template #header>
            <div class="card-header">
              <el-icon>
                <List />
              </el-icon>
              <span>生成日志</span>
            </div>
          </template>
          <el-scrollbar>
            <div class="log-content">
              <div v-for="(logItem, i) in epubLogArr" :key="i" class="log-item">
                <p v-if="logItem.flgHtml" v-html="logItem.msg"></p>
                <p v-else>{{ logItem.msg }}</p>
              </div>
            </div>
          </el-scrollbar>
        </el-card>
      </div>
    </el-main>
  </el-container>
</template>

<script setup lang="ts">
import { OpenDialogOptions } from 'electron';
import { ElMessage } from 'element-plus';
import {
  Document,
  EditPen,
  Folder,
  FolderOpened,
  Picture,
  PictureFilled,
  Finished,
  Reading,
  List
} from '@element-plus/icons-vue';
import { reactive } from 'vue';

class LogInfo {
  msg: string;
  flgHtml: boolean;

  constructor(msg: string, flgHtml: boolean) {
    this.msg = msg;
    this.flgHtml = flgHtml;
  }
}
const epubLogArr = reactive([] as LogInfo[]);

const formData = reactive({
  title: '',
  epubDataPath: '',
  epubCover: ''
});

const onSubmit = () => {
  if (!formData.title || formData.title.length <= 0) {
    ElMessage.error('请填写文件名');
    return;
  }
  if (!formData.epubDataPath || formData.epubDataPath.length <= 0) {
    ElMessage.error('请选择文件夹');
    return;
  }
  window.api.createEpub(Object.assign({}, formData));
};
/**
 * 选择文件夹
 */
function choseEpubDataPath() {
  const options: OpenDialogOptions = {
    title: '请选择文件夹',
    properties: ['openDirectory']
  };
  window.api.showOpenDialog(options, 'epubDataPath');
}
/**
 * 选择封面图片
 */
function choseCover() {
  const options: OpenDialogOptions = {
    title: '请选择图片',
    properties: ['openFile'],
    filters: [{ name: 'Images', extensions: ['jpg', 'jpeg', 'png'] }]
  };
  window.api.showOpenDialog(options, 'epubCover');
}
/**
 * 选择文件夹、图片的回调
 */
window.api.openDialogCallback(async (_event, callbackMsg: string, pathStr: string) => {
  formData[callbackMsg] = pathStr;
});
/**
 * 输出日志
 */
window.api.outputEpubLog(async (_event, msg: string, flgAppend = false, flgHtml = false) => {
  outputLog(msg, flgAppend, flgHtml);
});
async function outputLog(msg: string, flgAppend = false, flgHtml = false) {
  if (flgAppend) {
    epubLogArr.push({ msg, flgHtml });
  } else {
    epubLogArr.length = 0;
    epubLogArr.push({ msg, flgHtml });
  }
}
</script>

<style scoped>
.epub-container {
  background: #f5f7fa;
}

.epub-header {
  --el-header-padding: 24px;
  height: auto;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: 500;
  font-size: 16px;
}

.card-header .el-icon {
  font-size: 20px;
  color: var(--el-color-primary);
}

.el-form {
  margin-top: 16px;
}

.el-form-item {
  margin-bottom: 20px;
}

:deep(.form-btn .el-form-item__content) {
  justify-content: center;
}

.epub-main {
  --el-main-padding: 0 24px 24px 24px;
  overflow: hidden;
}

.empty-state {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  background: #fff;
  border-radius: 8px;
}

.empty-state p {
  color: #909399;
  font-size: 14px;
  margin-top: 8px;
}

.log-container {
  height: 100%;
}

.log-container .el-card {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.log-container :deep(.el-card__body) {
  flex: 1;
  overflow: hidden;
  padding: 0;
}

.log-content {
  padding: 16px;
  word-wrap: break-word;
}

.log-item {
  padding: 8px 0;
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
