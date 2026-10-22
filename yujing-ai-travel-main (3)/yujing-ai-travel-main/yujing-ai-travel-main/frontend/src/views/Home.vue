<template>
  <div class="home-page">
    <NavBar />

    <!-- Hero Banner -->
    <section class="hero">
      <div class="hero-bg">
        <img :src="heroBg" alt="重庆城市风光" />
        <div class="hero-overlay"></div>
      </div>
      <div class="hero-content">
        <div class="hero-tag">城市风光 · 动态</div>
        <h1 class="hero-title">渝境智旅 · 重庆人文漫游</h1>
        <p class="hero-sub">一城山水，一路人文；用 AI 发现重庆的独特魅力</p>
        <div class="hero-btns">
          <el-button type="danger" size="large" class="btn-primary" @click="goForm">开启重庆之旅</el-button>
          <el-button size="large" class="btn-outline" @click="$router.push('/heritage')">探索人文景点</el-button>
        </div>
      </div>
    </section>

    <div class="page-wrap">
      <!-- 重庆印象 -->
      <section class="section impression">
        <div class="sec-head">
          <span class="sec-dot"></span>
          <h2 class="sec-title">重庆印象</h2>
        </div>
        <div class="impression-body">
          <div class="impression-text">
            <p>山城重庆，依山傍水，独特的地形与人文风情闻名。</p>
            <p>这里有壮丽的江河、有热闹的市井、有深厚的巴渝文化，</p>
            <p>是一座让人来了就不想走的城市。</p>
            <div class="impression-icons">
              <div class="imp-item">
                <span class="imp-icon">🏔</span>
                <div>
                  <div class="imp-name">山水之城</div>
                  <div class="imp-desc">两江环绕 · 武陵山脉</div>
                </div>
              </div>
              <div class="imp-item">
                <span class="imp-icon">🚈</span>
                <div>
                  <div class="imp-name">立体交通</div>
                  <div class="imp-desc">轻轨穿楼 · 长江索道</div>
                </div>
              </div>
              <div class="imp-item">
                <span class="imp-icon">🎭</span>
                <div>
                  <div class="imp-name">巴渝文化</div>
                  <div class="imp-desc">历史悠久 · 人文荟萃</div>
                </div>
              </div>
            </div>
          </div>
          <div class="impression-img">
            <img :src="impressionImg" alt="重庆印象" />
          </div>
        </div>
      </section>

      <!-- 为你推荐 -->
      <section class="section recommend">
        <div class="sec-head">
          <span class="sec-dot"></span>
          <h2 class="sec-title">为你推荐</h2>
          <div class="sec-tabs">
            <span :class="['sec-tab', recommendTab === 'spot' && 'active']" @click="recommendTab = 'spot'">人气景点</span>
            <span :class="['sec-tab', recommendTab === 'hot' && 'active']" @click="recommendTab = 'hot'">热度榜单</span>
          </div>
          <router-link to="/spots" class="sec-more">查看更多 →</router-link>
        </div>
        <div class="rec-cards">
          <div class="rec-card" v-for="spot in displaySpots" :key="spot.id" @click="goDetail(spot.id)">
            <div class="rec-cover">
              <img :src="spot.cover" :alt="spot.name" />
            </div>
            <div class="rec-info">
              <div class="rec-name">{{ spot.name }}</div>
              <div class="rec-meta">
                <span class="rec-region"><el-icon><Location /></el-icon>{{ spot.region }}</span>
                <span class="rec-heat"><span class="rec-dot"></span>{{ spot.heatIndex || spot.heat }}热度</span>
              </div>
              <div class="rec-footer">
                <span class="rec-type">{{ spot.typeLabel }}</span>
                <span class="rec-detail">查看详情 →</span>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- 重庆热度推荐榜（4榜单横向并排） -->
      <section class="section ranking-section">
        <div class="ranking-header">
          <div class="ranking-title-wrap">
            <div class="sec-head" style="margin-bottom:6px">
              <span class="sec-dot"></span>
              <h2 class="sec-title" style="margin:0">重庆热度推荐榜</h2>
            </div>
            <p class="ranking-sub">基于游客浏览热度实时更新</p>
          </div>
          <router-link to="/spots" class="ranking-more">查看更多 →</router-link>
        </div>

        <div class="ranking-boards">
          <!-- 景点榜 -->
          <div class="board">
            <div class="board-head">
              <span class="board-icon attraction">景</span>
              <h3 class="board-name">景点榜</h3>
            </div>
            <div class="board-cards">
              <div class="rank-card" v-for="(item, idx) in attractionList" :key="'a'+idx">
                <div class="card-top">
                  <img :src="item.cover" :alt="item.name" class="card-img" />
                  <span class="rank-badge" :class="'rank-'+(idx+1)">TOP{{ idx+1 }}</span>
                  <el-icon class="fav-icon"><Star /></el-icon>
                </div>
                <div class="card-body">
                  <div class="card-name">{{ item.name }}</div>
                  <div class="card-region">{{ item.region }}</div>
                  <div class="card-tags">
                    <span class="tag tag-blue">{{ item.tag1 }}</span>
                    <span class="tag tag-green">{{ item.tag2 }}</span>
                  </div>
                  <div class="card-meta">
                    <span class="heat"><i class="dot"></i>热度 {{ item.heat }}</span>
                    <span class="price">¥{{ item.price }} 起</span>
                  </div>
                  <div class="card-reason">上榜理由：{{ item.reason }}</div>
                </div>
              </div>
            </div>
          </div>

          <!-- 美食榜 -->
          <div class="board">
            <div class="board-head">
              <span class="board-icon food">食</span>
              <h3 class="board-name">美食榜</h3>
            </div>
            <div class="board-cards">
              <div class="rank-card" v-for="(item, idx) in foodList" :key="'f'+idx">
                <div class="card-top">
                  <img :src="item.cover" :alt="item.name" class="card-img" />
                  <span class="rank-badge" :class="'rank-'+(idx+1)">TOP{{ idx+1 }}</span>
                  <el-icon class="fav-icon"><Star /></el-icon>
                </div>
                <div class="card-body">
                  <div class="card-name">{{ item.name }}</div>
                  <div class="card-region">{{ item.region }}</div>
                  <div class="card-tags">
                    <span class="tag tag-orange">{{ item.tag1 }}</span>
                    <span class="tag tag-red">{{ item.tag2 }}</span>
                  </div>
                  <div class="card-meta">
                    <span class="heat"><i class="dot"></i>热度 {{ item.heat }}</span>
                    <span class="price">¥{{ item.price }} 起</span>
                  </div>
                  <div class="card-reason">上榜理由：{{ item.reason }}</div>
                </div>
              </div>
            </div>
          </div>

          <!-- 酒店榜 -->
          <div class="board">
            <div class="board-head">
              <span class="board-icon hotel">宿</span>
              <h3 class="board-name">酒店榜</h3>
            </div>
            <div class="board-cards">
              <div class="rank-card" v-for="(item, idx) in hotelList" :key="'h'+idx">
                <div class="card-top">
                  <img :src="item.cover" :alt="item.name" class="card-img" />
                  <span class="rank-badge" :class="'rank-'+(idx+1)">TOP{{ idx+1 }}</span>
                  <el-icon class="fav-icon"><Star /></el-icon>
                </div>
                <div class="card-body">
                  <div class="card-name">{{ item.name }}</div>
                  <div class="card-region">{{ item.region }}</div>
                  <div class="card-tags">
                    <span class="tag tag-purple">{{ item.tag1 }}</span>
                    <span class="tag tag-blue">{{ item.tag2 }}</span>
                  </div>
                  <div class="card-meta">
                    <span class="heat"><i class="dot"></i>热度 {{ item.heat }}</span>
                    <span class="price">¥{{ item.price }} 起</span>
                  </div>
                  <div class="card-reason">上榜理由：{{ item.reason }}</div>
                </div>
              </div>
            </div>
          </div>

          <!-- 演艺活动榜 -->
          <div class="board">
            <div class="board-head">
              <span class="board-icon activity">演</span>
              <h3 class="board-name">演艺活动榜</h3>
            </div>
            <div class="board-cards">
              <div class="rank-card" v-for="(item, idx) in activityList" :key="'c'+idx">
                <div class="card-top">
                  <img :src="item.cover" :alt="item.name" class="card-img" />
                  <span class="rank-badge" :class="'rank-'+(idx+1)">TOP{{ idx+1 }}</span>
                  <el-icon class="fav-icon"><Star /></el-icon>
                </div>
                <div class="card-body">
                  <div class="card-name">{{ item.name }}</div>
                  <div class="card-region">{{ item.region }}</div>
                  <div class="card-tags">
                    <span class="tag tag-pink">{{ item.tag1 }}</span>
                    <span class="tag tag-orange">{{ item.tag2 }}</span>
                  </div>
                  <div class="card-meta">
                    <span class="heat"><i class="dot"></i>热度 {{ item.heat }}</span>
                    <span class="price">¥{{ item.price }} 起</span>
                  </div>
                  <div class="card-reason">上榜理由：{{ item.reason }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- 推荐路线 -->
      <section class="section routes">
        <div class="sec-head">
          <span class="sec-dot"></span>
          <h2 class="sec-title">推荐路线</h2>
          <span class="sec-sub">精选行程 · 深度体验</span>
          <router-link to="/guide/form" class="sec-more">查看更多 →</router-link>
        </div>
        <div class="route-cards">
          <div class="route-card" v-for="(r, idx) in routeCards" :key="idx" @click="generateFromPlan(r.plan)">
            <div class="route-cover">
              <img :src="r.cover" :alt="r.name" />
            </div>
            <div class="route-info">
              <div class="route-name">{{ r.name }}</div>
              <div class="route-meta">
                <el-icon><Clock /></el-icon>
                <span>{{ r.days }}</span>
                <span class="route-tags">{{ r.tags }}</span>
              </div>
              <div class="route-btn">查看路线 →</div>
            </div>
          </div>
        </div>
      </section>
    </div>

    <!-- 底部智能生成攻略 CTA -->
    <section class="cta-section">
      <div class="cta-inner">
        <div class="cta-icon">🤖</div>
        <div class="cta-text">
          <div class="cta-title">智能生成攻略</div>
          <div class="cta-desc">告诉 AI 你的时间、兴趣与预算，生成专属重庆行程</div>
        </div>
        <el-button type="danger" size="large" class="cta-btn" @click="goForm">
          立即生成攻略 →
        </el-button>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import NavBar from '../components/NavBar.vue'
import { getHomeRecommend, getSpotList, getHeritageList, addFavorite } from '../api'
import { useSelectedStore } from '../store/selected'
import { useUserStore } from '../store/user'

const router = useRouter()
const selectedStore = useSelectedStore()
const userStore = useUserStore()

const img = (seed) => `https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=${encodeURIComponent(seed)}&image_size=landscape_16_9`

const heroBg = img('重庆洪崖洞夜景长江索道城市风光全景航拍')
const impressionImg = img('重庆磁器口古镇传统建筑巴渝文化')

const recommendTab = ref('spot')
const recommendSpots = ref([])
const hotSpots = ref([])
const heritages = ref([])

const typeLabelMap = { attraction: '景点榜', food: '美食榜', activity: '活动榜' }

const displaySpots = computed(() => {
  const list = recommendTab.value === 'spot' ? recommendSpots.value : hotSpots.value
  return list.slice(0, 4).map(s => ({ ...s, typeLabel: typeLabelMap[s.type] || '人气榜' }))
})

const routeCards = ref([
  {
    name: '山城经典一日游',
    days: '约 6 小时',
    tags: '洪崖洞 · 解放碑 · 长江索道',
    cover: img('重庆洪崖洞解放碑长江索道地标景点'),
    plan: { name: '山城经典一日游', days: 1, spots: ['洪崖洞', '解放碑', '长江索道'], interests: ['城市休闲', '打卡摄影'] }
  },
  {
    name: '巴渝人文两日游',
    days: '约 2 天',
    tags: '磁器口 · 鹅岭 · 白公馆',
    cover: img('重庆磁器口古镇鹅岭二厂白公馆人文景点'),
    plan: { name: '巴渝人文两日游', days: 2, spots: ['磁器口古镇', '鹅岭二厂', '白公馆'], interests: ['人文历史', '非遗体验'] }
  },
  {
    name: '江景夜景漫游',
    days: '约 4 小时',
    tags: '两江夜游 · 南山一棵树',
    cover: img('重庆两江交汇夜景南山一棵树航拍'),
    plan: { name: '江景夜景漫游', days: 1, spots: ['南山一棵树', '两江夜游'], interests: ['夜生活', '打卡摄影'] }
  }
])

// 重庆热度推荐榜 占位数据
const attractionList = ref([
  { name: '洪崖洞民俗风貌区', region: '渝中区 · 嘉陵江畔', tag1: '夜景', tag2: '民俗', heat: '98.6万', price: '0', reason: '山城夜景地标，吊脚楼群灯火璀璨', cover: img('重庆洪崖洞夜景吊脚楼') },
  { name: '武隆天生三桥', region: '武隆区 · 仙女山镇', tag1: '自然奇观', tag2: '5A景区', heat: '87.2万', price: '125', reason: '世界自然遗产，喀斯特地貌震撼', cover: img('重庆武隆天生三桥自然风光') },
  { name: '磁器口古镇', region: '沙坪坝区 · 磁器口街道', tag1: '古镇', tag2: '巴渝文化', heat: '76.5万', price: '0', reason: '千年古镇，石板路与老茶馆韵味悠长', cover: img('重庆磁器口古镇街景') }
])

const foodList = ref([
  { name: '重庆老火锅', region: '渝中区 · 解放碑', tag1: '麻辣', tag2: '必吃', heat: '95.3万', price: '88', reason: '牛油锅底麻辣鲜香，山城味道代表', cover: img('重庆老火锅麻辣牛油锅底') },
  { name: '重庆小面', region: '全市各区', tag1: '早餐', tag2: '面食', heat: '82.1万', price: '12', reason: '豌杂面、肥肠面，一碗唤醒山城清晨', cover: img('重庆小面豌杂面') },
  { name: '辣子鸡', region: '歌乐山 · 沙坪坝', tag1: '江湖菜', tag2: '香辣', heat: '68.7万', price: '68', reason: '歌乐山辣子鸡，辣椒堆里找鸡肉', cover: img('重庆歌乐山辣子鸡江湖菜') }
])

const hotelList = ref([
  { name: '重庆解放碑威斯汀酒店', region: '渝中区 · 解放碑', tag1: '五星', tag2: '江景', heat: '91.2万', price: '888', reason: '解放碑核心地段，俯瞰两江夜景', cover: img('重庆解放碑五星级酒店江景房') },
  { name: '重庆丽晶酒店', region: '江北区 · 江北嘴', tag1: '奢华', tag2: '江景', heat: '79.8万', price: '1288', reason: '两江交汇之上，奢华江景体验', cover: img('重庆江北嘴奢华酒店江景') },
  { name: '磁器口花间堂民宿', region: '沙坪坝区 · 磁器口', tag1: '民宿', tag2: '古镇', heat: '65.4万', price: '368', reason: '古镇内庭院民宿，闹中取静', cover: img('重庆磁器口古镇庭院民宿') }
])

const activityList = ref([
  { name: '印象·武隆实景演出', region: '武隆区 · 仙女山', tag1: '实景演出', tag2: '非遗', heat: '89.5万', price: '180', reason: '张艺谋导演，川江号子震撼人心', cover: img('重庆武隆印象实景演出') },
  { name: '重庆国际马戏城', region: '南岸区 · 南滨路', tag1: '马戏', tag2: '亲子', heat: '72.3万', price: '120', reason: '国际级马戏表演，亲子首选', cover: img('重庆马戏城表演现场') },
  { name: '国泰艺术中心话剧', region: '渝中区 · 解放碑', tag1: '话剧', tag2: '文艺', heat: '58.9万', price: '180', reason: '山城文化地标，精品话剧常年上演', cover: img('重庆国泰艺术中心话剧演出') }
])

onMounted(async () => {
  try {
    const rec = await getHomeRecommend()
    recommendSpots.value = rec.recommendSpots || []
  } catch (e) {}

  try {
    hotSpots.value = await getSpotList({ sort: '热度优先' })
  } catch (e) {}

  try {
    heritages.value = (await getHeritageList()).slice(0, 4)
  } catch (e) {}
})

const goForm = () => {
  if (!userStore.token) {
    ElMessage.warning('请先登录')
    router.push('/login')
    return
  }
  router.push('/guide/form')
}

const goDetail = (id) => {
  router.push(`/spot/${id}`)
}

const generateFromPlan = (plan) => {
  if (!userStore.token) {
    ElMessage.warning('请先登录')
    router.push('/login')
    return
  }
  router.push({ path: '/guide/form', query: { plan: JSON.stringify(plan) } })
}
</script>

<style scoped>
.home-page {
  background: #faf7f0;
  min-height: 100vh;
  padding-bottom: 40px;
}

/* ===== Hero ===== */
.hero {
  position: relative;
  height: 460px;
  overflow: hidden;
}
.hero-bg {
  position: absolute; inset: 0;
}
.hero-bg img {
  width: 100%; height: 100%; object-fit: cover;
}
.hero-overlay {
  position: absolute; inset: 0;
  background: linear-gradient(180deg, rgba(0,0,0,0.15) 0%, rgba(0,0,0,0.55) 100%);
}
.hero-content {
  position: relative;
  z-index: 2;
  height: 100%;
  max-width: 1440px;
  margin: 0 auto;
  padding: 80px 40px 0;
  color: #fff;
}
.hero-tag {
  display: inline-block;
  padding: 6px 14px;
  background: rgba(255,255,255,0.2);
  backdrop-filter: blur(8px);
  border-radius: 20px;
  font-size: 13px;
  margin-bottom: 28px;
}
.hero-title {
  font-size: 44px;
  font-weight: 700;
  margin: 0 0 12px;
  letter-spacing: 1px;
}
.hero-sub {
  font-size: 17px;
  opacity: 0.9;
  margin: 0 0 36px;
}
.hero-btns { display: flex; gap: 14px; }
.btn-primary {
  background: linear-gradient(135deg, #c45a3a, #d97757);
  border: none;
  padding: 12px 32px;
  font-weight: 600;
}
.btn-outline {
  background: rgba(255,255,255,0.15);
  border: 1px solid rgba(255,255,255,0.5);
  color: #fff;
  padding: 12px 32px;
}
.btn-outline:hover {
  background: rgba(255,255,255,0.25);
  color: #fff;
}

/* ===== 通用 ===== */
.page-wrap {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 24px;
}
.section {
  margin-top: 56px;
}
.sec-head {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 24px;
}
.sec-dot {
  width: 4px; height: 20px;
  background: linear-gradient(180deg, #c45a3a, #d97757);
  border-radius: 2px;
}
.sec-title {
  font-size: 22px;
  font-weight: 600;
  color: #2c2c2c;
  margin: 0;
}
.sec-sub {
  font-size: 13px;
  color: #999;
  margin-left: 4px;
}
.sec-more {
  margin-left: auto;
  color: #999;
  text-decoration: none;
  font-size: 13px;
}
.sec-more:hover { color: #c45a3a; }
.sec-tabs {
  display: flex;
  gap: 18px;
  margin-left: 8px;
}
.sec-tab {
  font-size: 14px;
  color: #999;
  cursor: pointer;
  padding: 4px 0;
  border-bottom: 2px solid transparent;
  transition: all 0.2s;
}
.sec-tab.active {
  color: #c45a3a;
  border-bottom-color: #c45a3a;
  font-weight: 500;
}

/* ===== 重庆印象 ===== */
.impression-body {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 36px;
  align-items: center;
}
.impression-text p {
  font-size: 15px;
  color: #555;
  line-height: 1.9;
  margin: 0;
}
.impression-icons {
  display: flex;
  gap: 28px;
  margin-top: 28px;
}
.imp-item {
  display: flex;
  align-items: center;
  gap: 10px;
}
.imp-icon {
  width: 40px; height: 40px;
  background: #fff;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}
.imp-name {
  font-size: 14px;
  font-weight: 600;
  color: #2c2c2c;
}
.imp-desc {
  font-size: 12px;
  color: #999;
  margin-top: 2px;
}
.impression-img {
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 16px rgba(0,0,0,0.08);
  height: 280px;
}
.impression-img img {
  width: 100%; height: 100%; object-fit: cover;
}

/* ===== 为你推荐 ===== */
.rec-cards {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}
.rec-card {
  background: #fff;
  border-radius: 10px;
  overflow: hidden;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
}
.rec-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 20px rgba(0,0,0,0.1);
}
.rec-cover {
  height: 140px;
  overflow: hidden;
}
.rec-cover img {
  width: 100%; height: 100%; object-fit: cover;
}
.rec-info {
  padding: 12px 14px 14px;
}
.rec-name {
  font-size: 15px;
  font-weight: 600;
  color: #2c2c2c;
  margin-bottom: 6px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.rec-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 12px;
  color: #999;
  margin-bottom: 8px;
}
.rec-region {
  display: flex;
  align-items: center;
  gap: 2px;
}
.rec-heat {
  color: #e8605a;
  display: flex;
  align-items: center;
  gap: 4px;
}
.rec-dot {
  width: 6px; height: 6px;
  border-radius: 50%;
  background: #e8605a;
  display: inline-block;
}
.rec-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 8px;
  border-top: 1px dashed #eee;
}
.rec-type {
  font-size: 12px;
  color: #c45a3a;
  background: #fef0ea;
  padding: 2px 8px;
  border-radius: 4px;
}
.rec-detail {
  font-size: 12px;
  color: #999;
}

/* ===== 推荐路线 ===== */
.route-cards {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}
.route-card {
  background: #fff;
  border-radius: 12px;
  overflow: hidden;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
  transition: transform 0.2s, box-shadow 0.2s;
}
.route-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 20px rgba(0,0,0,0.1);
}
.route-cover {
  height: 170px;
  overflow: hidden;
}
.route-cover img {
  width: 100%; height: 100%; object-fit: cover;
}
.route-info {
  padding: 14px 16px 16px;
}
.route-name {
  font-size: 16px;
  font-weight: 600;
  color: #2c2c2c;
  margin-bottom: 8px;
}
.route-meta {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 12px;
  color: #999;
  margin-bottom: 10px;
}
.route-tags {
  margin-left: 4px;
  color: #666;
}
.route-btn {
  font-size: 13px;
  color: #c45a3a;
  font-weight: 500;
}

/* ===== CTA ===== */
.cta-section {
  margin: 60px 24px 0;
  max-width: 1152px;
  margin-left: auto;
  margin-right: auto;
}
.cta-inner {
  display: flex;
  align-items: center;
  gap: 20px;
  background: linear-gradient(135deg, #f5e6d8 0%, #faf0e6 100%);
  border-radius: 16px;
  padding: 28px 36px;
  border: 1px solid #e8d5c0;
}
.cta-icon {
  font-size: 36px;
  width: 56px; height: 56px;
  background: #fff;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
}
.cta-text {
  flex: 1;
}
.cta-title {
  font-size: 18px;
  font-weight: 600;
  color: #2c2c2c;
  margin-bottom: 4px;
}
.cta-desc {
  font-size: 13px;
  color: #888;
}
.cta-btn {
  background: linear-gradient(135deg, #c45a3a, #d97757);
  border: none;
  padding: 12px 28px;
  font-weight: 600;
}

/* ===== 重庆热度推荐榜 ===== */
.ranking-section {
  background: #fff;
  border-radius: 16px;
  padding: 24px 28px;
  box-shadow: 0 4px 20px rgba(0,0,0,0.05);
}
.ranking-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  margin-bottom: 20px;
  padding-bottom: 14px;
  border-bottom: 1px solid #f0f2f5;
}
.ranking-title-wrap { }
.ranking-sub { font-size: 13px; color: #999; margin: 0 0 0 14px; }
.ranking-more { color: #999; text-decoration: none; font-size: 13px; }
.ranking-more:hover { color: #c45a3a; }

.ranking-boards {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}
.board {
  background: #f8fafc;
  border-radius: 12px;
  padding: 14px;
  border: 1px solid #eef2f7;
}
.board-head { display: flex; align-items: center; gap: 8px; margin-bottom: 12px; }
.board-icon {
  width: 28px; height: 28px;
  border-radius: 8px;
  display: flex; align-items: center; justify-content: center;
  color: #fff; font-size: 14px; font-weight: 600;
}
.board-icon.attraction { background: linear-gradient(135deg, #409eff, #66b1ff); }
.board-icon.food { background: linear-gradient(135deg, #ff9a3c, #ffb74d); }
.board-icon.hotel { background: linear-gradient(135deg, #9c6ade, #b39ddb); }
.board-icon.activity { background: linear-gradient(135deg, #f06292, #f48fb1); }
.board-name { font-size: 15px; font-weight: 600; color: #2c2c2c; margin: 0; }

.board-cards { display: flex; flex-direction: column; gap: 10px; }
.rank-card {
  background: #fff;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  transition: transform 0.2s, box-shadow 0.2s;
}
.rank-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(0,0,0,0.1);
}
.card-top { position: relative; height: 100px; overflow: hidden; }
.card-img { width: 100%; height: 100%; object-fit: cover; }
.rank-badge {
  position: absolute; top: 6px; left: 6px;
  background: rgba(0,0,0,0.55);
  color: #fff; font-size: 10px; font-weight: 700;
  padding: 2px 7px; border-radius: 4px;
  letter-spacing: 0.5px;
}
.rank-badge.rank-1 { background: linear-gradient(135deg, #ffb300, #ffd54f); color: #5d4037; }
.rank-badge.rank-2 { background: linear-gradient(135deg, #90a4ae, #cfd8dc); color: #37474f; }
.rank-badge.rank-3 { background: linear-gradient(135deg, #a1887f, #d7ccc8); color: #3e2723; }
.fav-icon {
  position: absolute; top: 6px; right: 6px;
  color: #fff; font-size: 16px;
  text-shadow: 0 1px 3px rgba(0,0,0,0.4); cursor: pointer;
}
.card-body { padding: 8px 10px 10px; }
.card-name { font-size: 13px; font-weight: 600; color: #2c2c2c; margin-bottom: 2px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.card-region { font-size: 11px; color: #999; margin-bottom: 6px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.card-tags { display: flex; gap: 4px; margin-bottom: 6px; flex-wrap: wrap; }
.tag { font-size: 10px; padding: 1px 6px; border-radius: 4px; }
.tag-blue { background: #e3f2fd; color: #1976d2; }
.tag-green { background: #e8f5e9; color: #388e3c; }
.tag-orange { background: #fff3e0; color: #f57c00; }
.tag-red { background: #ffebee; color: #d32f2f; }
.tag-purple { background: #f3e5f5; color: #7b1fa2; }
.tag-pink { background: #fce4ec; color: #c2185b; }
.card-meta { display: flex; justify-content: space-between; align-items: center; margin-bottom: 6px; }
.heat { font-size: 11px; color: #ff6b6b; display: flex; align-items: center; gap: 3px; }
.heat .dot { width: 5px; height: 5px; border-radius: 50%; background: #ff6b6b; display: inline-block; }
.price { font-size: 12px; color: #ff5722; font-weight: 600; }
.card-reason { font-size: 10px; color: #666; line-height: 1.5; border-top: 1px dashed #eee; padding-top: 5px; }

/* ===== 响应式 ===== */
@media (max-width: 1200px) {
  .rec-cards { grid-template-columns: repeat(2, 1fr); }
  .route-cards { grid-template-columns: repeat(2, 1fr); }
  .ranking-boards { grid-template-columns: repeat(2, 1fr); }
}
@media (max-width: 900px) {
  .hero { height: 360px; }
  .hero-content { padding: 60px 24px 0; }
  .hero-title { font-size: 30px; }
  .impression-body { grid-template-columns: 1fr; }
  .rec-cards { grid-template-columns: repeat(2, 1fr); }
  .route-cards { grid-template-columns: 1fr; }
}
@media (max-width: 600px) {
  .rec-cards { grid-template-columns: 1fr; }
  .ranking-boards { grid-template-columns: 1fr; }
  .impression-icons { flex-direction: column; gap: 14px; }
  .cta-inner { flex-wrap: wrap; }
}
</style>
