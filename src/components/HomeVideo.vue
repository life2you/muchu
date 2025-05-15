<template>
  <div class="home-video-container">
    <video 
      class="background-video" 
      ref="videoRef"
      autoplay 
      loop 
      playsinline
      :src="videoSrc"
    ></video>
    <div class="content-overlay">
      <div class="video-title">我们的家</div>
      <div class="video-subtitle">珍藏每一个家庭时刻</div>
      <button class="sound-control" @click="toggleSound">
        <span v-if="isMuted">🔇</span>
        <span v-else>🔊</span>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// 视频源
const videoSrc = ref(new URL('../assets/main.MP4', import.meta.url).href);
const videoRef = ref(null);
const isMuted = ref(true); // 默认静音，因为大多数浏览器不允许自动播放带声音的视频

// 切换声音
function toggleSound() {
  if (videoRef.value) {
    videoRef.value.muted = !videoRef.value.muted;
    isMuted.value = videoRef.value.muted;
  }
}

// 组件挂载后初始化视频
onMounted(() => {
  // 由于浏览器策略限制，我们先设置为静音，让用户通过点击按钮来开启声音
  if (videoRef.value) {
    videoRef.value.muted = true;
    videoRef.value.play().catch(e => {
      console.log('视频自动播放失败，这在某些浏览器中是正常的:', e);
    });
  }
});
</script>

<style scoped>
.home-video-container {
  position: fixed;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  overflow: hidden;
  z-index: 1;
}

.background-video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: 1;
}

.content-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 2;
  background: rgba(0, 0, 0, 0.3);
  color: white;
  text-align: center;
  padding-top: 60px; /* 为顶部导航留出空间 */
}

.video-title {
  font-size: 5rem;
  font-weight: 700;
  margin-bottom: 1rem;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.7);
  font-family: 'Ma Shan Zheng', cursive;
}

.video-subtitle {
  font-size: 1.8rem;
  max-width: 800px;
  margin: 0 auto;
  margin-bottom: 2rem;
  text-shadow: 0 2px 5px rgba(0, 0, 0, 0.7);
}

.sound-control {
  position: absolute;
  bottom: 30px;
  right: 30px;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.15);
  border: none;
  color: white;
  font-size: 1.8rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  backdrop-filter: blur(5px);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
  z-index: 10;
}

.sound-control:hover {
  transform: scale(1.1);
  background: rgba(255, 255, 255, 0.25);
}

@media (max-width: 768px) {
  .video-title {
    font-size: 3.5rem;
  }
  
  .video-subtitle {
    font-size: 1.4rem;
  }
  
  .sound-control {
    width: 50px;
    height: 50px;
    font-size: 1.5rem;
    bottom: 20px;
    right: 20px;
  }
}

@media (max-width: 480px) {
  .video-title {
    font-size: 2.5rem;
  }
  
  .video-subtitle {
    font-size: 1.2rem;
  }
  
  .sound-control {
    width: 45px;
    height: 45px;
    font-size: 1.3rem;
    bottom: 15px;
    right: 15px;
  }
}
</style> 