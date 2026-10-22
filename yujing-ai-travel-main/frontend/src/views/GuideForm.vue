<template>
  <div class="page-wrap">
    <NavBar />
    <div class="container">
      <div class="page-head">
        <span class="sec-dot"></span>
        <h2 class="page-title">生成我的重庆旅游攻略</h2>
      </div>

      <el-row :gutter="24">
        <el-col :xs="24" :md="16">
          <el-card>
            <el-form :model="form" label-width="100px">
              <el-form-item label="指定去处">
                <div class="selected-spots">
                  <el-tag v-for="(name, i) in selectedStore.spotNames" :key="i" closable @close="removeSpot(i)">
                    {{ name }}
                  </el-tag>
                  <span v-if="!selectedStore.spotNames.length" class="tip">从首页/热点榜选择去处，或留空由AI推荐</span>
                </div>
              </el-form-item>

              <el-form-item label="出行天数" required>
                <el-input-number v-model="form.days" :min="1" :max="15" /> 天
              </el-form-item>

              <el-form-item label="出行人数" required>
                <el-input-number v-model="form.people" :min="1" :max="20" /> 人
              </el-form-item>

              <el-form-item label="预算范围" required>
                <el-radio-group v-model="form.budget">
                  <el-radio label="经济">经济</el-radio>
                  <el-radio label="适中">适中</el-radio>
                  <el-radio label="豪华">豪华</el-radio>
                </el-radio-group>
              </el-form-item>

              <el-form-item label="兴趣偏好" required>
                <el-checkbox-group v-model="form.interests">
                  <el-checkbox label="自然探索" />
                  <el-checkbox label="人文历史" />
                  <el-checkbox label="美食探店" />
                  <el-checkbox label="城市休闲" />
                  <el-checkbox label="夜生活" />
                  <el-checkbox label="亲子玩乐" />
                  <el-checkbox label="打卡摄影" />
                  <el-checkbox label="非遗体验" />
                </el-checkbox-group>
              </el-form-item>

              <el-form-item label="交通方式" required>
                <el-radio-group v-model="form.transport">
                  <el-radio label="飞机">飞机</el-radio>
                  <el-radio label="高铁">高铁</el-radio>
                  <el-radio label="自驾">自驾</el-radio>
                  <el-radio label="公共交通">公共交通</el-radio>
                </el-radio-group>
              </el-form-item>

              <el-form-item label="住宿偏好" required>
                <el-radio-group v-model="form.accommodation">
                  <el-radio label="经济型">经济型</el-radio>
                  <el-radio label="舒适型">舒适型</el-radio>
                  <el-radio label="豪华型">豪华型</el-radio>
                </el-radio-group>
              </el-form-item>

              <el-form-item label="特殊需求">
                <el-input v-model="form.specialNeed" type="textarea" :rows="3" placeholder="如有特殊需求请说明（选填）" maxlength="500" show-word-limit />
              </el-form-item>

              <el-form-item>
                <el-button type="danger" size="large" @click="submit" :loading="loading">
                  {{ loading ? 'AI正在生成中...' : '生成攻略' }}
                </el-button>
              </el-form-item>
            </el-form>
          </el-card>
        </el-col>

        <el-col :xs="24" :md="8">
          <el-card class="tips">
            <h3>小贴士</h3>
            <ul>
              <li>指定去处将被优先安排进行程</li>
              <li>系统会自动优化每日路线，减少折返</li>
              <li>根据你的预算推荐合适的酒店</li>
              <li>生成后可一键制作海报式攻略图</li>
            </ul>
          </el-card>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import NavBar from '../components/NavBar.vue'
import { generateGuide } from '../api'
import { useSelectedStore } from '../store/selected'

const route = useRoute()
const router = useRouter()
const selectedStore = useSelectedStore()

const loading = ref(false)
const form = ref({
  days: 3,
  people: 2,
  budget: '适中',
  travelDate: '',
  interests: ['人文历史', '美食探店'],
  transport: '高铁',
  accommodation: '舒适型',
  specialNeed: ''
})

onMounted(() => {
  // 从推荐行程方案带入
  if (route.query.plan) {
    try {
      const plan = JSON.parse(route.query.plan)
      form.value.days = plan.days || 3
      form.value.interests = plan.interests || []
      ElMessage.info(`已带入行程方案：${plan.name}`)
    } catch (e) {}
  }
})

const removeSpot = (i) => {
  selectedStore.spotIds.splice(i, 1)
  selectedStore.spotNames.splice(i, 1)
}

const submit = async () => {
  if (!form.value.days || !form.value.people) {
    ElMessage.warning('请填写出行天数和人数')
    return
  }
  if (!form.value.budget) { ElMessage.warning('请选择预算范围'); return }
  if (!form.value.interests.length) { ElMessage.warning('请至少选择一个兴趣偏好'); return }
  if (!form.value.transport) { ElMessage.warning('请选择交通方式'); return }
  if (!form.value.accommodation) { ElMessage.warning('请选择住宿偏好'); return }

  loading.value = true
  try {
    const data = await generateGuide({
      spotIds: selectedStore.spotIds,
      days: form.value.days,
      people: form.value.people,
      budget: form.value.budget,
      travelDate: form.value.travelDate,
      interests: form.value.interests,
      transport: form.value.transport,
      accommodation: form.value.accommodation,
      specialNeed: form.value.specialNeed
    })
    selectedStore.clear()
    router.push(`/guide/detail/${data.guideId}`)
  } catch (e) {
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.page-wrap { background: #faf7f0; min-height: 100vh; padding-bottom: 40px; }
.container { max-width: 1200px; margin: 0 auto; padding: 28px 20px; }
.page-head { display: flex; align-items: center; gap: 10px; margin-bottom: 22px; }
.sec-dot { width: 4px; height: 22px; background: linear-gradient(180deg, #c45a3a, #d97757); border-radius: 2px; }
.page-title { font-size: 22px; font-weight: 600; color: #2c2c2c; margin: 0; }
.selected-spots { display: flex; flex-wrap: wrap; gap: 8px; }
.selected-spots .tip { color: #909399; font-size: 14px; }
.tips { border-radius: 12px; }
.tips ul { padding-left: 20px; color: #606266; line-height: 2; }
</style>
