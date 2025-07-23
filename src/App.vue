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
  padding: 40px 24px;
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
  width: 360px;
  background: #fff;
  color: #222;
  border-right: 1px solid #ececec;
  display: flex;
  flex-direction: column;
  align-items: stretch;
  padding: 24px 0 0 0;
  border-top-right-radius: 40px;
  border-bottom-right-radius: 40px;
  margin: 40px 20px 0 20px;
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
  margin: 40px 20px 0px 20px;
  min-width: 0;
  border-top-left-radius: 40px;
  border-bottom-left-radius: 40px;
  padding: 0;
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
