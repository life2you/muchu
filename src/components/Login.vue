<template>
  <div class="login-container">
    <div class="login-card">
      <div class="login-header">
        <h2 class="login-title">欢迎回来</h2>
        <p class="login-subtitle">请登录您的账号继续访问</p>
      </div>

      <el-form :model="loginForm" :rules="loginRules" ref="loginFormRef" label-position="top">
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="loginForm.email" placeholder="请输入邮箱地址" type="email">
            <template #prefix>
              <el-icon><Message /></el-icon>
            </template>
          </el-input>
        </el-form-item>

        <el-tabs v-model="loginType" class="login-tabs">
          <el-tab-pane label="密码登录" name="password">
            <el-form-item label="密码" prop="password" v-if="loginType === 'password'">
              <el-input v-model="loginForm.password" placeholder="请输入密码" type="password" show-password>
                <template #prefix>
                  <el-icon><Lock /></el-icon>
                </template>
              </el-input>
            </el-form-item>
          </el-tab-pane>
          
          <el-tab-pane label="验证码登录" name="code">
            <el-form-item label="验证码" prop="code" v-if="loginType === 'code'">
              <div class="verification-code-container">
                <el-input v-model="loginForm.code" placeholder="请输入验证码" class="verification-input">
                  <template #prefix>
                    <el-icon><Key /></el-icon>
                  </template>
                </el-input>
                <el-button 
                  type="primary" 
                  class="send-code-btn" 
                  :disabled="isSending" 
                  @click="sendVerificationCode"
                >
                  {{ sendBtnText }}
                </el-button>
              </div>
            </el-form-item>
          </el-tab-pane>
        </el-tabs>

        <el-form-item>
          <el-button type="primary" class="login-btn" @click="handleLogin" :loading="isLoading">
            登 录
          </el-button>
        </el-form-item>

        <div class="form-footer">
          <span>还没有账号？</span>
          <router-link to="/register" class="register-link">立即注册</router-link>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { Message, Lock, Key } from '@element-plus/icons-vue'

const router = useRouter()
const loginFormRef = ref(null)
const loginType = ref('password')
const isLoading = ref(false)
const isSending = ref(false)
const countdown = ref(0)

// 倒计时逻辑
const sendBtnText = computed(() => {
  if (countdown.value > 0) {
    return `${countdown.value}秒后重试`
  }
  return '获取验证码'
})

// 表单数据
const loginForm = reactive({
  email: '',
  password: '',
  code: ''
})

// 表单验证规则
const loginRules = reactive({
  email: [
    { required: true, message: '请输入邮箱地址', trigger: 'blur' },
    { type: 'email', message: '请输入有效的邮箱地址', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, message: '密码长度至少为6个字符', trigger: 'blur' }
  ],
  code: [
    { required: true, message: '请输入验证码', trigger: 'blur' },
    { min: 6, max: 6, message: '验证码长度应为6位', trigger: 'blur' }
  ]
})

// 发送验证码
function sendVerificationCode() {
  if (!loginForm.email) {
    ElMessage.warning('请先输入邮箱地址')
    return
  }
  
  // 模拟发送验证码
  isSending.value = true
  countdown.value = 60
  
  ElMessage.success(`验证码已发送至${loginForm.email}`)
  
  // 倒计时
  const timer = setInterval(() => {
    countdown.value--
    if (countdown.value <= 0) {
      clearInterval(timer)
      isSending.value = false
    }
  }, 1000)
}

// 处理登录
function handleLogin() {
  loginFormRef.value.validate((valid) => {
    if (!valid) return
    
    isLoading.value = true
    
    // 模拟登录验证
    setTimeout(() => {
      const mockUsers = [
        { email: 'test@example.com', password: '123456' }
      ]
      
      let loginSuccess = false
      
      if (loginType.value === 'password') {
        // 密码登录验证
        loginSuccess = mockUsers.some(user => 
          user.email === loginForm.email && user.password === loginForm.password
        )
      } else {
        // 验证码登录 - 模拟验证码为 '123456'
        loginSuccess = loginForm.email === 'test@example.com' && loginForm.code === '123456'
      }
      
      if (loginSuccess) {
        ElMessage.success('登录成功')
        // 存储登录状态
        localStorage.setItem('isLoggedIn', 'true')
        localStorage.setItem('userEmail', loginForm.email)
        // 跳转到首页
        router.push('/')
      } else {
        if (loginType.value === 'password') {
          ElMessage.error('邮箱或密码错误')
        } else {
          ElMessage.error('邮箱或验证码错误')
        }
      }
      
      isLoading.value = false
    }, 1000)
  })
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px;
  background-color: var(--bg-color);
}

.login-card {
  width: 100%;
  max-width: 440px;
  padding: 40px;
  border-radius: 16px;
  background-color: var(--card-bg);
  box-shadow: 0 8px 24px var(--shadow-color);
}

.login-header {
  text-align: center;
  margin-bottom: 30px;
}

.login-title {
  font-size: 2rem;
  color: var(--primary-color);
  margin-bottom: 8px;
  font-family: 'Ma Shan Zheng', cursive;
}

.login-subtitle {
  font-size: 1rem;
  color: var(--text-secondary);
}

.login-tabs {
  margin-bottom: 15px;
}

.verification-code-container {
  display: flex;
  gap: 10px;
}

.verification-input {
  flex: 1;
}

.send-code-btn {
  width: 120px;
}

.login-btn {
  width: 100%;
  margin-top: 10px;
  height: 44px;
  font-size: 1.1rem;
}

.form-footer {
  margin-top: 20px;
  text-align: center;
  color: var(--text-secondary);
}

.register-link {
  color: var(--primary-color);
  text-decoration: none;
  margin-left: 5px;
}

.register-link:hover {
  text-decoration: underline;
}

@media (max-width: 480px) {
  .login-card {
    padding: 30px 20px;
  }
}
</style> 