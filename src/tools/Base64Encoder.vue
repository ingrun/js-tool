<template>
  <div class="tool-module">
    <el-tabs v-model="activeTab">
      <el-tab-pane label="编码" name="encode">
        <el-form label-width="0">
          <el-form-item>
            <el-input
              v-model="encodeInput"
              type="textarea"
              :rows="8"
              placeholder="请输入要编码的内容"
            />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="handleEncode">编码</el-button>
            <el-button @click="encodeInput = ''; encodeOutput = ''">清空</el-button>
          </el-form-item>
        </el-form>
        <el-input
          v-model="encodeOutput"
          type="textarea"
          :rows="6"
          readonly
          class="base64-output"
          placeholder="编码结果"
        />
        <el-form-item style="margin-top: 12px">
          <el-button @click="copyText(encodeOutput)">复制结果</el-button>
        </el-form-item>
      </el-tab-pane>
      <el-tab-pane label="解码" name="decode">
        <el-form label-width="0">
          <el-form-item>
            <el-input
              v-model="decodeInput"
              type="textarea"
              :rows="8"
              placeholder="请输入要解码的Base64内容"
            />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="handleDecode">解码</el-button>
            <el-button @click="decodeInput = ''; decodeOutput = ''">清空</el-button>
          </el-form-item>
        </el-form>
        <el-input
          v-model="decodeOutput"
          type="textarea"
          :rows="6"
          readonly
          class="base64-output"
          placeholder="解码结果"
        />
        <el-alert
          v-if="decodeError"
          :title="decodeError"
          type="error"
          show-icon
          style="margin: 12px 0"
        />
        <el-form-item style="margin-top: 12px">
          <el-button @click="copyText(decodeOutput)">复制结果</el-button>
        </el-form-item>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script setup>
defineOptions({
  name: 'Base64',
  meta: {
    label: 'Base64编码'
  }
})
import { ref } from 'vue'

const activeTab = ref('encode')
const encodeInput = ref('')
const encodeOutput = ref('')
const decodeInput = ref('')
const decodeOutput = ref('')
const decodeError = ref('')

function handleEncode() {
  try {
    encodeOutput.value = btoa(encodeURIComponent(encodeInput.value))
  } catch (err) {
    encodeOutput.value = '编码失败: ' + err.message
  }
}

function handleDecode() {
  try {
    decodeOutput.value = decodeURIComponent(atob(decodeInput.value))
    decodeError.value = ''
  } catch (err) {
    decodeError.value = '解码失败: ' + err.message
    decodeOutput.value = ''
  }
}

function copyText(text) {
  if (text) {
    navigator.clipboard.writeText(text)
  }
}
</script>

<style scoped>
.tool-module {
  padding: 16px;
}

.base64-output :deep(textarea) {
  font-family: monospace;
  background: #f6f8fa;
  border-radius: 4px;
}
</style>