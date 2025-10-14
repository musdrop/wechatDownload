<template>
    <el-container style="height: 100%">
        <el-main>
            <div class="guide-container">
                <div class="guide-card">
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
                            <span>源码地址：<a target="_blank"
                                    href="https://github.com/musdrop/wechatDownload">wechatDownload</a></span>
                        </div>
                    </div>
                    <el-divider />
                    <div class="guide-section">
                        <h3>
                            <el-icon>
                                <Document />
                            </el-icon> 使用说明
                        </h3>
                        <ul>
                            <li>单篇下载：输入链接可直接使用</li>
                            <li>批量下载：可以批量下载某公众号的文章</li>
                            <li>监控下载：可以手动打开所有需要下载的文章，程序自动识别进行下载</li>
                            <li>批量下载和监控下载需要代理，使用代理需要安装证书，证书路径请前往设置中心查看</li>
                            <li>证书安装完成请重启软件</li>
                            <li>通过html文件生成EPUB电子书</li>
                        </ul>
                    </div>
                    <el-divider />
                    <div class="guide-section">
                        <h3>
                            <el-icon>
                                <Setting />
                            </el-icon> 功能特性
                        </h3>
                        <ul>
                            <li>支持下载为 HTML、Markdown、PDF 格式</li>
                            <li>支持下载文章中的图片和音频到本地</li>
                            <li>支持下载文章评论及评论回复</li>
                            <li>支持按公众号自动分类保存</li>
                            <li>支持自定义时间范围批量下载</li>
                            <li>支持保存到 MySQL 数据库</li>
                            <li>支持生成 EPUB 电子书</li>
                        </ul>
                    </div>
                    <el-divider />
                    <div class="tips-section">
                        <h3>
                            <el-icon>
                                <InfoFilled />
                            </el-icon> 常见问题
                        </h3>
                        <el-collapse>
                            <el-collapse-item title="如何安装证书？" name="1">
                                <p>Windows：以管理员身份打开程序后可直接点击设置中心的“安装证书”自动安装，也可以点击“打开证书路径”，在打开的文件夹中双击证书文件rootCA.crt，按提示安装即可
                                </p>
                                <p>Mac：打开"钥匙串访问"，导入证书文件并设置为信任</p>
                                <p>Linux：使用系统证书管理工具导入证书</p>
                            </el-collapse-item>
                            <el-collapse-item title="批量下载没有反应？" name="2">
                                <p>1. 确认已安装证书并重启软件</p>
                                <p>2. 确认在微信客户端中打开了公众号文章</p>
                                <p>3. 检查设置中的代理配置是否正确</p>
                            </el-collapse-item>
                            <el-collapse-item title="如何使用生成 EPUB 电子书？" name="3">
                                <p>1. 使用批量下载功能将公众号文章保存为 HTML 文件到本地</p>
                                <p>2. 切换到"生成 Epub"页面，填写文件名和包含 HTML 文件的文件夹路径</p>
                                <p>3. 可选：选择封面图片，支持 jpg、png 格式</p>
                                <p>4. 点击"生成 EPUB"按钮开始创建电子书</p>
                                <p>5. 生成的 EPUB 文件会保存在指定的输出目录中</p>
                            </el-collapse-item>
                            <el-collapse-item title="如何选择下载格式？" name="4">
                                <p>在"设置中心"页面可以选择需要的下载格式：</p>
                                <p>- HTML：保留原始样式，适合在浏览器查看</p>
                                <p>- Markdown：纯文本格式，方便编辑</p>
                                <p>- PDF：固定格式，适合打印和分享</p>
                            </el-collapse-item>
                        </el-collapse>
                    </div>
                    <el-divider />
                    <div class="warning-section">
                        <el-alert type="warning" :closable="false" show-icon>
                            <template #title>
                                如遇网络问题，请尝试重启软件
                            </template>
                        </el-alert>
                    </div>
                </div>
            </div>
        </el-main>
    </el-container>
</template>

<script setup lang="ts">
import { CircleCheck, Link, Document, Setting, InfoFilled } from '@element-plus/icons-vue';
</script>

<style scoped>
.el-main {
    --el-main-padding: 24px;
    background: #f5f7fa;
}

/* 指南页面样式 */
.guide-container {
    display: flex;
    justify-content: center;
    align-items: flex-start;
    min-height: 100%;
}

.guide-card {
    background: #fff;
    border-radius: 8px;
    padding: 32px;
    max-width: 800px;
    width: 100%;
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
}

.guide-card h2 {
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
    margin: 20px 0;
}

.tips-section h3 {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 18px;
    font-weight: 500;
    color: #303133;
    margin: 0 0 12px 0;
}

.el-collapse {
    border: none;
}

.el-collapse-item :deep(.el-collapse-item__header) {
    font-size: 15px;
    color: #606266;
    background-color: #f5f7fa;
    padding: 12px 16px;
    border-radius: 4px;
    margin-bottom: 8px;
}

.el-collapse-item :deep(.el-collapse-item__wrap) {
    border: none;
    background-color: transparent;
}

.el-collapse-item :deep(.el-collapse-item__content) {
    padding: 12px 16px;
    color: #606266;
    line-height: 1.8;
}

.el-collapse-item p {
    margin: 8px 0;
}

.warning-section {
    margin-top: 20px;
}
</style>
