<template>
  <el-form :inline="true" class="uuid-options">
    <el-form-item label="生成数量">
      <el-input-number v-model="count" :min="1" :max="100" @change="generateCase" />
    </el-form-item>
    <el-form-item label="目标长度">
      <el-input-number v-model="len" :min="1" :max="100" @change="generateCase" />
    </el-form-item>
  </el-form>
  
  <el-form>
    <el-form-item label="字符类型">
      <el-checkbox-group v-model="selectedCharTypes" @change="updateCharSetFromTypes">
        <el-checkbox label="digits">数字 (0-9)</el-checkbox>
        <el-checkbox label="lowercase">小写字母 (a-z)</el-checkbox>
        <el-checkbox label="uppercase">大写字母 (A-Z)</el-checkbox>
        <el-checkbox label="special">特殊字符</el-checkbox>
      </el-checkbox-group>
    </el-form-item>
    
    <el-form-item label="字符集">
      <el-input v-model="caseString" @input="onCharSetInput" placeholder="在此输入或修改字符集" />
    </el-form-item>
  </el-form>
  
  <div class="result-section">
    <el-input
      v-model="resultText"
      type="textarea"
      :rows="20"
      placeholder="生成的随机字符串将显示在这里，您可以直接编辑"
    ></el-input>
    <div class="result-actions">
      <el-button type="primary" @click="copy(resultText)">复制全部</el-button>
      <el-button @click="clearResults">清空结果</el-button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const count = ref(10);
const len = ref(20);
const selectedCharTypes = ref(["digits", "lowercase", "uppercase"]);
const caseString = ref("0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ");
const resultText = ref("");

const charSets = {
  digits: "0123456789",
  lowercase: "abcdefghijklmnopqrstuvwxyz",
  uppercase: "ABCDEFGHIJKLMNOPQRSTUVWXYZ",
  special: ".,:;-_+*/?=()[]{}!@#$%^&|~`\"'\\"
};

let list = ref([]);

const updateCharSetFromTypes = () => {
  let chars = "";
  selectedCharTypes.value.forEach(type => {
    chars += charSets[type] || "";
  });
  caseString.value = chars;
  generateCase();
};

const onCharSetInput = () => {
  // 当用户手动输入字符集时，需要更新列表并重新生成
  generateCase();
};

const generateCase = () => {
  if (!caseString.value || !count.value) return;
  list.value = Array.from({ length: count.value }, () => ({
    char: generateRandomString(caseString.value),
  }));
  
  // 将结果转换为文本格式
  resultText.value = list.value.map(item => item.char).join('\n');
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
};

const clearResults = () => {
  resultText.value = "";
  list.value = [];
};

// 初始化
onMounted(() => {
  updateCharSetFromTypes();
});
</script>

<style scoped>
.result-section {
  margin-top: 20px;
}

.result-actions {
  margin-top: 10px;
  text-align: center;
}
</style>