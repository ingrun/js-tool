<template>
  <div class="tool-module">
    <el-form label-width="0">
      <el-form-item>
        <el-input
          v-model="inputText"
          type="textarea"
          :rows="6"
          placeholder="请输入要生成二维码的内容"
        />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="generateQRCode">生成二维码</el-button>
        <el-button @click="clear">清空</el-button>
      </el-form-item>
    </el-form>
    
    <div v-if="qrCodeData" class="qrcode-container">
      <img :src="qrCodeData" alt="QR Code" class="qrcode-image" />
      <div class="qrcode-actions">
        <el-button @click="downloadQRCode">下载二维码</el-button>
        <el-button @click="copyQRCode">复制二维码</el-button>
      </div>
    </div>
    
    <div v-if="error" class="error-message">
      <el-alert :title="error" type="error" show-icon />
    </div>
  </div>
</template>

<script setup>
defineOptions({
  name: 'QRCode',
  meta: {
    label: '二维码生成'
  }
})
import { ref } from 'vue'
import QRCode from 'qrcode'

const inputText = ref('')
const qrCodeData = ref('')
const error = ref('')

async function generateQRCode() {
  if (!inputText.value) {
    error.value = '请输入内容'
    qrCodeData.value = ''
    return
  }
  
  try {
    // 使用qrcode库本地生成二维码
    const qrCodeUrl = await QRCode.toDataURL(inputText.value)
    qrCodeData.value = qrCodeUrl
    error.value = ''
  } catch (err) {
    error.value = '生成二维码失败: ' + err.message
    qrCodeData.value = ''
  }
}

function clear() {
  inputText.value = ''
  qrCodeData.value = ''
  error.value = ''
}

function downloadQRCode() {
  if (!qrCodeData.value) return
  
  const link = document.createElement('a')
  link.href = qrCodeData.value
  link.download = 'qrcode.png'
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
}

function copyQRCode() {
  if (!qrCodeData.value) return
  
  fetch(qrCodeData.value)
    .then(response => response.blob())
    .then(blob => {
      const item = new ClipboardItem({ [blob.type]: blob })
      navigator.clipboard.write([item])
    })
    .catch(() => {
      // 如果无法复制图片，复制链接
      navigator.clipboard.writeText(qrCodeData.value)
    })
}
</script>

<style scoped>
.tool-module {
  padding: 16px;
}

.qrcode-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.qrcode-image {
  max-width: 200px;
  border: 1px solid #eee;
  padding: 10px;
  border-radius: 8px;
}

.qrcode-actions {
  margin-top: 15px;
  display: flex;
  gap: 10px;
}

.error-message {
  margin-top: 15px;
}
</style>