<template>
  <div class="tool-module">
    <el-row :gutter="20">
      <el-col :span="24">
        <el-card class="regex-tester-card">
          <template #header>
            <div class="card-header">
              <span>正则表达式测试器</span>
            </div>
          </template>
          
          <el-form :model="form" label-position="top">
            <el-row :gutter="20">
              <el-col :span="24">
                <el-form-item label="正则表达式">
                  <el-input 
                    v-model="form.pattern" 
                    placeholder="输入正则表达式，例如: \b\w+\b" 
                    @input="testRegex"
                  >
                    <template #prepend>/</template>
                    <template #append>/</template>
                  </el-input>
                </el-form-item>
              </el-col>
            </el-row>
            
            <el-row :gutter="20">
              <el-col :span="12">
                <el-form-item label="修饰符">
                  <el-checkbox-group v-model="form.flags" @change="testRegex">
                    <el-checkbox label="g">全局匹配 (g)</el-checkbox>
                    <el-checkbox label="i">忽略大小写 (i)</el-checkbox>
                    <el-checkbox label="m">多行匹配 (m)</el-checkbox>
                  </el-checkbox-group>
                </el-form-item>
              </el-col>
              
              <el-col :span="12">
                <el-form-item label="测试文本">
                  <el-input 
                    v-model="form.text" 
                    type="textarea" 
                    :rows="6" 
                    placeholder="在此输入要测试的文本内容"
                    @input="testRegex"
                  />
                </el-form-item>
              </el-col>
            </el-row>
          </el-form>
          
          <div class="result-section">
            <el-tabs v-model="activeResultTab">
              <el-tab-pane label="匹配结果" name="matches">
                <div v-if="matches.length > 0">
                  <el-table :data="matches" style="width: 100%" stripe>
                    <el-table-column prop="index" label="索引" width="80" />
                    <el-table-column prop="match" label="匹配内容" />
                    <el-table-column v-if="form.flags.includes('g')" prop="groups" label="捕获组" />
                  </el-table>
                </div>
                <el-empty v-else description="没有匹配结果" />
              </el-tab-pane>
              
              <el-tab-pane label="替换结果" name="replace">
                <el-form :model="replaceForm" label-position="left" label-width="80px">
                  <el-form-item label="替换为">
                    <el-input 
                      v-model="replaceForm.replacement" 
                      placeholder="输入替换内容"
                      @input="testRegex"
                    />
                  </el-form-item>
                  <el-form-item label="结果">
                    <el-input 
                      type="textarea" 
                      :rows="4" 
                      v-model="replaceResult" 
                      readonly
                    />
                  </el-form-item>
                </el-form>
              </el-tab-pane>
            </el-tabs>
            
            <div class="regex-info" v-if="regexInfo">
              <el-alert 
                :title="regexInfo.isValid ? '正则表达式有效' : '正则表达式无效'" 
                :type="regexInfo.isValid ? 'success' : 'error'" 
                show-icon
              />
              <div v-if="regexInfo.isValid" class="regex-stats">
                <p>匹配数量: {{ matches.length }}</p>
              </div>
            </div>
            
            <div class="regex-rules">
              <el-divider />
              <h3>正则表达式语法规则</h3>
              <el-row :gutter="20">
                <el-col :span="12">
                  <h4>常用字符类</h4>
                  <el-descriptions :column="1" size="small" border>
                    <el-descriptions-item label=".">匹配除换行符外的任意字符</el-descriptions-item>
                    <el-descriptions-item label="\d">匹配数字，等价于[0-9]</el-descriptions-item>
                    <el-descriptions-item label="\D">匹配非数字字符</el-descriptions-item>
                    <el-descriptions-item label="\w">匹配字母、数字或下划线</el-descriptions-item>
                    <el-descriptions-item label="\W">匹配非字母、数字或下划线</el-descriptions-item>
                    <el-descriptions-item label="\s">匹配空白字符</el-descriptions-item>
                    <el-descriptions-item label="\S">匹配非空白字符</el-descriptions-item>
                  </el-descriptions>
                </el-col>
                <el-col :span="12">
                  <h4>量词</h4>
                  <el-descriptions :column="1" size="small" border>
                    <el-descriptions-item label="*">匹配前面的子表达式零次或多次</el-descriptions-item>
                    <el-descriptions-item label="+">匹配前面的子表达式一次或多次</el-descriptions-item>
                    <el-descriptions-item label="?">匹配前面的子表达式零次或一次</el-descriptions-item>
                    <el-descriptions-item label="{n}">匹配确定的n次</el-descriptions-item>
                    <el-descriptions-item label="{n,}">至少匹配n次</el-descriptions-item>
                    <el-descriptions-item label="{n,m}">最少匹配n次，最多匹配m次</el-descriptions-item>
                  </el-descriptions>
                </el-col>
              </el-row>
              <el-row :gutter="20" style="margin-top: 20px;">
                <el-col :span="12">
                  <h4>定位符</h4>
                  <el-descriptions :column="1" size="small" border>
                    <el-descriptions-item label="^">匹配输入字符串的开始位置</el-descriptions-item>
                    <el-descriptions-item label="$">匹配输入字符串的结束位置</el-descriptions-item>
                    <el-descriptions-item label="\b">匹配一个单词边界</el-descriptions-item>
                    <el-descriptions-item label="\B">匹配非单词边界</el-descriptions-item>
                  </el-descriptions>
                </el-col>
                <el-col :span="12">
                  <h4>特殊字符</h4>
                  <el-descriptions :column="1" size="small" border>
                    <el-descriptions-item label="|">指明两项之间的一个选择</el-descriptions-item>
                    <el-descriptions-item label="[]">匹配方括号内的任意字符</el-descriptions-item>
                    <el-descriptions-item label="()">标记一个子表达式的开始和结束位置</el-descriptions-item>
                    <el-descriptions-item label="\">转义下一个字符</el-descriptions-item>
                  </el-descriptions>
                </el-col>
              </el-row>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>
    
    <el-row :gutter="20" style="margin-top: 20px;">
      <el-col :span="24">
        <el-card class="regex-cheatsheet-card">
          <template #header>
            <div class="card-header">
              <span>常用正则表达式速查表</span>
            </div>
          </template>
          
          <el-table :data="cheatsheetData" style="width: 100%" stripe>
            <el-table-column prop="name" label="用途" width="200" />
            <el-table-column prop="pattern" label="正则表达式">
              <template #default="scope">
                <code>{{ scope.row.pattern }}</code>
              </template>
            </el-table-column>
            <el-table-column prop="description" label="说明" />
            <el-table-column label="操作" width="120">
              <template #default="scope">
                <el-button size="small" @click="useRegex(scope.row.pattern)">使用</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from "vue";

const activeResultTab = ref("matches");

const form = reactive({
  pattern: "",
  flags: ["g", 'm'],
  text: ""
});

const replaceForm = reactive({
  replacement: ""
});

const regexInfo = ref(null);
const matches = ref([]);

const replaceResult = computed(() => {
  if (!form.pattern || !form.text || !regexInfo.value?.isValid) {
    return "";
  }
  
  try {
    const regex = new RegExp(form.pattern, form.flags.join(""));
    return form.text.replace(regex, replaceForm.replacement);
  } catch (e) {
    return "无效的正则表达式";
  }
});

const cheatsheetData = [
  { name: "电子邮件", pattern: "\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}\\b", description: "匹配常见的电子邮件地址格式" },
  { name: "手机号码", pattern: "^1[3-9]\\d{9}$", description: "匹配中国大陆11位手机号码" },
  { name: "URL链接", pattern: "https?:\\/\\/(www\\.)?[-a-zA-Z0-9@:%._\\+~#=]{1,256}\\.[a-zA-Z0-9()]{1,6}\\b([-a-zA-Z0-9()@:%_\\+.~#?&//=]*)", description: "匹配HTTP或HTTPS链接" },
  { name: "IP地址", pattern: "\\b(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\b", description: "匹配有效的IPv4地址" },
  { name: "日期格式", pattern: "^\\d{4}-\\d{2}-\\d{2}$", description: "匹配YYYY-MM-DD日期格式" },
  { name: "时间格式", pattern: "^([01]?[0-9]|2[0-3]):[0-5][0-9](:[0-5][0-9])?$", description: "匹配HH:MM或HH:MM:SS时间格式" },
  { name: "邮政编码", pattern: "^\\d{6}$", description: "匹配6位数字邮政编码" },
  { name: "身份证号", pattern: "^\\d{17}[\\dXx]$", description: "匹配18位身份证号码" },
  { name: "纯数字", pattern: "^\\d+$", description: "匹配纯数字字符串" },
  { name: "汉字", pattern: "^[\\u4e00-\\u9fa5]+$", description: "匹配纯中文字符串" },
  { name: "英文单词", pattern: "\\b[A-Za-z]+\\b", description: "匹配英文单词" },
  { name: "去除首尾空格", pattern: "(^\\s+)|(\\s+$)", description: "匹配字符串首尾的空格" }
];

function testRegex() {
  matches.value = [];
  regexInfo.value = null;
  
  if (!form.pattern) {
    return;
  }
  
  try {
    const regex = new RegExp(form.pattern, form.flags.join(""));
    regexInfo.value = { isValid: true };
    
    if (form.text) {
      if (form.flags.includes("g")) {
        const allMatches = [...form.text.matchAll(regex)];
        matches.value = allMatches.map((m, index) => ({
          index: m.index,
          match: m[0],
          groups: m.slice(1).filter(g => g !== undefined).join(", ") || "-"
        }));
      } else {
        const match = form.text.match(regex);
        if (match) {
          matches.value = [{
            index: match.index,
            match: match[0]
          }];
        }
      }
    }
  } catch (e) {
    regexInfo.value = { isValid: false, error: e.message };
  }
}

function useRegex(pattern) {
  form.pattern = pattern;
  testRegex();
}
</script>

<style scoped>
.tool-module {
  padding: 16px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.regex-tester-card, .regex-cheatsheet-card {
  margin-bottom: 20px;
}

.result-section {
  margin-top: 20px;
}

.regex-info {
  margin-top: 15px;
}

.regex-stats p {
  margin: 5px 0;
}
</style>