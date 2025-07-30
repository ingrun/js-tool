<template>
  <div class="tool-module">
    <el-form label-width="0">
      <el-form-item>
        <el-input
          v-model="input"
          type="textarea"
          :rows="14"
          placeholder='{"key":"value"}'
        />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="formatJSON">格式化</el-button>
        <el-button @click="clear">清空</el-button>
      </el-form-item>
    </el-form>
    <el-alert
      v-if="error"
      :title="error"
      type="error"
      show-icon
      style="margin-bottom: 8px"
    />
    <el-input
      type="textarea"
      :rows="14"
      v-model="output"
      readonly
      class="json-output"
    />
  </div>
</template>

<script setup>
import { ref } from "vue";
const input = ref("");
const output = ref("");
const error = ref("");
function formatJSON() {
  try {
    output.value = JSON.stringify(JSON.parse(input.value), null, 2);
    error.value = "";
  } catch (e) {
    error.value = "JSON格式错误";
    output.value = "";
  }
}

const clear = () => {
  input.value = "";
  error.value = "";
};

</script>


<style scoped>
.tool-module {
  padding: 16px;
}
.json-output :deep(textarea) {
  background: #f6f8fa;
  border-radius: 4px;
  font-family: monospace;
}
</style>
