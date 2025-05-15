<template>
  <div id="app" :class="{ 'dark-theme': isDarkTheme }">
    <header>
      <div class="logo">
        <span class="logo-icon">♥</span> 我们的家
      </div>
      <nav>
        <button 
          @click="changeTab('home')" 
          :class="['tab-btn', { active: activeTab === 'home' }]"
        >
          <span class="btn-icon">🏠</span>
          <span class="btn-text">首页</span>
        </button>
        <button 
          @click="changeTab('timeline')" 
          :class="['tab-btn', { active: activeTab === 'timeline' }]"
        >
          <span class="btn-icon">📅</span>
          <span class="btn-text">时光轴</span>
        </button>
        <button class="theme-toggle" @click="toggleTheme">
          {{ isDarkTheme ? '☀️' : '🌙' }}
        </button>
      </nav>
    </header>
    
    <main class="main-content">
      <!-- 首页视频 -->
      <div v-if="activeTab === 'home'" class="tab-content">
        <HomeVideo />
      </div>
      
      <!-- 时间线 -->
      <div v-if="activeTab === 'timeline'" class="tab-content">
        <TimeLine />
      </div>
    </main>
    
    <footer>
      <div class="footer-content">
        <p>© 2024 我们的家 · 用心记录每一刻</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import TimeLine from './components/TimeLine.vue'
import HomeVideo from './components/HomeVideo.vue'

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
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Ma+Shan+Zheng&family=Noto+Serif+SC:wght@300;400;500;700&display=swap');

:root {
  /* 亮色主题（默认） */
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
  /* 暗色主题 */
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

html {
  scroll-behavior: smooth;
  font-size: 16px;
}

body {
  margin: 0;
  font-family: 'Noto Serif SC', serif;
  background-color: var(--bg-color);
  color: var(--text-color);
  line-height: 1.6;
  transition: background-color 0.3s ease, color 0.3s ease;
}

#app {
  width: 100%;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 10%;
  background-color: var(--header-bg);
  box-shadow: 0 4px 20px var(--shadow-color);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
  backdrop-filter: blur(10px);
}

.logo {
  font-size: 2.2rem;
  font-weight: 700;
  color: var(--primary-color);
  display: flex;
  align-items: center;
  font-family: 'Ma Shan Zheng', cursive;
  text-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.logo-icon {
  color: var(--accent-color);
  margin-right: 12px;
  animation: pulse 2s infinite;
  font-size: 2.4rem;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.1); }
  100% { transform: scale(1); }
}

nav {
  display: flex;
  gap: 25px;
  align-items: center;
}

.tab-btn {
  background: rgba(255, 255, 255, 0.15);
  border: none;
  color: var(--text-color);
  font-size: 1.15rem;
  font-weight: 500;
  cursor: pointer;
  padding: 12px 28px;
  border-radius: 30px;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  position: relative;
  overflow: hidden;
  z-index: 1;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.08);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  backdrop-filter: blur(5px);
}

.tab-btn::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
  transition: left 0.7s ease;
  z-index: -1;
}

.tab-btn:hover {
  color: var(--primary-color);
  transform: translateY(-3px);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.12);
  background: rgba(255, 255, 255, 0.25);
}

.tab-btn:hover::before {
  left: 100%;
}

.tab-btn.active {
  background: linear-gradient(135deg, var(--primary-color), var(--primary-color) 60%, rgba(138, 168, 198, 0.9));
  color: white;
  box-shadow: 0 8px 25px rgba(122, 152, 184, 0.5);
  transform: translateY(-2px);
}

.btn-icon {
  font-size: 1.4rem;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.3s ease;
}

.btn-text {
  font-weight: 600;
  letter-spacing: 0.5px;
}

.tab-btn:hover .btn-icon {
  transform: scale(1.2);
}

.tab-btn.active .btn-icon {
  animation: iconPulse 1.5s infinite alternate;
}

@keyframes iconPulse {
  0% { transform: scale(1); }
  100% { transform: scale(1.2); }
}

.theme-toggle {
  background: rgba(255, 255, 255, 0.15);
  border: none;
  font-size: 1.8rem;
  cursor: pointer;
  width: 65px;
  height: 65px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.12);
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(5px);
}

.theme-toggle::after {
  content: '';
  position: absolute;
  width: 150%;
  height: 150%;
  border-radius: 50%;
  background: radial-gradient(circle, transparent 60%, rgba(255,255,255,0.1) 100%);
  top: -25%;
  left: -25%;
  opacity: 0;
  transition: opacity 0.4s ease;
  pointer-events: none;
}

.theme-toggle:hover {
  transform: rotate(25deg) scale(1.15);
  box-shadow: 0 12px 30px rgba(0, 0, 0, 0.15);
  background: rgba(255, 255, 255, 0.25);
}

.theme-toggle:hover::after {
  opacity: 1;
}

.dark-theme .tab-btn {
  background: rgba(30, 30, 30, 0.5);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.15);
}

.dark-theme .tab-btn:hover {
  background: rgba(40, 40, 40, 0.7);
}

.dark-theme .theme-toggle {
  background: rgba(30, 30, 30, 0.5);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
}

.dark-theme .theme-toggle:hover {
  background: rgba(40, 40, 40, 0.7);
}

.main-content {
  margin-top: 80px;
  flex: 1;
  display: flex;
  flex-direction: column;
}

.tab-content {
  width: 100%;
  flex: 1;
}

footer {
  background-color: var(--header-bg);
  color: var(--text-color);
  padding: 30px 0;
  margin-top: auto;
  border-top: 1px solid var(--border-color);
  text-align: center;
  font-size: 0.95rem;
}

.footer-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

@media (max-width: 768px) {
  html {
    font-size: 14px;
  }
  
  header {
    flex-direction: column;
    gap: 16px;
    padding: 15px 5%;
  }
  
  nav {
    width: 100%;
    justify-content: center;
    flex-wrap: wrap;
  }
  
  .main-content {
    margin-top: 130px;
  }
}

/* 添加更精细的移动设备适配 */
@media (max-width: 480px) {
  html {
    font-size: 13px;
  }
  
  header {
    padding: 12px 4%;
    gap: 12px;
  }
  
  .logo {
    font-size: 1.5rem;
  }
  
  nav {
    gap: 8px;
  }
  
  .tab-btn {
    padding: 6px 12px;
    font-size: 0.9rem;
  }
  
  .theme-toggle {
    width: 35px;
    height: 35px;
    font-size: 1.2rem;
  }
  
  footer {
    padding: 20px 0;
    font-size: 0.85rem;
  }
}

/* 竖屏手机特别处理 */
@media (max-width: 380px) {
  header {
    padding: 10px 3%;
  }
  
  .logo {
    font-size: 1.3rem;
  }
  
  .tab-btn {
    padding: 5px 10px;
    font-size: 0.85rem;
  }
  
  .main-content {
    margin-top: 110px;
  }
}
</style>
