<script setup>
import { ref } from "vue";
import "element-plus/dist/index.css";
import UUIDGenerator from "./tools/UUIDGenerator.vue";
import JSONFormatter from "./tools/JSONFormatter.vue";
import MD5Encoder from "./tools/MD5Encoder.vue";
import Base64Encoder from "./tools/Base64Encoder.vue";
import QRCodeGenerator from "./tools/QRCodeGenerator.vue";
import IPGeoLocation from "./tools/IPGeoLocation.vue";
import RandomString from "./tools/RandomString.vue";
import RegexTester from "./tools/RegexTester.vue";

const tools = [
  { name: "UUID", label: "UUID生成", component: UUIDGenerator },
  { name: "JSON", label: "JSON格式化", component: JSONFormatter },
  { name: "MD5", label: "MD5加密", component: MD5Encoder },
  { name: "Base64", label: "Base64编码", component: Base64Encoder },
  { name: "QRCode", label: "二维码生成", component: QRCodeGenerator },
  { name: "IP", label: "IP归属地查询", component: IPGeoLocation },
  { name: "RString", label: "随机字符串", component: RandomString  },
  { name: "Regex", label: "正则表达式", component: RegexTester },
];
const currentTool = ref("UUID");
</script>

<template>
  <el-container class="app-layout">
    <el-header class="header">
      <div class="header-title">
        <div class="header-logo">
          <img src="./assets/tool-logo.png"></img>
        </div>
        <div class="header-name">捡陋工具箱</div>
      </div>
      <div class="header-nav">
        <a
          href="https://github.com/ingrun/js-tool"
          class="header-link"
          target="_blank"
          title="GitHub"
        >
          <svg class="github-icon" viewBox="0 0 24 24" width="24" height="24">
            <path fill="currentColor" d="M12 .297c-6.63 0-12 5.373-12 12 0 5.303 3.438 9.8 8.205 11.385.6.113.82-.258.82-.577 0-.285-.01-1.04-.015-2.04-3.338.724-4.042-1.61-4.042-1.61C4.422 18.07 3.633 17.7 3.633 17.7c-1.087-.744.084-.729.084-.729 1.205.084 1.838 1.236 1.838 1.236 1.07 1.835 2.809 1.305 3.495.998.108-.776.417-1.305.76-1.605-2.665-.3-5.466-1.332-5.466-5.93 0-1.31.465-2.38 1.235-3.22-.135-.303-.54-1.523.105-3.176 0 0 1.005-.322 3.3 1.23.96-.267 1.98-.399 3-.405 1.02.006 2.04.138 3 .405 2.28-1.552 3.285-1.23 3.285-1.23.645 1.653.24 2.873.12 3.176.765.84 1.23 1.91 1.23 3.22 0 4.61-2.805 5.625-5.475 5.92.42.36.81 1.096.81 2.22 0 1.606-.015 2.896-.015 3.286 0 .315.21.69.825.57C20.565 22.092 24 17.592 24 12.297c0-6.627-5.373-12-12-12"></path>
          </svg>
        </a>
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

<style></style>

<style scoped>
.app-layout {
  min-height: 100vh;
  background: #edeff0;
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
  /* justify-content: space-between; */
  box-shadow: 0 1.5px 6px #0001;
  padding: 40px 24px;
  position: sticky;
  top: 0;
  z-index: 10;
}

.header-title{
  display: flex;
  align-items: center;
  color: #222;
  font-size: 1.2em;
  margin-left: 8px;
}

.header-logo img{
  width: 32px;
  height: 32px;
  margin-right: 12px;
  vertical-align: middle;
}

.header-name {
  color: #4c4c50;
  font-weight: bolder;
  line-height: 1;
}
.content-row {
  flex: 1;
  display: flex;
  flex-direction: row;
  min-height: 0;
  padding: 20px;
}
.sidebar {
  width: 16%;
  background: #fff;
  color: #222;
  display: flex;
  flex-direction: column;
  align-items: stretch;
  padding: 24px 0 0 0;
  border-radius: 8px;
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
  margin-left: auto;
}

.github-icon {
  color: #413743;
  transition: fill 0.2s;
  
}


.main-content {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: stretch;
  margin-left: 20px;
  min-width: 0;
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
