<template>
  <div class="tool-module">
    <el-card class="ip-info-card">
      <template #header>
        <div class="card-header">
          <span>IP地址信息</span>
          <el-button type="primary" @click="getIPInfo" :loading="loading">刷新</el-button>
        </div>
      </template>
      
      <div v-if="ipInfo" class="ip-info-content">
        <el-descriptions :column="1" border>
          <el-descriptions-item label="IP地址">{{ ipInfo.query }}</el-descriptions-item>
          <el-descriptions-item label="国家">{{ ipInfo.country }}</el-descriptions-item>
          <el-descriptions-item label="省份">{{ ipInfo.regionName }}</el-descriptions-item>
          <el-descriptions-item label="城市">{{ ipInfo.city }}</el-descriptions-item>
          <el-descriptions-item label="邮编">{{ ipInfo.zip }}</el-descriptions-item>
          <el-descriptions-item label="时区">{{ ipInfo.timezone }}</el-descriptions-item>
          <el-descriptions-item label="运营商">{{ ipInfo.isp }}</el-descriptions-item>
          <el-descriptions-item label="地理位置">{{ ipInfo.lat }}, {{ ipInfo.lon }}</el-descriptions-item>
        </el-descriptions>
        
        <div class="ip-action-buttons">
          <el-button type="primary" @click="copyIP">复制IP地址</el-button>
          <el-button @click="copyAll">复制全部信息</el-button>
        </div>
      </div>
      
      <div v-else class="ip-info-loading">
        <el-skeleton v-if="loading" :rows="8" animated />
        <el-empty v-else description="获取IP信息失败，请点击刷新重试" />
      </div>
    </el-card>
    
    <el-card class="ip-search-card">
      <template #header>
        <div class="card-header">
          <span>查询指定IP</span>
        </div>
      </template>
      
      <el-form :inline="true">
        <el-form-item label="IP地址">
          <el-input v-model="searchIP" placeholder="请输入IP地址" style="width: 200px" />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="searchIPInfo" :loading="searchLoading">查询</el-button>
        </el-form-item>
      </el-form>
      
      <div v-if="searchResult" class="search-result">
        <el-divider />
        <el-descriptions :column="1" border>
          <el-descriptions-item label="IP地址">{{ searchResult.query }}</el-descriptions-item>
          <el-descriptions-item label="国家">{{ searchResult.country }}</el-descriptions-item>
          <el-descriptions-item label="省份">{{ searchResult.regionName }}</el-descriptions-item>
          <el-descriptions-item label="城市">{{ searchResult.city }}</el-descriptions-item>
          <el-descriptions-item label="邮编">{{ searchResult.zip }}</el-descriptions-item>
          <el-descriptions-item label="时区">{{ searchResult.timezone }}</el-descriptions-item>
          <el-descriptions-item label="运营商">{{ searchResult.isp }}</el-descriptions-item>
          <el-descriptions-item label="地理位置">{{ searchResult.lat }}, {{ searchResult.lon }}</el-descriptions-item>
        </el-descriptions>
      </div>
    </el-card>
    
    <el-alert
      title="提示"
      description="系统会自动尝试多个IP查询服务以提高准确性。如果查询结果不准确，可能是由于网络环境或查询服务数据库更新延迟导致。"
      type="info"
      show-icon
      style="margin-top: 20px"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const loading = ref(false);
const searchLoading = ref(false);
const ipInfo = ref(null);
const searchIP = ref("");
const searchResult = ref(null);

// 获取当前访问者IP信息 - 使用多个API服务
async function getIPInfo() {
  try {
    loading.value = true;
    
    // 使用多个API作为备选方案，包括国内服务
    const apis = [
      // 国内服务优先尝试
      () => fetch("https://api.ipify.org?format=json").then(res => res.json())
        .then(data => fetch(`https://ipapi.co/${data.ip}/json/`))
        .then(res => res.json()),
      
      // 备用方案
      () => fetch("https://api.ip.sb/geoip").then(res => res.json()),
      
      // 另一个备用方案
      () => fetch("https://ipinfo.io/json").then(res => res.json())
    ];
    let data = null;
    let lastError = null;
    
    // 按顺序尝试API
    for (const api of apis) {
      try {
        const result = await api();
        // 检查响应是否有效
        if ((result.country && result.ip) || (result.country_name && result.ip) || (result.loc && result.ip)) {
          data = result;
          break;
        }
      } catch (error) {
        lastError = error;
        continue;
      }
    }
    
    if (data) {
      // 标准化数据格式
      ipInfo.value = normalizeIPData(data);
    } else {
      throw lastError || new Error("所有IP查询服务均不可用");
    }
  } catch (error) {
    console.error("获取IP信息出错:", error);
    ipInfo.value = null;
  } finally {
    loading.value = false;
  }
}

// 查询指定IP信息
async function searchIPInfo() {
  if (!searchIP.value) {
    return;
  }
  
  try {
    searchLoading.value = true;
    searchResult.value = null;
    
    // 使用 ipapi.co 查询指定IP
    const response = await fetch(`https://ipapi.co/${searchIP.value}/json/`);
    const data = await response.json();
    
    if (data.error) {
      throw new Error(data.reason || "查询IP信息失败");
    } else {
      searchResult.value = normalizeIPData(data);
    }
  } catch (error) {
    console.error("查询IP信息出错:", error);
    searchResult.value = null;
    alert("查询失败: " + error.message);
  } finally {
    searchLoading.value = false;
  }
}

// 标准化不同API返回的数据格式
function normalizeIPData(data) {
  // 处理 ipapi.co 的数据格式
  if (data.ip && data.country_name) {
    return {
      query: data.ip,
      country: data.country_name,
      countryCode: data.country_code,
      region: data.region_code || "",
      regionName: data.region || "",
      city: data.city || "",
      zip: data.postal || "",
      lat: data.latitude || "",
      lon: data.longitude || "",
      timezone: data.timezone || "",
      isp: data.org || data.asn || "",
      status: "success"
    };
  }
  
  // 处理 ip.sb 的数据格式
  if (data.ip && data.country) {
    return {
      query: data.ip,
      country: data.country,
      countryCode: data.country_code,
      region: data.region_code || "",
      regionName: data.region || "",
      city: data.city || "",
      zip: data.postal_code || "",
      lat: data.latitude || "",
      lon: data.longitude || "",
      timezone: data.timezone || "",
      isp: data.organization || data.asn || "",
      status: "success"
    };
  }
  
  // 处理 ipinfo.io 的数据格式
  if (data.ip && data.loc) {
    const [lat, lon] = data.loc.split(",");
    return {
      query: data.ip,
      country: data.country,
      countryCode: data.country,
      region: data.region || "",
      regionName: data.region || "",
      city: data.city || "",
      zip: data.postal || "",
      lat: lat || "",
      lon: lon || "",
      timezone: data.timezone || "",
      isp: data.org || data.asn || "",
      status: "success"
    };
  }
  
  // 处理 ipify 获取IP后查询的结果
  if (data.query) {
    return {
      query: data.query,
      country: data.country || "",
      regionName: data.regionName || "",
      city: data.city || "",
      zip: data.zip || "",
      lat: data.lat || "",
      lon: data.lon || "",
      timezone: data.timezone || "",
      isp: data.isp || data.org || "",
      status: "success"
    };
  }
  
  return data;
}

// 复制IP地址
function copyIP() {
  if (ipInfo.value) {
    navigator.clipboard.writeText(ipInfo.value.query);
  }
}

// 复制全部信息
function copyAll() {
  if (ipInfo.value) {
    const info = `IP地址: ${ipInfo.value.query}
国家: ${ipInfo.value.country}
省份: ${ipInfo.value.regionName}
城市: ${ipInfo.value.city}
邮编: ${ipInfo.value.zip}
时区: ${ipInfo.value.timezone}
运营商: ${ipInfo.value.isp}
地理位置: ${ipInfo.value.lat}, ${ipInfo.value.lon}`;
    navigator.clipboard.writeText(info);
  }
}

// 初始化获取当前IP信息
onMounted(() => {
  getIPInfo();
});
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

.ip-info-card, .ip-search-card {
  margin-bottom: 20px;
}

.ip-info-content {
  margin-top: 16px;
}

.ip-action-buttons {
  margin-top: 20px;
  text-align: center;
}

.ip-info-loading {
  min-height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.search-result {
  margin-top: 20px;
}
</style>