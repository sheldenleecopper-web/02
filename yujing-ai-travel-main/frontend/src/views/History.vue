<template>
  <div>
    <NavBar />
    <div class="container">
      <h2 class="page-title">我的攻略记录</h2>
      <el-row :gutter="16">
        <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="g in list" :key="g.id">
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
              <el-button size="small" @click="del(g.id)">删除</el-button>
            </div>
          </el-card>
        </el-col>
      </el-row>
      <el-empty v-if="!list.length" description="暂无保存的攻略" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import NavBar from '../components/NavBar.vue'
import { getGuideList, deleteGuide } from '../api'

const list = ref([])

onMounted(async () => {
  try {
    list.value = await getGuideList()
  } catch (e) {}
})

const formatTime = (t) => t ? new Date(t).toLocaleString('zh-CN') : ''

const downloadPoster = (g) => {
  const a = document.createElement('a')
  a.href = g.posterUrl
  a.download = `${g.title}.png`
  a.click()
}

const del = async (id) => {
  try {
    await ElMessageBox.confirm('确定删除该攻略吗？', '提示', { type: 'warning' })
    await deleteGuide(id)
    list.value = list.value.filter(g => g.id !== id)
    ElMessage.success('已删除')
  } catch (e) {}
}
</script>

<style scoped>
.container { max-width: 1200px; margin: 0 auto; padding: 24px 20px; }
.page-title { margin-bottom: 24px; }
.guide-card { text-align: center; }
.poster-thumb { height: 200px; border-radius: 6px; overflow: hidden; margin-bottom: 12px; background: #f5f7fa; }
.poster-thumb img { width: 100%; height: 100%; object-fit: cover; }
.poster-thumb.placeholder { display: flex; flex-direction: column; align-items: center; justify-content: center; color: #ccc; font-size: 13px; gap: 8px; }
.g-title { font-size: 16px; font-weight: 600; margin-bottom: 6px; }
.g-meta { color: #909399; font-size: 13px; margin-bottom: 4px; }
.g-time { color: #c0c4cc; font-size: 12px; margin-bottom: 12px; }
.g-actions { display: flex; gap: 6px; justify-content: center; }
</style>
