<template>
  <div id="app" :class="{ 'dark-theme': isDarkTheme }">
    <header>
      <div class="logo">
        <span class="logo-icon">♥</span> 我们的家
      </div>
      <nav>
        <a href="#timeline" class="nav-link">时光轴</a>
        <a href="#photos" class="nav-link">相册</a>
        <a href="#videos" class="nav-link">视频</a>
        <button class="theme-toggle" @click="toggleTheme">
          {{ isDarkTheme ? '☀️' : '🌙' }}
        </button>
      </nav>
    </header>
    
    <div class="hero">
      <div class="hero-content">
        <h1>珍藏时光</h1>
        <p>记录家庭的美好瞬间与成长轨迹</p>
        <a href="#timeline" class="cta-button">浏览时间线</a>
      </div>
    </div>
    
    <section id="timeline" class="timeline-section">
      <TimeLine />
    </section>
    
    <div class="additional-sections-hint">
      <div class="hint-container">
        <div class="hint-icon">📷</div>
        <p>您还可以浏览<a href="#photos" class="section-link">照片集</a>和<a href="#videos" class="section-link">视频集</a>来查看更多精彩瞬间</p>
      </div>
    </div>
    
    <section id="photos" class="section-hidden">
      <PhotoGallery />
    </section>
    
    <section id="videos" class="section-hidden">
      <VideoGallery />
    </section>
    
    <footer>
      <div class="footer-content">
        <p>© 2024 我们的家 · 用心记录每一刻</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import ImageGallery from './components/ImageGallery.vue'
import VideoGallery from './components/VideoGallery.vue'
import PhotoGallery from './components/PhotoGallery.vue'
import TimeLine from './components/TimeLine.vue'

// 主题控制
const isDarkTheme = ref(false)

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
  
  // 滚动处理
  const handleHash = () => {
    const hash = window.location.hash;
    if (hash) {
      setTimeout(() => {
        const element = document.querySelector(hash);
        if (element) {
          element.scrollIntoView({ behavior: 'smooth' });
        }
      }, 100);
    }
  };
  
  window.addEventListener('hashchange', handleHash);
  if (window.location.hash) {
    handleHash();
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
  padding: 15px 10%;
  background-color: var(--header-bg);
  box-shadow: 0 2px 15px var(--shadow-color);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
  backdrop-filter: blur(10px);
}

.logo {
  font-size: 1.8rem;
  font-weight: 700;
  color: var(--primary-color);
  display: flex;
  align-items: center;
  font-family: 'Ma Shan Zheng', cursive;
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

nav {
  display: flex;
  gap: 20px;
  align-items: center;
}

.nav-link {
  text-decoration: none;
  color: var(--text-color);
  font-weight: 500;
  transition: all 0.3s ease;
  padding: 8px 16px;
  border-radius: 20px;
  position: relative;
  letter-spacing: 0.5px;
}

.nav-link::after {
  content: '';
  position: absolute;
  width: 0;
  height: 2px;
  bottom: 0;
  left: 50%;
  background-color: var(--primary-color);
  transition: all 0.3s ease;
  transform: translateX(-50%);
}

.nav-link:hover::after {
  width: 70%;
}

.nav-link:hover {
  color: var(--primary-color);
}

.theme-toggle {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  background-color: var(--card-bg);
  box-shadow: 0 2px 8px var(--shadow-color);
}

.theme-toggle:hover {
  transform: rotate(45deg);
}

.hero {
  padding: 220px 10% 180px;
  text-align: center;
  background: linear-gradient(rgba(0,0,0,0.3), rgba(0,0,0,0.3)), url(./assets/WechatIMG4.jpg);
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  color: white;
  position: relative;
  margin-top: 0;
}

.hero::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: url("data:image/svg+xml,%3Csvg width='20' height='20' viewBox='0 0 20 20' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='%23ffffff' fill-opacity='0.1' fill-rule='evenodd'%3E%3Ccircle cx='3' cy='3' r='3'/%3E%3Ccircle cx='13' cy='13' r='3'/%3E%3C/g%3E%3C/svg%3E");
  z-index: 1;
}

.hero-content {
  position: relative;
  z-index: 2;
  max-width: 800px;
  margin: 0 auto;
}

.hero h1 {
  font-size: 4.5rem;
  margin-bottom: 1.5rem;
  font-family: 'Ma Shan Zheng', cursive;
  text-shadow: 0 2px 10px rgba(0,0,0,0.5);
  letter-spacing: 3px;
}

.hero p {
  font-size: 1.5rem;
  max-width: 600px;
  margin: 0 auto 40px;
  opacity: 0.9;
}

.cta-button {
  display: inline-block;
  padding: 15px 40px;
  background-color: var(--accent-color);
  color: white;
  text-decoration: none;
  border-radius: 50px;
  font-weight: 500;
  font-size: 1.1rem;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0,0,0,0.2);
  letter-spacing: 1px;
}

.cta-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 20px rgba(0,0,0,0.3);
}

.timeline-section {
  background-color: var(--bg-color);
  padding: 80px 0;
  position: relative;
}

.additional-sections-hint {
  text-align: center;
  padding: 60px 20px;
  background-color: var(--card-bg);
}

.hint-container {
  max-width: 600px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
}

.hint-icon {
  font-size: 2.5rem;
  color: var(--primary-color);
}

.hint-container p {
  font-size: 1.1rem;
  color: var(--text-secondary);
  line-height: 1.6;
}

.section-link {
  color: var(--primary-color);
  text-decoration: none;
  transition: all 0.2s ease;
  font-weight: 500;
}

.section-link:hover {
  text-decoration: underline;
}

.section-hidden {
  padding: 80px 0;
  background-color: var(--bg-color);
  scroll-margin-top: 80px;
}

.section-hidden:nth-child(even) {
  background-color: var(--card-bg);
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
  
  .hero {
    padding: 180px 5% 120px;
  }
  
  .hero h1 {
    font-size: 3rem;
  }
  
  .hero p {
    font-size: 1.2rem;
  }
  
  .cta-button {
    padding: 12px 30px;
    font-size: 1rem;
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
  
  .nav-link {
    padding: 6px 12px;
    font-size: 0.9rem;
  }
  
  .theme-toggle {
    width: 35px;
    height: 35px;
    font-size: 1.2rem;
  }
  
  .hero {
    padding: 150px 4% 100px;
  }
  
  .hero h1 {
    font-size: 2.5rem;
    margin-bottom: 1rem;
    letter-spacing: 2px;
  }
  
  .hero p {
    font-size: 1rem;
    margin: 0 auto 30px;
  }
  
  .cta-button {
    padding: 10px 25px;
    font-size: 0.9rem;
  }
  
  .timeline-section {
    padding: 60px 0;
  }
  
  .section-hidden {
    padding: 60px 0;
  }
  
  .additional-sections-hint {
    padding: 40px 15px;
  }
  
  .hint-icon {
    font-size: 2rem;
  }
  
  .hint-container p {
    font-size: 0.9rem;
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
  
  .nav-link {
    padding: 5px 10px;
    font-size: 0.85rem;
  }
  
  .hero h1 {
    font-size: 2.2rem;
  }
  
  .hero p {
    font-size: 0.9rem;
  }
  
  .cta-button {
    padding: 8px 20px;
  }
}
</style>
