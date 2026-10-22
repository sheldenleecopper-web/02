<template>
  <div class="page-wrap">
    <NavBar />
    <div class="container">
      <div class="page-head">
        <span class="sec-dot"></span>
        <h2 class="page-title">个人主页</h2>
      </div>
      <el-card class="user-info-card" shadow="never">
        <div class="user-info">
          <el-avatar :size="56" class="avatar">{{ userInfo.username ? userInfo.username.charAt(0).toUpperCase() : 'U' }}</el-avatar>
          <div class="user-meta">
            <div class="user-name">{{ userInfo.username || '加载中...' }}</div>
            <div class="user-id">用户ID：{{ userInfo.userId || '-' }}</div>
          </div>
        </div>
      </el-card>
      <el-tabs v-model="activeTab" type="card">
        <el-tab-pane label="我的收藏" name="favorite">
          <el-row :gutter="16">
            <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="spot in favorites" :key="spot.id">
              <SpotCard :spot="spot" :is-fav="true" @select="addToSelected" @favorite="removeFav" />
            </el-col>
          </el-row>
          <el-empty v-if="!favorites.length" description="暂无收藏" />
        </el-tab-pane>

        <el-tab-pane label="我的行程" name="trip">
          <div class="trip-header" v-if="selectedStore.spotIds.length">
            <span>已选 {{ selectedStore.spotIds.length }} 个去处</span>
            <el-button type="danger" @click="goGuideForm">生成攻略</el-button>
          </div>
          <el-row :gutter="16" v-if="selectedStore.spotIds.length">
            <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="(name, i) in selectedStore.spotNames" :key="i">
              <el-card class="trip-card" shadow="hover">
                <div class="trip-name">{{ name }}</div>
                <el-button size="small" type="danger" plain @click="removeTripSpot(i)">移除</el-button>
              </el-card>
            </el-col>
          </el-row>
          <el-empty v-else description="暂无行程，去首页或景点推荐选择去处吧" />
        </el-tab-pane>

        <el-tab-pane label="攻略历史记录" name="guide">
          <el-row :gutter="16">
            <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="g in guideList" :key="g.id">
              <el-card class="guide-card" shadow="hover">
                <div class="poster-thumb" v-if="g.posterUrl">
                  <img :src="g.posterUrl" />
                </div>
                <div class="poster-thumb placeholder" v-else>
                  <el-icon :size="40" color="#ccc"><Document /></el-icon>
                  <span>暂无海报</span>
                </div>
                <div class="g-title">{{ g.title }}</div>
                <div class="g-meta">{{ g.paramsSummary }}</div>
                <div class="g-time">{{ formatTime(g.createTime) }}</div>
                <div class="g-actions">
                  <el-button size="small" type="primary" @click="$router.push(`/guide/detail/${g.id}`)">查看</el-button>
                  <el-button size="small" type="danger" @click="downloadPoster(g)" v-if="g.posterUrl">下载海报</el-button>
                  <el-button size="small" @click="delGuide(g.id)">删除</el-button>
                </div>
              </el-card>
            </el-col>
          </el-row>
          <el-empty v-if="!guideList.length" description="暂无保存的攻略" />
        </el-tab-pane>

        <el-tab-pane label="攻略图生成记录" name="poster">
          <el-row :gutter="16">
            <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="g in posters" :key="g.id">
              <el-card class="poster-card" shadow="hover" @click="$router.push(`/guide/detail/${g.id}`)">
                <div class="poster-thumb"><img :src="g.posterUrl" /></div>
                <div class="p-title">{{ g.title }}</div>
                <div class="p-time">{{ formatTime(g.createTime) }}</div>
                <el-button size="small" type="danger" @click.stop="downloadPoster(g)">下载</el-button>
              </el-card>
            </el-col>
          </el-row>
          <el-empty v-if="!posters.length" description="暂无生成记录" />
        </el-tab-pane>

        <el-tab-pane label="历史浏览" name="browse">
          <div class="browse-header">
            <el-button type="danger" text @click="clearBrowse" v-if="browseList.length">清空浏览记录</el-button>
          </div>
          <el-row :gutter="16">
            <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="spot in browseList" :key="spot.id">
              <SpotCard :spot="spot" @select="addToSelected" @favorite="addFav" />
            </el-col>
          </el-row>
          <el-empty v-if="!browseList.length" description="暂无浏览记录" />
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import NavBar from '../components/NavBar.vue'
import SpotCard from '../components/SpotCard.vue'
import { getFavoriteList, removeFavorite, getBrowseHistory, clearBrowseHistory, addFavorite, getPosterRecordList, getUserInfo, getGuideList, deleteGuide } from '../api'
import { useSelectedStore } from '../store/selected'
import { useRouter } from 'vue-router'

const router = useRouter()
const selectedStore = useSelectedStore()
const activeTab = ref('favorite')
const favorites = ref([])
const posters = ref([])
const browseList = ref([])
const guideList = ref([])
const userInfo = ref({})

onMounted(loadAll)

async function loadAll() {
  try { userInfo.value = await getUserInfo() } catch (e) {}
  try { favorites.value = await getFavoriteList() } catch (e) {}
  try { posters.value = await getPosterRecordList() } catch (e) {}
  try { browseList.value = await getBrowseHistory() } catch (e) {}
  try { guideList.value = await getGuideList() } catch (e) {}
}

const addToSelected = (spot) => {
  selectedStore.addSpot(spot.id, spot.name)
  ElMessage.success(`已加入行程：${spot.name}`)
}

const removeFav = async (spot) => {
  try {
    await removeFavorite(spot.id)
    favorites.value = favorites.value.filter(s => s.id !== spot.id)
    ElMessage.success('已取消收藏')
  } catch (e) {}
}

const addFav = async (spot) => {
  try { await addFavorite(spot.id); ElMessage.success('已收藏') } catch (e) {}
}

const clearBrowse = async () => {
  try {
    await ElMessageBox.confirm('确定清空所有浏览记录吗？', '提示', { type: 'warning' })
    await clearBrowseHistory()
    browseList.value = []
    ElMessage.success('已清空')
  } catch (e) {}
}

const removeTripSpot = (index) => {
  const id = selectedStore.spotIds[index]
  selectedStore.removeSpot(id)
  ElMessage.success('已移除')
}

const goGuideForm = () => {
  router.push('/guide/form')
}

const delGuide = async (id) => {
  try {
    await ElMessageBox.confirm('确定删除该攻略吗？', '提示', { type: 'warning' })
    await deleteGuide(id)
    guideList.value = guideList.value.filter(g => g.id !== id)
    ElMessage.success('已删除')
  } catch (e) {}
}

const formatTime = (t) => t ? new Date(t).toLocaleString('zh-CN') : ''

const downloadPoster = (g) => {
  const a = document.createElement('a')
  a.href = g.posterUrl
  a.download = `${g.title}.png`
  a.click()
}
</script>

<style scoped>
.page-wrap { background: #faf7f0; min-height: 100vh; padding-bottom: 40px; }
.container { max-width: 1200px; margin: 0 auto; padding: 28px 20px; }
.page-head { display: flex; align-items: center; gap: 10px; margin-bottom: 18px; }
.sec-dot { width: 4px; height: 22px; background: linear-gradient(180deg, #c45a3a, #d97757); border-radius: 2px; }
.page-title { font-size: 22px; font-weight: 600; color: #2c2c2c; margin: 0; }
.user-info-card { margin-bottom: 20px; border-radius: 12px; }
.user-info { display: flex; align-items: center; gap: 16px; }
.avatar { background: linear-gradient(135deg, #c45a3a, #d97757); color: #fff; font-size: 22px; font-weight: 600; }
.user-name { font-size: 18px; font-weight: 600; color: #2c2c2c; }
.user-id { font-size: 13px; color: #909399; margin-top: 4px; }
.browse-header { margin-bottom: 16px; }
.trip-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; color: #606266; }
.trip-card { text-align: center; border-radius: 10px; }
.trip-name { font-size: 16px; font-weight: 600; margin-bottom: 12px; }
.poster-card { cursor: pointer; text-align: center; border-radius: 10px; }
.guide-card { text-align: center; border-radius: 10px; }
.poster-thumb { height: 200px; border-radius: 8px; overflow: hidden; margin-bottom: 12px; background: #f5f0e8; }
.poster-thumb img { width: 100%; height: 100%; object-fit: cover; }
.poster-thumb.placeholder { display: flex; flex-direction: column; align-items: center; justify-content: center; color: #bbb; font-size: 13px; gap: 8px; }
.p-title { font-size: 15px; font-weight: 600; margin-bottom: 4px; color: #2c2c2c; }
.p-time { color: #c0c4cc; font-size: 12px; margin-bottom: 10px; }
.g-title { font-size: 16px; font-weight: 600; margin-bottom: 6px; color: #2c2c2c; }
.g-meta { color: #909399; font-size: 13px; margin-bottom: 4px; }
.g-time { color: #c0c4cc; font-size: 12px; margin-bottom: 12px; }
.g-actions { display: flex; gap: 6px; justify-content: center; }
</style>
