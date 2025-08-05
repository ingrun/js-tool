<template>
  <el-form :inline="true" class="uuid-options">
    <el-form-item label="生成数量">
      <el-input-number v-model="count" :min="1" :max="100" />
    </el-form-item>
    <el-form-item label="目标长度">
      <el-input-number v-model="len" :min="1" :max="100" />
    </el-form-item>
    <el-form-item label="目标字符">
      <el-input v-model="caseString" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="generateCase">生成随机字符串</el-button>
    </el-form-item>
  </el-form>
  <el-table :data="list" border style="margin-top: 12px">
    <el-table-column prop="char" label="字符串" />
    <el-table-column label="操作" width="80">
      <template #default="{ row: { char = ''} }">
        <el-button size="small" @click="copy(char)">复制</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script setup>
import { ref } from "vue";
const count = ref(1);
const len = ref(1);
const caseString = ref("");
let list = ref([]);

const generateCase = () => {
  if (!caseString.value || !count.value) return;
  list.value = Array.from({ length: count.value }, () => ({
    char: generateRandomString(caseString.value),
  }));
};

const generateRandomString = (char) => {
  let result = "";
  for (let i = 0; i < len.value; i++) {
    result += char.charAt(Math.floor(Math.random() * char.length));
  }
  return result;
};

const copy = (text) => {
  navigator.clipboard.writeText(text);
}
</script>

<style scoped></style>
