<template>
  <router-view />
</template>

<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  // 检查是否有主题设置
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark') {
    document.documentElement.classList.add('dark-theme')
  }
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
</style>
