<template>
  <div class="tool-module">
    <el-form label-width="0">
      <el-form-item>
        <el-input
          v-model="inputText"
          type="textarea"
          :rows="6"
          placeholder="请输入要处理的内容"
        />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleEncode">UrlEncode编码</el-button>
        <el-button type="success" @click="handleDecode">UrlDecode解码</el-button>
        <el-button type="warning" @click="clearInput">清空输入框</el-button>
        <el-button @click="copyResult">复制网址</el-button>
      </el-form-item>
    </el-form>
    <el-input
      v-model="outputText"
      type="textarea"
      :rows="8"
      readonly
      placeholder=""
    />
    <el-alert
      v-if="errorMessage"
      :title="errorMessage"
      type="error"
      show-icon
      style="margin-top: 12px"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { ElNotification } from 'element-plus'

const inputText = ref('')
const outputText = ref('')
const errorMessage = ref('')

defineOptions({
  name: 'UrlEncode',
  meta: {
    label: 'URL编码解码'
  }
})

function handleEncode() {
  try {
    outputText.value = encodeURIComponent(inputText.value)
    errorMessage.value = ''
  } catch (err) {
    errorMessage.value = '编码失败: ' + err.message
    outputText.value = ''
  }
}

function handleDecode() {
  try {
    outputText.value = decodeURIComponent(inputText.value)
    errorMessage.value = ''
  } catch (err) {
    errorMessage.value = '解码失败: ' + err.message
    outputText.value = ''
  }
}

function clearInput() {
  inputText.value = ''
  outputText.value = ''
  errorMessage.value = ''
}

function copyResult() {
  if (outputText.value) {
    navigator.clipboard.writeText(outputText.value)
    ElNotification({
        title: '提示',
        message: '复制成功',
        type: 'success',
    })
  }
}
</script>

<style scoped>
.tool-module {
  padding: 20px;
}
</style>