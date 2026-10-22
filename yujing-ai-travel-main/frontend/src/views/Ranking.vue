<template>
  <div class="page-wrap">
    <NavBar />
    <div class="container">
      <div class="page-head">
        <span class="sec-dot"></span>
        <h2 class="page-title">景点推荐</h2>
      </div>
      <div class="filter-bar">
        <el-radio-group v-model="activeTab" @change="loadList">
          <el-radio-button label="attraction">景点榜</el-radio-button>
          <el-radio-button label="food">美食榜</el-radio-button>
          <el-radio-button label="activity">快闪活动榜</el-radio-button>
        </el-radio-group>
        <el-select v-model="region" placeholder="区域" @change="loadList" style="width:140px">
          <el-option label="全部" value="全部" />
          <el-option v-for="r in regions" :key="r" :label="r" :value="r" />
        </el-select>
        <el-select v-model="sort" placeholder="排序" @change="loadList" style="width:140px">
          <el-option label="热度优先" value="热度优先" />
          <el-option label="评分优先" value="评分优先" />
          <el-option label="最新发布" value="最新发布" />
        </el-select>
        <el-button type="danger" @click="goGenerate" v-if="selectedStore.spotIds.length">
          生成攻略 ({{ selectedStore.spotIds.length }})
        </el-button>
      </div>

      <el-row :gutter="16">
        <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="spot in spots" :key="spot.id">
          <SpotCard :spot="spot" @select="addToSelected" @favorite="toggleFavorite" />
        </el-col>
      </el-row>

      <el-empty v-if="!spots.length" description="暂无数据" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import NavBar from '../components/NavBar.vue'
import SpotCard from '../components/SpotCard.vue'
import { getSpotList, addFavorite } from '../api'
import { useSelectedStore } from '../store/selected'
import { useUserStore } from '../store/user'

const router = useRouter()
const selectedStore = useSelectedStore()
const userStore = useUserStore()

const activeTab = ref('attraction')
const region = ref('全部')
const sort = ref('热度优先')
const spots = ref([])

const regions = ['渝中区', '江北区', '南岸区', '沙坪坝区', '九龙坡区', '渝北区', '巴南区', '北碚区', '大渡口区', '武隆区', '大足区', '巫山县', '酉阳县', '秀山县', '丰都县', '涪陵区', '万州区']

onMounted(loadList)

async function loadList() {
  try {
    spots.value = await getSpotList({ type: activeTab.value, region: region.value, sort: sort.value })
  } catch (e) {}
}

const addToSelected = (spot) => {
  if (!userStore.token) {
    ElMessage.warning('请先登录')
    router.push('/login')
    return
  }
  selectedStore.addSpot(spot.id, spot.name)
  ElMessage.success(`已加入行程：${spot.name}`)
}

const toggleFavorite = async (spot) => {
  if (!userStore.token) {
    ElMessage.warning('请先登录')
    router.push('/login')
    return
  }
  try {
    await addFavorite(spot.id)
    ElMessage.success('已收藏')
  } catch (e) {}
}

const goGenerate = () => {
  router.push('/guide/form')
}
</script>

<style scoped>
.page-wrap { background: #faf7f0; min-height: 100vh; padding-bottom: 40px; }
.container { max-width: 1200px; margin: 0 auto; padding: 28px 20px; }
.page-head { display: flex; align-items: center; gap: 10px; margin-bottom: 22px; }
.sec-dot { width: 4px; height: 22px; background: linear-gradient(180deg, #c45a3a, #d97757); border-radius: 2px; }
.page-title { font-size: 22px; font-weight: 600; color: #2c2c2c; margin: 0; }
.filter-bar {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 24px;
  background: #fff;
  padding: 16px;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0,0,0,0.05);
  flex-wrap: wrap;
}
</style>
