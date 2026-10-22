<template>
  <div class="navbar">
    <div class="nav-inner">
      <div class="logo" @click="$router.push('/')">
        <el-icon :size="24" color="#b54b33"><Location /></el-icon>
        <span>渝境智旅</span>
      </div>

      <!-- 桌面端导航 -->
      <div class="nav-links">
        <router-link to="/" exact-active-class="active">首页</router-link>
        <router-link to="/spots" active-class="active">景点推荐</router-link>
        <router-link to="/food" active-class="active">重庆美食</router-link>
        <router-link to="/heritage" active-class="active">非遗文化</router-link>
        <router-link to="/profile" active-class="active">个人主页</router-link>
      </div>

      <!-- 搜索框 -->
      <div class="nav-search">
        <el-icon class="search-icon"><Search /></el-icon>
        <input
          v-model="keyword"
          class="search-input"
          placeholder="搜索景点、美食、非遗、攻略…"
          @keyup.enter="doSearch"
        />
      </div>

      <!-- 用户区 -->
      <div class="nav-user">
        <template v-if="userStore.token">
          <span class="username">{{ userStore.username }}</span>
          <el-button text class="logout-btn" @click="handleLogout">退出</el-button>
        </template>
        <template v-else>
          <router-link to="/login">
            <el-button type="danger">登录/注册</el-button>
          </router-link>
        </template>
      </div>

      <!-- 移动端汉堡按钮 -->
      <el-icon class="hamburger" @click="mobileMenuOpen = !mobileMenuOpen">
        <Menu v-if="!mobileMenuOpen" />
        <Close v-else />
      </el-icon>
    </div>

    <!-- 移动端菜单 -->
    <transition name="fade">
      <div class="mobile-menu" v-if="mobileMenuOpen" @click.self="mobileMenuOpen = false">
        <div class="mobile-menu-inner" @click.stop>
          <router-link to="/" @click="mobileMenuOpen = false">首页</router-link>
          <router-link to="/spots" @click="mobileMenuOpen = false">景点推荐</router-link>
          <router-link to="/food" @click="mobileMenuOpen = false">重庆美食</router-link>
          <router-link to="/heritage" @click="mobileMenuOpen = false">非遗文化</router-link>
          <router-link to="/profile" @click="mobileMenuOpen = false">个人主页</router-link>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useUserStore } from '../store/user'

const userStore = useUserStore()
const router = useRouter()
const keyword = ref('')
const mobileMenuOpen = ref(false)

const doSearch = () => {
  const kw = keyword.value.trim()
  if (!kw) return
  router.push({ path: '/search', query: { keyword: kw } })
}

const handleLogout = () => {
  userStore.logout()
  router.push('/')
}
</script>

<style scoped>
.navbar {
  height: 62px;
  background: rgba(255, 253, 250, 0.96);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid var(--yj-border, #f0e5da);
  position: sticky;
  top: 0;
  z-index: 100;
}
.nav-inner {
  max-width: 1800px;
  margin: 0 auto;
  height: 100%;
  display: flex;
  align-items: center;
  padding: 0 40px;
  gap: 40px;
}
.logo {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 20px;
  font-weight: 700;
  letter-spacing: 0.5px;
  color: var(--yj-primary, #b54b33);
  cursor: pointer;
  flex-shrink: 0;
}
.nav-links {
  display: flex;
  gap: 34px;
  flex: 1;
}
.nav-links a {
  position: relative;
  text-decoration: none;
  color: #6b5b51;
  font-size: 15px;
  padding: 4px 0;
  transition: color 0.2s;
}
.nav-links a:hover {
  color: var(--yj-primary, #b54b33);
}
.nav-links a.active {
  color: var(--yj-primary, #b54b33);
  font-weight: 600;
}
.nav-links a.active::after {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  bottom: -3px;
  height: 2px;
  background: var(--yj-primary, #b54b33);
  border-radius: 1px;
}
.nav-search {
  position: relative;
  width: 220px;
  flex-shrink: 0;
}
.search-icon {
  position: absolute;
  left: 12px;
  top: 50%;
  transform: translateY(-50%);
  color: #b3a49a;
  font-size: 15px;
}
.search-input {
  width: 100%;
  height: 36px;
  padding: 0 14px 0 34px;
  border: 1px solid #ede1d5;
  border-radius: 18px;
  background: #fbf7f2;
  color: #4a3a31;
  font-size: 13px;
  outline: none;
  box-sizing: border-box;
  transition: border-color 0.2s, background 0.2s;
}
.search-input::placeholder {
  color: #b3a49a;
}
.search-input:focus {
  border-color: var(--yj-primary, #b54b33);
  background: #fff;
}
.nav-user {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-shrink: 0;
}
.username {
  font-size: 14px;
  color: #5b4a40;
  max-width: 80px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.logout-btn {
  color: var(--yj-primary, #b54b33);
}
.hamburger {
  display: none;
  font-size: 22px;
  color: #606266;
  cursor: pointer;
  flex-shrink: 0;
}
.mobile-menu {
  position: fixed;
  inset: 60px 0 0 0;
  background: rgba(0,0,0,0.3);
  z-index: 99;
}
.mobile-menu-inner {
  background: #fffdfa;
  padding: 12px 0;
}
.mobile-menu-inner a {
  display: block;
  padding: 14px 24px;
  color: #6b5b51;
  text-decoration: none;
  font-size: 15px;
  border-bottom: 1px solid #f4ece3;
}
.mobile-menu-inner a:active {
  color: var(--yj-primary, #b54b33);
  background: #fdf1eb;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

@media (max-width: 900px) {
  .nav-links { display: none; }
  .nav-search { width: 150px; }
  .hamburger { display: block; }
}
@media (max-width: 600px) {
  .nav-search { display: none; }
  .username { max-width: 60px; }
}
</style>
