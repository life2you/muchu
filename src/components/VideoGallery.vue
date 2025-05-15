<template>
  <div class="video-gallery">
    <h2 class="section-title">家庭<span>珍贵视频</span></h2>
    <p class="section-desc">记录家庭中那些难忘瞬间和欢乐时光</p>
    
    <div class="video-memory-highlight">
      <div class="memory-text">
        <div class="memory-icon">💝</div>
        <h3>家庭视频记忆</h3>
        <p>视频是家庭记忆中最生动的部分，它记录了孩子成长的每一步、家庭活动的欢声笑语和一起度过的美好时光。在这里珍藏您最珍贵的回忆。</p>
      </div>
    </div>
    
    <div class="video-grid">
      <div 
        v-for="(video, index) in videos" 
        :key="index" 
        class="video-card"
        @click="openVideo(video.src)"
      >
        <!-- 视频缩略图 -->
        <div class="thumbnail-container">
          <video :src="video.src" preload="metadata" class="video-thumbnail"></video>
          <div class="video-duration">{{ video.duration }}</div>
          <div class="play-icon">▶</div>
        </div>
        <div class="video-info">
          <h3>{{ video.title }}</h3>
          <div class="video-meta">
            <span class="video-date">{{ video.date }}</span>
            <span class="video-tag">{{ video.tag }}</span>
          </div>
          <p class="video-desc">{{ video.description }}</p>
        </div>
      </div>
    </div>
    
    <!-- 视频添加提示 -->
    <div class="video-upload-tip">
      <div class="tip-content">
        <div class="tip-icon">📹</div>
        <p>想要添加更多家庭视频？<br>只需点击右上角上传按钮，即可添加您的珍贵时刻。</p>
      </div>
    </div>
    
    <!-- 视频播放器 -->
    <div v-if="isPlayerOpen" class="video-player-modal">
      <div class="video-player-container">
        <button class="close-player" @click="closePlayer">×</button>
        <video 
          ref="player" 
          :src="currentVideo" 
          controls 
          autoplay 
          class="full-player"
        ></video>
        <div class="video-player-info">
          <h3>{{ currentVideoInfo?.title || '家庭视频' }}</h3>
          <p>{{ currentVideoInfo?.description || '' }}</p>
        </div>
      </div>
      <div class="modal-overlay" @click="closePlayer"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// 视频列表
const videoList = [
  {
    id: 1,
    src: new URL('../assets/CZYE5524.MP4', import.meta.url).href,
    title: '家庭欢乐时光',
    date: '2024年6月10日',
    tag: '家庭活动',
    duration: '0:35',
    description: '记录我们家庭聚会的欢乐时刻，每个人都乐在其中！'
  },
  // 示例视频（使用相同视频但展示不同信息）
  {
    id: 2,
    src: new URL('../assets/CZYE5524.MP4', import.meta.url).href,
    title: '孩子的成长记录',
    date: '2024年5月5日',
    tag: '成长记录',
    duration: '0:35',
    description: '孩子成长的珍贵视频记录，每一步都令人感动。'
  }
];

const videos = ref(videoList);
const isPlayerOpen = ref(false);
const currentVideo = ref('');
const currentVideoId = ref(null);
const player = ref(null);

// 获取当前播放视频的信息
const currentVideoInfo = computed(() => {
  if (!currentVideoId.value) return null;
  return videoList.find(video => video.src === currentVideoId.value);
});

function openVideo(src) {
  currentVideo.value = src;
  currentVideoId.value = src;
  isPlayerOpen.value = true;
  document.body.style.overflow = 'hidden';
}

function closePlayer() {
  if (player.value) {
    player.value.pause();
  }
  isPlayerOpen.value = false;
  document.body.style.overflow = 'auto';
}
</script>

<style scoped>
.video-gallery {
  width: 100%;
  max-width: 1400px;
  margin: 0 auto;
  padding: 20px;
}

.section-title {
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 10px;
  color: var(--text-color);
  font-family: 'Ma Shan Zheng', cursive;
}

.section-title span {
  color: var(--primary-color);
}

.section-desc {
  text-align: center;
  font-size: 1.1rem;
  color: var(--text-secondary);
  margin-bottom: 30px;
}

.video-memory-highlight {
  background: linear-gradient(135deg, rgba(106, 140, 175, 0.1), rgba(255, 157, 125, 0.1));
  border-radius: 15px;
  padding: 30px;
  margin-bottom: 40px;
  border: 1px solid var(--border-color);
}

.memory-text {
  max-width: 800px;
  margin: 0 auto;
  text-align: center;
}

.memory-icon {
  font-size: 3rem;
  margin-bottom: 15px;
  color: var(--accent-color);
}

.memory-text h3 {
  font-size: 1.6rem;
  color: var(--text-color);
  margin-bottom: 15px;
  font-family: 'Ma Shan Zheng', cursive;
}

.memory-text p {
  font-size: 1.05rem;
  color: var(--text-secondary);
  line-height: 1.7;
}

.video-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 30px;
  margin: 0 auto;
}

.video-card {
  background: var(--card-bg);
  border-radius: 15px;
  overflow: hidden;
  box-shadow: 0 5px 15px var(--shadow-color);
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  height: 100%;
  border: 1px solid var(--border-color);
}

.video-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 12px 25px var(--shadow-color);
}

.thumbnail-container {
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 56.25%; /* 16:9 比例 */
  overflow: hidden;
}

.video-thumbnail {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.video-duration {
  position: absolute;
  bottom: 10px;
  right: 10px;
  background: rgba(0, 0, 0, 0.6);
  color: white;
  font-size: 0.8rem;
  padding: 3px 8px;
  border-radius: 4px;
  z-index: 2;
}

.play-icon {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 60px;
  height: 60px;
  background: rgba(255,255,255,0.2);
  backdrop-filter: blur(5px);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 24px;
  opacity: 0.8;
  transition: all 0.3s ease;
}

.video-card:hover .play-icon {
  background: var(--primary-color);
  opacity: 1;
  transform: translate(-50%, -50%) scale(1.1);
}

.video-info {
  padding: 20px;
  flex-grow: 1;
  display: flex;
  flex-direction: column;
}

.video-info h3 {
  margin: 0 0 10px;
  font-size: 1.3rem;
  color: var(--text-color);
}

.video-meta {
  display: flex;
  justify-content: space-between;
  margin-bottom: 12px;
}

.video-date {
  font-size: 0.85rem;
  color: var(--text-secondary);
}

.video-tag {
  background-color: var(--primary-color);
  color: white;
  font-size: 0.75rem;
  padding: 2px 8px;
  border-radius: 12px;
}

.video-desc {
  margin: 0;
  font-size: 0.95rem;
  color: var(--text-secondary);
  line-height: 1.6;
  flex-grow: 1;
}

.video-upload-tip {
  margin-top: 50px;
  text-align: center;
}

.tip-content {
  display: inline-block;
  background-color: var(--card-bg);
  border-radius: 12px;
  padding: 20px 30px;
  box-shadow: 0 5px 15px var(--shadow-color);
  border: 1px dashed var(--accent-color);
}

.tip-icon {
  font-size: 2.5rem;
  margin-bottom: 10px;
  color: var(--accent-color);
}

.tip-content p {
  margin: 0;
  color: var(--text-color);
  font-size: 1rem;
  line-height: 1.6;
}

/* 视频播放器模态框 */
.video-player-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.85);
  backdrop-filter: blur(5px);
}

.video-player-container {
  position: relative;
  z-index: 10;
  width: 80%;
  max-width: 1000px;
  display: flex;
  flex-direction: column;
  animation: fadeIn 0.3s ease;
}

.full-player {
  width: 100%;
  border-radius: 8px 8px 0 0;
  box-shadow: 0 0 30px rgba(0, 0, 0, 0.5);
  aspect-ratio: 16/9;
}

.video-player-info {
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  padding: 15px 20px;
  border-radius: 0 0 8px 8px;
}

.video-player-info h3 {
  margin: 0 0 8px;
  font-size: 1.3rem;
}

.video-player-info p {
  margin: 0;
  font-size: 0.95rem;
  opacity: 0.9;
}

.close-player {
  position: absolute;
  top: -40px;
  right: -40px;
  width: 40px;
  height: 40px;
  background: transparent;
  border: none;
  color: white;
  font-size: 2rem;
  cursor: pointer;
  transition: all 0.3s ease;
  z-index: 20;
}

.close-player:hover {
  transform: rotate(90deg);
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 768px) {
  .section-title {
    font-size: 2rem;
  }
  
  .video-grid {
    grid-template-columns: 1fr;
  }
  
  .video-player-container {
    width: 95%;
  }
  
  .close-player {
    top: -35px;
    right: 0;
  }
  
  .memory-text h3 {
    font-size: 1.4rem;
  }
  
  .memory-text p {
    font-size: 1rem;
  }
}

/* 添加更精细的移动设备适配 */
@media (max-width: 480px) {
  .video-gallery {
    padding: 15px 10px;
  }
  
  .section-title {
    font-size: 1.8rem;
    margin-bottom: 5px;
  }
  
  .section-desc {
    font-size: 0.95rem;
    margin-bottom: 20px;
  }
  
  .video-memory-highlight {
    padding: 20px 15px;
    margin-bottom: 25px;
  }
  
  .memory-icon {
    font-size: 2.5rem;
    margin-bottom: 10px;
  }
  
  .memory-text h3 {
    font-size: 1.3rem;
    margin-bottom: 10px;
  }
  
  .memory-text p {
    font-size: 0.9rem;
    line-height: 1.6;
  }
  
  .video-card {
    margin-bottom: 15px;
  }
  
  .video-info {
    padding: 12px;
  }
  
  .video-info h3 {
    font-size: 1.1rem;
    margin-bottom: 8px;
  }
  
  .video-meta {
    margin-bottom: 8px;
  }
  
  .video-desc {
    font-size: 0.85rem;
  }
  
  .play-icon {
    width: 50px;
    height: 50px;
    font-size: 20px;
  }
  
  /* 改进视频播放器在小屏幕上的体验 */
  .video-player-container {
    width: 100%;
  }
  
  .video-player-info {
    padding: 10px 15px;
  }
  
  .video-player-info h3 {
    font-size: 1.1rem;
    margin-bottom: 5px;
  }
  
  .video-player-info p {
    font-size: 0.85rem;
  }
  
  .close-player {
    top: -30px;
    right: 0;
    font-size: 1.8rem;
  }
  
  /* 优化上传提示显示 */
  .tip-content {
    padding: 15px 20px;
  }
  
  .tip-icon {
    font-size: 2rem;
    margin-bottom: 8px;
  }
  
  .tip-content p {
    font-size: 0.9rem;
  }
}
</style>
  