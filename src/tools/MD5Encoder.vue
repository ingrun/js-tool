<template>
  <div class="tool-module">
    <el-form label-width="0">
      <el-form-item>
        <el-input v-model="input" placeholder="请输入内容" clearable />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="encodeMD5">加密</el-button>
      </el-form-item>
    </el-form>
    <el-input v-if="output" v-model="output" readonly class="md5-output" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import md5 from 'blueimp-md5'
const input = ref('')
const output = ref('')
function encodeMD5() {
  output.value = input.value ? md5(input.value) : ''
}
</script>

<style scoped>
  .tool-module { padding: 16px; }
  .md5-output :deep(input) {
    font-family: monospace;
    margin-top: 8px;
    word-break: break-all;
  }
</style>
