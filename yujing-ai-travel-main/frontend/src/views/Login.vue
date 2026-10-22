<template>
  <div class="login-page">
    <div class="login-box">
      <h2>{{ isLogin ? '登录' : '注册' }} · 渝境智旅</h2>
      <el-form
        ref="formRef"
        :model="form"
        :rules="rules"
        label-width="0"
        @submit.prevent="submit"
      >
        <el-form-item prop="username">
          <el-input v-model="form.username" placeholder="用户名（3-20字符）" prefix-icon="User" />
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            v-model="form.password"
            type="password"
            placeholder="密码（6-20位）"
            prefix-icon="Lock"
            show-password
          />
        </el-form-item>
        <el-form-item v-if="!isLogin" prop="confirmPassword">
          <el-input
            v-model="form.confirmPassword"
            type="password"
            placeholder="确认密码"
            prefix-icon="Lock"
            show-password
          />
        </el-form-item>
        <el-button type="danger" class="submit-btn" @click="submit" :loading="loading">
          {{ isLogin ? '登录' : '注册' }}
        </el-button>
      </el-form>
      <div class="switch" @click="toggleMode">
        {{ isLogin ? '没有账号？去注册' : '已有账号？去登录' }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { useUserStore } from '../store/user'

const router = useRouter()
const userStore = useUserStore()
const formRef = ref(null)
const isLogin = ref(true)
const loading = ref(false)

const form = reactive({
  username: '',
  password: '',
  confirmPassword: ''
})

const validateConfirmPassword = (rule, value, callback) => {
  if (!isLogin.value && value !== form.password) {
    callback(new Error('两次密码不一致'))
  } else {
    callback()
  }
}

const rules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 3, max: 20, message: '用户名需3-20个字符', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, max: 20, message: '密码需6-20位', trigger: 'blur' }
  ],
  confirmPassword: [
    { required: true, message: '请确认密码', trigger: 'blur' },
    { validator: validateConfirmPassword, trigger: 'blur' }
  ]
}

const toggleMode = () => {
  isLogin.value = !isLogin.value
  formRef.value?.clearValidate()
}

const submit = async () => {
  try {
    await formRef.value.validate()
  } catch (e) {
    return
  }

  loading.value = true
  try {
    if (isLogin.value) {
      await userStore.login({ username: form.username, password: form.password })
      ElMessage.success('登录成功')
    } else {
      await userStore.register({ username: form.username, password: form.password })
      ElMessage.success('注册成功')
    }
    router.push('/')
  } catch (e) {
    // 错误已由 request 拦截器统一提示
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.login-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #faf7f0;
}
.login-box {
  width: 400px;
  background: #fff;
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 8px 32px rgba(196, 90, 58, 0.12);
  border: 1px solid #f0ebe0;
}
.login-box h2 { text-align: center; margin-bottom: 28px; color: #2c2c2c; }
.submit-btn { width: 100%; }
.switch { text-align: center; margin-top: 16px; color: #c45a3a; cursor: pointer; font-size: 14px; }
.switch:hover { color: #d97757; }
</style>
