<template>
  <div class="tool-module">
    <el-form :inline="true" class="uuid-options">
      <el-form-item label="生成数量">
        <el-input-number v-model="count" :min="1" :max="100" size="small" style="width:90px" />
      </el-form-item>
      <el-form-item label="字母">
        <el-select v-model="caseType" size="small" style="width:90px">
          <el-option label="原样" value="original" />
          <el-option label="大写" value="upper" />
          <el-option label="小写" value="lower" />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-checkbox v-model="withDash">带横线</el-checkbox>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="generateUUIDs" size="small">生成UUID</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="uuids.map(u=>({uuid:u}))" border size="small" style="margin-top:12px;">
      <el-table-column prop="uuid" label="UUID" />
      <el-table-column label="操作" width="80">
        <template #default="scope">
          <el-button size="small" @click="copy(scope.row.uuid)">复制</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const count = ref(5)
const caseType = ref('original') // original, upper, lower
const withDash = ref(true)
const uuids = ref([])

function createUUID(dash = true) {
  let u = ([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, c =>
    (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)
  )
  if (!dash) u = u.replace(/-/g, '')
  return u
}

function formatCase(u) {
  if (caseType.value === 'upper') return u.toUpperCase()
  if (caseType.value === 'lower') return u.toLowerCase()
  return u
}

function generateUUIDs() {
  uuids.value = Array.from({length: count.value}, () => formatCase(createUUID(withDash.value)))
}

function copy(text) {
  navigator.clipboard.writeText(text)
}

// 初始化
generateUUIDs()
</script>

<style scoped>
.tool-module {
  padding: 16px;
}
.uuid-options {
  margin-bottom: 8px;
  display: flex;
  flex-wrap: wrap;
  gap: 8px 16px;
  align-items: center;
}
.uuid-options .el-form-item {
  margin-bottom: 0 !important;
}
</style>
