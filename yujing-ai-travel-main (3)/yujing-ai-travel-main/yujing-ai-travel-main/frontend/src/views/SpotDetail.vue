<template>
  <div class="page-wrap">
    <NavBar />
    <div class="container" v-if="spot">
      <div class="page-head">
        <span class="sec-dot"></span>
        <h2 class="page-title">{{ spot.name }}</h2>
      </div>
      <el-row :gutter="24" class="detail">
        <el-col :xs="24" :md="14">
          <img :src="spot.cover" class="main-img" />
          <div class="intro">
            <h3>简介</h3>
            <p>{{ spot.intro }}</p>
          </div>
        </el-col>
        <el-col :xs="24" :md="10">
          <el-card>
            <h2>{{ spot.name }}</h2>
            <div class="meta">
              <p><el-icon><Guide /></el-icon> 类型：{{ typeText }}</p>
              <p><el-icon><Location /></el-icon> {{ spot.region }} · {{ spot.address }}</p>
              <p><el-icon><Clock /></el-icon> {{ spot.openTime || '全天开放' }}</p>
              <p v-if="spot.avgCost > 0"><el-icon><Money /></el-icon> 人均 ¥{{ spot.avgCost }}</p>
              <p><el-icon><Fire /></el-icon> 热度 {{ spot.heatIndex }}</p>
            </div>
            <div class="tags">
              <el-tag v-for="tag in tags" :key="tag" type="danger" effect="plain">{{ tag }}</el-tag>
            </div>
            <div class="actions">
              <el-button type="primary" size="large" @click="addToSelected">加入行程</el-button>
              <el-button type="danger" size="large" @click="toggleFavorite">{{ isFavorited ? '已收藏' : '收藏' }}</el-button>
            </div>
          </el-card>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import NavBar from '../components/NavBar.vue'
import { getSpotDetail, addFavorite, removeFavorite } from '../api'
import { useSelectedStore } from '../store/selected'
import { useUserStore } from '../store/user'

const route = useRoute()
const router = useRouter()
const selectedStore = useSelectedStore()
const userStore = useUserStore()

const spot = ref(null)
const isFavorited = ref(false)
const tags = computed(() => (spot.value?.tags || '').split(',').filter(Boolean))
const typeText = computed(() => {
  const map = { attraction: '景点', food: '美食', activity: '快闪活动' }
  return map[spot.value?.type] || spot.value?.type || ''
})

onMounted(async () => {
  try {
    spot.value = await getSpotDetail(route.params.id)
    isFavorited.value = !!spot.value?.isFavorited
  } catch (e) {}
})

const addToSelected = () => {
  if (!userStore.token) {
    ElMessage.warning('请先登录')
    router.push('/login')
    return
  }
  selectedStore.addSpot(spot.value.id, spot.value.name)
  ElMessage.success(`已加入行程：${spot.value.name}`)
}

const toggleFavorite = async () => {
  if (!userStore.token) {
    ElMessage.warning('请先登录')
    router.push('/login')
    return
  }
  try {
    if (isFavorited.value) {
      await removeFavorite(spot.value.id)
      isFavorited.value = false
      ElMessage.success('已取消收藏')
    } else {
      await addFavorite(spot.value.id)
      isFavorited.value = true
      ElMessage.success('已收藏')
    }
  } catch (e) {}
}
</script>

<style scoped>
.page-wrap { background: #faf7f0; min-height: 100vh; padding-bottom: 40px; }
.container { max-width: 1200px; margin: 0 auto; padding: 28px 20px; }
.page-head { display: flex; align-items: center; gap: 10px; margin-bottom: 16px; }
.sec-dot { width: 4px; height: 22px; background: linear-gradient(180deg, #c45a3a, #d97757); border-radius: 2px; }
.page-title { font-size: 22px; font-weight: 600; color: #2c2c2c; margin: 0; }
.detail { margin-top: 20px; }
.main-img { width: 100%; border-radius: 12px; margin-bottom: 20px; box-shadow: 0 2px 12px rgba(0,0,0,0.08); }
.intro h3 { margin-bottom: 12px; color: #2c2c2c; }
.intro p { line-height: 1.8; color: #606266; }
.meta p { display: flex; align-items: center; gap: 6px; margin-bottom: 8px; color: #606266; }
.tags { margin: 16px 0; }
.actions { display: flex; gap: 12px; margin-top: 20px; }
.actions .el-button { flex: 1; }
</style>
