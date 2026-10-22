<template>
  <el-card class="spot-card" shadow="hover" @click="goDetail">
    <div class="cover">
      <img :src="spot.cover" :alt="spot.name" />
      <div class="tags">
        <el-tag v-for="tag in tagList" :key="tag" size="small" type="danger" effect="dark">{{ tag }}</el-tag>
      </div>
    </div>
    <div class="info">
      <div class="name">{{ spot.name }}</div>
      <div class="region">
        <el-icon><Location /></el-icon>
        <span>{{ spot.region }}</span>
      </div>
      <div class="footer">
        <span class="heat">热度 {{ spot.heatIndex }}</span>
        <span v-if="spot.avgCost > 0" class="cost">¥{{ spot.avgCost }}</span>
      </div>
      <div class="actions" @click.stop>
        <el-button size="small" type="primary" plain @click="$emit('select', spot)">加入行程</el-button>
        <el-button size="small" type="danger" plain @click="$emit('favorite', spot)">{{ isFav ? '已收藏' : '收藏' }}</el-button>
      </div>
    </div>
  </el-card>
</template>

<script setup>
import { computed } from 'vue'
import { useRouter } from 'vue-router'

const props = defineProps({
  spot: { type: Object, required: true },
  isFav: { type: Boolean, default: false }
})
defineEmits(['select', 'favorite'])

const router = useRouter()
const tagList = computed(() => (props.spot.tags || '').split(',').filter(Boolean))

const goDetail = () => {
  router.push(`/spot/${props.spot.id}`)
}
</script>

<style scoped>
.spot-card {
  cursor: pointer;
  overflow: hidden;
}
.cover {
  height: 160px;
  position: relative;
  overflow: hidden;
}
.cover img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.tags {
  position: absolute;
  top: 8px;
  left: 8px;
  display: flex;
  gap: 4px;
}
.info {
  padding: 12px;
}
.name {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 6px;
}
.region {
  display: flex;
  align-items: center;
  gap: 4px;
  color: #909399;
  font-size: 13px;
  margin-bottom: 8px;
}
.footer {
  display: flex;
  justify-content: space-between;
  font-size: 13px;
  color: #909399;
  margin-bottom: 10px;
}
.heat { color: #e63946; }
.cost { color: #f56c6c; }
.actions {
  display: flex;
  gap: 8px;
}
</style>
