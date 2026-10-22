<template>
  <div class="page-wrap">
    <NavBar />
    <div class="container" v-if="guide">
      <div class="detail-header">
        <h2>{{ guide.title }}</h2>
        <div class="actions">
          <el-button type="primary" @click="saveGuide">保存攻略</el-button>
          <el-button type="danger" @click="showPoster = true">生成海报</el-button>
          <el-button @click="$router.push('/guide/form')">重新生成</el-button>
        </div>
      </div>

      <el-card class="markdown-body" v-html="renderedMarkdown" />

      <!-- 海报预览弹窗 -->
      <el-dialog v-model="showPoster" title="攻略海报" width="460px">
        <div class="poster-wrap">
          <div id="poster-template" class="poster">
            <div class="poster-header">
              <div class="poster-title">{{ guide.title }}</div>
              <div class="poster-sub">渝境智旅 · AI生成</div>
            </div>
            <div class="poster-meta">{{ guide.paramsSummary }}</div>
            <div class="poster-content">
              <div v-for="(line, i) in posterLines" :key="i" class="poster-line">{{ line }}</div>
            </div>
            <div class="poster-footer">扫码关注渝境智旅，发现更多重庆之美</div>
          </div>
        </div>
        <template #footer>
          <el-button @click="downloadPoster">下载海报</el-button>
        </template>
      </el-dialog>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { marked } from 'marked'
import DOMPurify from 'dompurify'
import html2canvas from 'html2canvas'
import NavBar from '../components/NavBar.vue'
import { getGuideDetail, saveGuide as saveApi, savePoster } from '../api'

const route = useRoute()
const router = useRouter()
const guide = ref(null)
const showPoster = ref(false)

const renderedMarkdown = computed(() => {
  if (!guide.value?.markdownContent) return ''
  return DOMPurify.sanitize(marked.parse(guide.value.markdownContent))
})

const posterLines = computed(() => {
  if (!guide.value?.markdownContent) return []
  return guide.value.markdownContent
    .split('\n')
    .filter(l => l.trim().startsWith('#') || l.trim().startsWith('-'))
    .map(l => l.replace(/^#+\s*/, '').replace(/^-\s*/, '• '))
    .slice(0, 15)
})

onMounted(async () => {
  try {
    guide.value = await getGuideDetail(route.params.id)
  } catch (e) {}
})

const saveGuide = async () => {
  try {
    await saveApi({ id: guide.value.id, posterUrl: guide.value.posterUrl || '' })
    ElMessage.success('保存成功')
    router.push('/history')
  } catch (e) {}
}

const downloadPoster = async () => {
  try {
    const el = document.getElementById('poster-template')
    const canvas = await html2canvas(el, { scale: 2, useCORS: true })
    const dataUrl = canvas.toDataURL('image/png')
    await savePoster(guide.value.id, dataUrl)
    const a = document.createElement('a')
    a.href = dataUrl
    a.download = `${guide.value.title}.png`
    a.click()
    ElMessage.success('海报已下载')
  } catch (e) {
    ElMessage.error('海报生成失败')
  }
}
</script>

<style scoped>
.page-wrap { background: #faf7f0; min-height: 100vh; padding-bottom: 40px; }
.container { max-width: 900px; margin: 0 auto; padding: 28px 20px; }
.detail-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
.detail-header h2 { color: #2c2c2c; }
.actions { display: flex; gap: 10px; }
.markdown-body :deep(h1) { font-size: 26px; margin: 20px 0 16px; color: #c45a3a; }
.markdown-body :deep(h2) { font-size: 20px; margin: 20px 0 12px; padding-left: 10px; border-left: 4px solid #c45a3a; }
.markdown-body :deep(h3) { font-size: 17px; margin: 16px 0 10px; color: #2c2c2c; }
.markdown-body :deep(p) { line-height: 1.8; margin: 8px 0; color: #555; }
.markdown-body :deep(ul) { padding-left: 20px; line-height: 2; }
.markdown-body :deep(strong) { color: #c45a3a; }

.poster-wrap { display: flex; justify-content: center; }
.poster {
  width: 320px;
  background: linear-gradient(180deg, #c45a3a 0%, #a04a2e 100%);
  color: #fff;
  border-radius: 12px;
  padding: 24px;
  font-family: sans-serif;
}
.poster-header { text-align: center; margin-bottom: 16px; }
.poster-title { font-size: 22px; font-weight: bold; margin-bottom: 6px; }
.poster-sub { font-size: 13px; opacity: 0.85; }
.poster-meta { text-align: center; font-size: 13px; background: rgba(255,255,255,0.15); border-radius: 6px; padding: 8px; margin-bottom: 16px; }
.poster-content { font-size: 14px; line-height: 1.9; }
.poster-line { margin-bottom: 4px; }
.poster-footer { text-align: center; font-size: 11px; opacity: 0.8; margin-top: 20px; padding-top: 12px; border-top: 1px solid rgba(255,255,255,0.3); }
</style>
