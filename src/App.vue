/* el-menu-item 隔离样式 */
.el-menu-vertical-demo .el-menu-item {
  margin-bottom: 6px;
  border-radius: 6px;
  transition: background 0.2s, color 0.2s;
  position: relative;
}
.el-menu-vertical-demo .el-menu-item:not(:last-child)::after {
  content: '';
  display: block;
  position: absolute;
  left: 16px;
  right: 16px;
  bottom: -3px;
  height: 1px;
  background: #ececec;
  opacity: 0.8;
}
.el-menu-vertical-demo .el-menu-item.is-active,
.el-menu-vertical-demo .el-menu-item:hover {
  background: #e6f0fa !important;
  color: #3a5ccc !important;
}
<script setup>
import { ref } from "vue";
import ElementPlus from "element-plus";
import "element-plus/dist/index.css";
import UUIDGenerator from "./tools/UUIDGenerator.vue";
import JSONFormatter from "./tools/JSONFormatter.vue";
import MD5Encoder from "./tools/MD5Encoder.vue";

const tools = [
  { name: "UUID", label: "UUID生成", component: UUIDGenerator },
  { name: "JSON", label: "JSON格式化", component: JSONFormatter },
  { name: "MD5", label: "MD5加密", component: MD5Encoder },
];
const currentTool = ref("UUID");
</script>

<template>
  <el-container class="app-layout">
    <el-header class="header">
      <div class="header-logo">LOGO</div>
      <div class="header-nav">
        <a href="#" class="header-link" disabled>关于作者</a>
        <a href="https://github.com/" class="header-link" target="_blank"
          >开源地址</a
        >
        <a href="#" class="header-link">日/夜间模式</a>
        <a href="#" class="header-link">中/English</a>
      </div>
    </el-header>
    <el-container class="content-row">
      <el-aside class="sidebar" width="210px">
        <el-menu
          :default-active="currentTool"
          class="el-menu-vertical-demo"
          @select="(key) => (currentTool = key)"
        >
          <el-menu-item
            v-for="tool in tools"
            :key="tool.name"
            :index="tool.name"
          >
            {{ tool.label }}
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-main class="main-content">
        <div class="tool-card tool-card-large">
          <component
            :is="tools.find((t) => t.name === currentTool).component"
          />
        </div>
      </el-main>
    </el-container>
  </el-container>
</template>

<style>
body {
  background: #efefef;
}

#app {
  padding: 0;
}
</style>

<style scoped>
.app-layout {
  min-height: 100vh;
  background: #f7f9fa;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  overflow-x: hidden;
}
.header {
  width: 100%;
  height: 56px;
  background: #fff;
  display: flex;
  align-items: center;
  justify-content: space-between;
  box-shadow: 0 1.5px 6px #0001;
  padding: 0 40px 0 0;
  position: sticky;
  top: 0;
  z-index: 10;
}
.header-logo {
  color: #3a5ccc;
}
.content-row {
  flex: 1;
  display: flex;
  flex-direction: row;
  min-height: 0;
  /* 移除固定高度，避免撑出滚动条 */
}
.sidebar {
  width: 210px;
  background: #f6f7f9;
  color: #222;
  border-right: 1px solid #ececec;
  display: flex;
  flex-direction: column;
  align-items: stretch;
  padding: 24px 0 0 0;
  min-height: 100%;
}
/* 让el-menu宽度100%填满sidebar */
.el-menu-vertical-demo {
  width: 100%;
  border-right: none;
  background: transparent;
}
.sidebar nav ul {
  list-style: none;
  padding: 0 0 0 0;
  margin: 0;
  width: 100%;
}
.sidebar nav li {
  padding: 10px 28px 10px 28px;
  cursor: pointer;
  border-radius: 6px;
  margin-bottom: 4px;
  font-size: 1.05em;
  transition: background 0.2s, color 0.2s;
  width: 100%;
  box-sizing: border-box;
}
.sidebar nav li.active,
.sidebar nav li:hover {
  background: #e6f0fa;
  color: #3a5ccc;
}
.header-nav {
  display: flex;
  gap: 24px;
  align-items: center;
}
.header-link {
  color: #444;
  text-decoration: none;
  font-size: 1em;
  padding: 0 4px;
  transition: color 0.2s;
}
.header-link:hover {
  color: #3a5ccc;
}
.main-content {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: stretch;
  padding: 40px 0 0 0;
  background: #f7f9fa;
  min-width: 0;
}
.tool-card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 1.5px 8px #0001;
  padding: 28px 32px 32px 32px;
  width: 100%;
  min-width: 0;
  height: 100%;
  margin: 0;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}
.tool-card-large {
  width: 100%;
  min-width: 0;
  height: 100%;
}
</style>
