<template>
  <div class="register-container">
    <div class="register-card">
      <div class="register-header">
        <h2 class="register-title">创建账号</h2>
        <p class="register-subtitle">注册一个新账号加入我们</p>
      </div>

      <el-form :model="registerForm" :rules="registerRules" ref="registerFormRef" label-position="top">
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="registerForm.email" placeholder="请输入邮箱地址" type="email">
            <template #prefix>
              <el-icon><Message /></el-icon>
            </template>
          </el-input>
        </el-form-item>

        <el-form-item label="验证码" prop="code">
          <div class="verification-code-container">
            <el-input v-model="registerForm.code" placeholder="请输入验证码" class="verification-input">
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

        <el-form-item label="密码" prop="password">
          <el-input 
            v-model="registerForm.password" 
            placeholder="请设置密码" 
            type="password" 
            show-password
          >
            <template #prefix>
              <el-icon><Lock /></el-icon>
            </template>
          </el-input>
        </el-form-item>

        <el-form-item label="确认密码" prop="confirmPassword">
          <el-input 
            v-model="registerForm.confirmPassword" 
            placeholder="请确认密码" 
            type="password" 
            show-password
          >
            <template #prefix>
              <el-icon><Lock /></el-icon>
            </template>
          </el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" class="register-btn" @click="handleRegister" :loading="isLoading">
            注 册
          </el-button>
        </el-form-item>

        <div class="form-footer">
          <span>已有账号？</span>
          <router-link to="/login" class="login-link">立即登录</router-link>
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
const registerFormRef = ref(null)
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
const registerForm = reactive({
  email: '',
  code: '',
  password: '',
  confirmPassword: ''
})

// 自定义验证密码是否一致
const validatePass = (rule, value, callback) => {
  if (value === '') {
    callback(new Error('请再次输入密码'))
  } else if (value !== registerForm.password) {
    callback(new Error('两次输入密码不一致!'))
  } else {
    callback()
  }
}

// 表单验证规则
const registerRules = reactive({
  email: [
    { required: true, message: '请输入邮箱地址', trigger: 'blur' },
    { type: 'email', message: '请输入有效的邮箱地址', trigger: 'blur' }
  ],
  code: [
    { required: true, message: '请输入验证码', trigger: 'blur' },
    { min: 6, max: 6, message: '验证码长度应为6位', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请设置密码', trigger: 'blur' },
    { min: 6, message: '密码长度至少为6个字符', trigger: 'blur' }
  ],
  confirmPassword: [
    { required: true, message: '请确认密码', trigger: 'blur' },
    { validator: validatePass, trigger: 'blur' }
  ]
})

// 发送验证码
function sendVerificationCode() {
  if (!registerForm.email) {
    ElMessage.warning('请先输入邮箱地址')
    return
  }
  
  // 模拟发送验证码
  isSending.value = true
  countdown.value = 60
  
  ElMessage.success(`验证码已发送至${registerForm.email}`)
  
  // 倒计时
  const timer = setInterval(() => {
    countdown.value--
    if (countdown.value <= 0) {
      clearInterval(timer)
      isSending.value = false
    }
  }, 1000)
}

// 处理注册
function handleRegister() {
  registerFormRef.value.validate((valid) => {
    if (!valid) return
    
    isLoading.value = true
    
    // 模拟注册过程
    setTimeout(() => {
      // 模拟验证码为 '123456'
      if (registerForm.code !== '123456') {
        ElMessage.error('验证码错误')
        isLoading.value = false
        return
      }
      
      // 模拟注册成功
      ElMessage.success('注册成功，正在跳转登录页面')
      
      // 清空表单
      registerFormRef.value.resetFields()
      
      // 跳转到登录页
      setTimeout(() => {
        router.push('/login')
      }, 1500)
      
      isLoading.value = false
    }, 1000)
  })
}
</script>

<style scoped>
.register-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px;
  background-color: var(--bg-color);
}

.register-card {
  width: 100%;
  max-width: 440px;
  padding: 40px;
  border-radius: 16px;
  background-color: var(--card-bg);
  box-shadow: 0 8px 24px var(--shadow-color);
}

.register-header {
  text-align: center;
  margin-bottom: 30px;
}

.register-title {
  font-size: 2rem;
  color: var(--primary-color);
  margin-bottom: 8px;
  font-family: 'Ma Shan Zheng', cursive;
}

.register-subtitle {
  font-size: 1rem;
  color: var(--text-secondary);
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

.register-btn {
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

.login-link {
  color: var(--primary-color);
  text-decoration: none;
  margin-left: 5px;
}

.login-link:hover {
  text-decoration: underline;
}

@media (max-width: 480px) {
  .register-card {
    padding: 30px 20px;
  }
}
</style> 