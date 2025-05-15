<template>
  <div id="app" :class="{ 'dark-theme': isDarkTheme }">
    <nav class="navbar">
      <div class="navbar-logo">
        <span class="logo-icon">♥</span> 我们的家
      </div>
      
      <div class="navbar-center">
        <button 
          @click="changeTab('home')" 
          :class="['nav-btn', { active: activeTab === 'home' }]"
        >
          <span class="btn-icon">🏠</span>
          <span class="btn-text">首页</span>
        </button>
        <button 
          @click="changeTab('timeline')" 
          :class="['nav-btn', { active: activeTab === 'timeline' }]"
        >
          <span class="btn-icon">📅</span>
          <span class="btn-text">时光轴</span>
        </button>
      </div>
      
      <div class="navbar-right">
        <button class="theme-btn" @click="toggleTheme">
          {{ isDarkTheme ? '☀️' : '🌙' }}
        </button>
        <button class="logout-btn" @click="logout">
          <span class="btn-icon">🚪</span>
          <span class="btn-text">退出</span>
        </button>
      </div>
    </nav>
    
    <!-- 首页视频 - 全屏 -->
    <HomeVideo v-if="activeTab === 'home'" />
    
    <!-- 时间线内容 -->
    <main class="main-content" v-if="activeTab === 'timeline'">
      <TimeLine />
      
      <footer>
        <div class="footer-content">
          <p>© 2024 我们的家 · 用心记录每一刻</p>
        </div>
      </footer>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRouter } from 'vue-router'
import TimeLine from './TimeLine.vue'
import HomeVideo from './HomeVideo.vue'
import { ElMessageBox } from 'element-plus'

const router = useRouter()

// 主题控制
const isDarkTheme = ref(false)
// 当前标签
const activeTab = ref('home')

// 切换标签
function changeTab(tab) {
  activeTab.value = tab
  window.scrollTo(0, 0) // 切换标签时滚动到顶部
}

// 初始化主题
onMounted(() => {
  // 检查本地存储中是否有主题设置
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme) {
    isDarkTheme.value = savedTheme === 'dark'
  } else {
    // 如果没有存储的主题，检查系统偏好
    const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches
    isDarkTheme.value = prefersDark
  }
})

// 切换主题
function toggleTheme() {
  isDarkTheme.value = !isDarkTheme.value
  // 保存主题设置到本地存储
  localStorage.setItem('theme', isDarkTheme.value ? 'dark' : 'light')
}

// 监听主题变化并更新文档根元素
watch(isDarkTheme, (newValue) => {
  document.documentElement.classList.toggle('dark-theme', newValue)
})

// 退出登录
function logout() {
  ElMessageBox.confirm('确定要退出登录吗?', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  }).then(() => {
    // 清除登录状态
    localStorage.removeItem('isLoggedIn')
    localStorage.removeItem('userEmail')
    // 跳转到登录页
    router.push('/login')
  }).catch(() => {
    // 取消操作
  })
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Ma+Shan+Zheng&family=Noto+Serif+SC:wght@300;400;500;700&display=swap');

:root {
  --primary-color: #7a98b8;
  --accent-color: #e8a87c;
  --bg-color: #f9f9f9;
  --card-bg: #ffffff;
  --text-color: #333;
  --text-secondary: #666;
  --border-color: #eaeaea;
  --shadow-color: rgba(0,0,0,0.08);
  --header-bg: rgba(255, 255, 255, 0.95);
}

.dark-theme {
  --primary-color: #8da9c4;
  --accent-color: #f1b894;
  --bg-color: #121212;
  --card-bg: #1e1e1e;
  --text-color: #f0f0f0;
  --text-secondary: #aaaaaa;
  --border-color: #333333;
  --shadow-color: rgba(0,0,0,0.3);
  --header-bg: rgba(30, 30, 30, 0.95);
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html, body {
  margin: 0;
  padding: 0;
  overflow-x: hidden;
  font-family: 'Noto Serif SC', serif;
  background-color: var(--bg-color);
  color: var(--text-color);
  line-height: 1.6;
  transition: background-color 0.3s ease, color 0.3s ease;
}

#app {
  width: 100vw;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  position: relative;
}

/* 导航栏样式 */
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(10px);
  padding: 8px 15px;
  height: 60px;
}

.navbar-logo {
  font-size: 1.8rem;
  font-weight: 700;
  color: #ffffff;
  display: flex;
  align-items: center;
  font-family: 'Ma Shan Zheng', cursive;
  text-shadow: 0 2px 5px rgba(0,0,0,0.2);
  width: 33%;
  min-width: 150px;
}

.logo-icon {
  color: var(--accent-color);
  margin-right: 8px;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.1); }
  100% { transform: scale(1); }
}

.navbar-center {
  display: flex;
  justify-content: center;
  gap: 10px;
  width: 34%;
}

.navbar-right {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
  width: 33%;
  min-width: 100px;
}

.nav-btn, .theme-btn, .logout-btn {
  background: rgba(255, 255, 255, 0.15);
  border: none;
  color: white;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  padding: 6px 12px;
  border-radius: 20px;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 6px;
}

.nav-btn:hover, .theme-btn:hover, .logout-btn:hover {
  background: rgba(255, 255, 255, 0.25);
  transform: translateY(-2px);
}

.nav-btn.active {
  background-color: var(--primary-color);
}

.theme-btn {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.1rem;
  padding: 0;
  border-radius: 50%;
}

.logout-btn {
  background-color: rgba(255, 100, 100, 0.15);
}

.logout-btn:hover {
  background-color: rgba(255, 100, 100, 0.25);
}

.btn-icon {
  font-size: 1.1rem;
}

.main-content {
  flex: 1;
  width: 100%;
  padding-top: 80px;
  display: flex;
  flex-direction: column;
}

footer {
  padding: 20px;
  background-color: var(--card-bg);
  box-shadow: 0 -4px 20px rgba(0,0,0,0.05);
  text-align: center;
  margin-top: auto;
}

.footer-content {
  max-width: 1200px;
  margin: 0 auto;
  color: var(--text-secondary);
  font-size: 0.9rem;
}

/* 响应式调整 */
@media (max-width: 600px) {
  .navbar-logo {
    min-width: 50px;
    font-size: 1.5rem;
    width: 20%;
  }
  
  .navbar-center {
    width: 50%;
    justify-content: center;
  }
  
  .navbar-right {
    width: 30%;
  }
  
  .btn-text {
    display: none;
  }
  
  .nav-btn {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    padding: 0;
    justify-content: center;
  }
}
</style> 