<script setup lang="ts">
import { ref } from 'vue';
import { Download, Setting as SettingIcon, Document } from '@element-plus/icons-vue';
import Home from './views/Home.vue';
import Setting from './views/Setting.vue';
import EpubCreator from './views/EpubCreator.vue';
//中文包
import zhCn from 'element-plus/dist/locale/zh-cn.mjs';

const menuIdx = ref('1');
function changeMenuIdx(index) {
  menuIdx.value = index;
}
</script>

<template>
  <el-config-provider :locale="zhCn">
    <el-menu mode="horizontal" class="my-menu" :default-active="menuIdx" @select="changeMenuIdx">
      <el-menu-item index="1">
        <el-icon>
          <Download />
        </el-icon>
        <span>下载中心</span>
      </el-menu-item>
      <el-menu-item index="2">
        <el-icon>
          <SettingIcon />
        </el-icon>
        <span>设置中心</span>
      </el-menu-item>
      <el-menu-item index="3">
        <el-icon>
          <Document />
        </el-icon>
        <span>生成Epub</span>
      </el-menu-item>
    </el-menu>
    <div class="home-div">
      <keep-alive>
        <Home v-if="menuIdx === '1'" />
      </keep-alive>
      <keep-alive>
        <Setting v-if="menuIdx === '2'" />
      </keep-alive>
      <keep-alive>
        <EpubCreator v-if="menuIdx === '3'" />
      </keep-alive>
    </div>
  </el-config-provider>
</template>
<style scoped>
.my-menu {
  height: 50px;
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  z-index: 999;
  border-bottom: 1px solid var(--el-border-color-light);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.04);
}

.home-div {
  height: 100%;
}

.el-menu-item {
  font-size: 14px;
  display: flex;
  align-items: center;
  gap: 6px;
}

.el-menu-item .el-icon {
  font-size: 18px;
}
</style>
