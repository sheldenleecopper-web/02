<template>
  <div class="page-wrap">
    <NavBar />
    <div class="container">
      <div class="page-head">
        <span class="sec-dot"></span>
        <h2 class="page-title">搜索结果</h2>
      </div>
      <h3 class="kw-title">关键词：<span class="kw">{{ keyword }}</span></h3>

      <!-- 景点/美食/快闪结果 -->
      <section class="result-section">
        <div class="result-title">
          <h3>景点 / 美食 / 活动 <el-tag size="small" type="info">{{ spotResults.length }}</el-tag></h3>
        </div>
        <el-row :gutter="16" v-if="spotResults.length">
          <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="spot in spotResults" :key="spot.id">
            <SpotCard :spot="spot" @select="addToSelected" @favorite="toggleFavorite" />
          </el-col>
        </el-row>
        <el-empty v-else description="暂无相关去处" :image-size="80" />
      </section>

      <!-- 非遗结果 -->
      <section class="result-section">
        <div class="result-title">
          <h3>非遗文化 <el-tag size="small" type="info">{{ heritageResults.length }}</el-tag></h3>
        </div>
        <el-row :gutter="16" v-if="heritageResults.length">
          <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="h in heritageResults" :key="h.id">
            <el-card class="heritage-card" shadow="hover" @click="goHeritage">
              <div class="h-cover"><img :src="h.cover" :alt="h.name" /></div>
              <div class="h-body">
                <div class="h-name">{{ h.name }}</div>
                <div class="h-tags">
                  <el-tag :type="h.level === '国家级' ? 'danger' : 'warning'" size="small">{{ h.level }}</el-tag>
                  <el-tag size="small">{{ h.category }}</el-tag>
                </div>
                <div class="h-inheritor">传承人：{{ h.inheritor || '暂无' }}</div>
              </div>
            </el-card>
          </el-col>
        </el-row>
        <el-empty v-else description="暂无相关非遗" :image-size="80" />
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import NavBar from '../components/NavBar.vue'
import SpotCard from '../components/SpotCard.vue'
import { searchSpots, searchHeritage, addFavorite } from '../api'
import { useSelectedStore } from '../store/selected'
import { useUserStore } from '../store/user'

const route = useRoute()
const router = useRouter()
const selectedStore = useSelectedStore()
const userStore = useUserStore()

const keyword = ref('')
const spotResults = ref([])
const heritageResults = ref([])

onMounted(() => {
  keyword.value = route.query.keyword || ''
  doSearch()
})

watch(() => route.query.keyword, (val) => {
  keyword.value = val || ''
  doSearch()
})

async function doSearch() {
  const kw = keyword.value.trim()
  if (!kw) {
    spotResults.value = []
    heritageResults.value = []
    return
  }
  try {
    spotResults.value = await searchSpots(kw)
  } catch (e) { spotResults.value = [] }
  try {
    heritageResults.value = await searchHeritage(kw)
  } catch (e) { heritageResults.value = [] }
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

const goHeritage = () => {
  router.push('/heritage')
}
</script>

<style scoped>
.page-wrap { background: #faf7f0; min-height: 100vh; padding-bottom: 40px; }
.container { max-width: 1200px; margin: 0 auto; padding: 28px 20px; }
.page-head { display: flex; align-items: center; gap: 10px; margin-bottom: 14px; }
.sec-dot { width: 4px; height: 22px; background: linear-gradient(180deg, #c45a3a, #d97757); border-radius: 2px; }
.page-title { font-size: 22px; font-weight: 600; color: #2c2c2c; margin: 0; }
.kw-title { font-size: 15px; color: #666; margin: 0 0 24px; font-weight: 400; }
.kw { color: #c45a3a; font-weight: 600; }
.result-section { margin-bottom: 36px; }
.result-title { margin-bottom: 16px; }
.result-title h3 { font-size: 17px; color: #2c2c2c; display: flex; align-items: center; gap: 8px; font-weight: 600; }
.heritage-card { cursor: pointer; border-radius: 10px; transition: transform 0.2s; }
.heritage-card:hover { transform: translateY(-3px); }
.h-cover { height: 160px; overflow: hidden; }
.h-cover img { width: 100%; height: 100%; object-fit: cover; }
.h-body { padding: 12px; }
.h-name { font-size: 16px; font-weight: 600; margin-bottom: 8px; }
.h-tags { display: flex; gap: 6px; margin-bottom: 8px; }
.h-inheritor { color: #909399; font-size: 13px; }
</style>
