<template>
  <div class="page-wrap">
    <NavBar />
    <div class="container">
      <div class="page-head">
        <span class="sec-dot"></span>
        <h2 class="page-title">非遗文化</h2>
      </div>
      <div class="filter-bar">
        <el-radio-group v-model="category" @change="loadList">
          <el-radio-button label="">全部</el-radio-button>
          <el-radio-button label="传统音乐">传统音乐</el-radio-button>
          <el-radio-button label="传统舞蹈">传统舞蹈</el-radio-button>
          <el-radio-button label="传统戏剧">传统戏剧</el-radio-button>
          <el-radio-button label="传统美术">传统美术</el-radio-button>
          <el-radio-button label="传统技艺">传统技艺</el-radio-button>
          <el-radio-button label="民俗">民俗</el-radio-button>
        </el-radio-group>
      </div>

      <el-row :gutter="16">
        <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="h in list" :key="h.id">
          <el-card class="h-card" shadow="hover" @click="showDetail(h)">
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

      <el-dialog v-model="detailVisible" :title="current?.name" width="600px">
        <div v-if="current">
          <img :src="current.cover" style="width:100%;border-radius:8px;margin-bottom:16px" />
          <p style="line-height:1.8">{{ current.intro }}</p>
          <p><strong>传承人：</strong>{{ current.inheritor || '暂无' }}</p>
          <p><strong>体验地点：</strong>{{ current.experienceSpot || '暂无' }}</p>
        </div>
      </el-dialog>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import NavBar from '../components/NavBar.vue'
import { getHeritageList } from '../api'

const category = ref('')
const list = ref([])
const detailVisible = ref(false)
const current = ref(null)

onMounted(loadList)

async function loadList() {
  try {
    list.value = await getHeritageList({ category: category.value || undefined })
  } catch (e) {}
}

const showDetail = (h) => {
  current.value = h
  detailVisible.value = true
}
</script>

<style scoped>
.page-wrap { background: #faf7f0; min-height: 100vh; padding-bottom: 40px; }
.container { max-width: 1200px; margin: 0 auto; padding: 28px 20px; }
.page-head { display: flex; align-items: center; gap: 10px; margin-bottom: 18px; }
.sec-dot { width: 4px; height: 22px; background: linear-gradient(180deg, #c45a3a, #d97757); border-radius: 2px; }
.page-title { font-size: 22px; font-weight: 600; color: #2c2c2c; margin: 0; }
.filter-bar { margin-bottom: 24px; background: #fff; padding: 16px; border-radius: 12px; box-shadow: 0 2px 12px rgba(0,0,0,0.05); }
.h-card { cursor: pointer; border-radius: 10px; transition: transform 0.2s; }
.h-card:hover { transform: translateY(-3px); }
.h-cover { height: 180px; overflow: hidden; }
.h-cover img { width: 100%; height: 100%; object-fit: cover; }
.h-body { padding: 12px; }
.h-name { font-size: 16px; font-weight: 600; margin-bottom: 8px; }
.h-tags { display: flex; gap: 6px; margin-bottom: 8px; }
.h-inheritor { color: #909399; font-size: 13px; }
</style>
