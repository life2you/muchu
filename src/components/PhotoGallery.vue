<!-- PhotoGallery.vue -->
<template>
  <div class="photo-gallery">
    <h2 class="section-title">我们的<span>家庭相册</span></h2>
    <p class="section-desc">记录每一个珍贵的家庭时刻</p>
    
    <div class="category-filter">
      <button 
        v-for="category in categories" 
        :key="category.id"
        :class="['filter-btn', { active: activeCategory === category.id }]"
        @click="filterByCategory(category.id)"
      >
        <span class="filter-icon">{{ category.icon }}</span>
        {{ category.name }}
      </button>
    </div>
    
    <div class="masonry-grid">
      <div 
        v-for="(photo, index) in filteredPhotos" 
        :key="index" 
        class="photo-item"
        @click="openPhoto(photo.src)"
      >
        <div class="photo-card">
          <img :src="photo.src" :alt="photo.title" loading="lazy" />
          <div class="photo-info">
            <h3>{{ photo.title }}</h3>
            <p class="photo-date">{{ photo.date }}</p>
          </div>
          <div class="photo-overlay">
            <span class="view-icon">👁️</span>
          </div>
        </div>
      </div>
    </div>
    
    <div v-if="filteredPhotos.length === 0" class="no-photos">
      <div class="empty-state">
        <div class="empty-icon">📷</div>
        <p>暂无该分类下的照片</p>
      </div>
    </div>
    
    <!-- 照片预览 -->
    <div v-if="showPreview" class="photo-preview">
      <div class="preview-container">
        <button class="close-button" @click="closePreview">×</button>
        <img :src="previewSrc" alt="照片预览" />
        <div class="preview-caption">
          <h3>{{ currentPhotoInfo?.title || '家庭照片' }}</h3>
          <p>{{ currentPhotoInfo?.date || '' }}</p>
        </div>
      </div>
      <div class="overlay" @click="closePreview"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// 照片分类
const categories = [
  { id: 'all', name: '全部照片', icon: '🏠' },
  { id: 'family', name: '全家福', icon: '👨‍👩‍👧‍👦' },
  { id: 'travel', name: '旅行记忆', icon: '🏞️' },
  { id: 'daily', name: '日常生活', icon: '☕' },
  { id: 'events', name: '重要时刻', icon: '🎉' }
];

// 照片数据
const photoCollection = [
  {
    id: 1, 
    src: new URL('../assets/WechatIMG4.jpg', import.meta.url).href,
    title: '美好周末',
    date: '2024年5月12日',
    category: 'daily'
  },
  {
    id: 2, 
    src: new URL('../assets/WechatIMG5.jpg', import.meta.url).href,
    title: '春游时光',
    date: '2024年4月8日',
    category: 'travel'
  },
  {
    id: 3, 
    src: new URL('../assets/WechatIMG4.jpg', import.meta.url).href,
    title: '全家欢聚',
    date: '2024年6月1日',
    category: 'family'
  },
  {
    id: 4, 
    src: new URL('../assets/WechatIMG5.jpg', import.meta.url).href,
    title: '生日派对',
    date: '2024年3月15日',
    category: 'events'
  }
];

// 状态变量
const activeCategory = ref('all');
const showPreview = ref(false);
const previewSrc = ref('');
const currentPhotoId = ref(null);

// 过滤照片
const filteredPhotos = computed(() => {
  if (activeCategory.value === 'all') {
    return photoCollection;
  }
  return photoCollection.filter(photo => photo.category === activeCategory.value);
});

// 获取当前预览照片的信息
const currentPhotoInfo = computed(() => {
  if (!currentPhotoId.value) return null;
  return photoCollection.find(photo => photo.src === currentPhotoId.value);
});

// 切换类别
function filterByCategory(categoryId) {
  activeCategory.value = categoryId;
}

// 打开照片预览
function openPhoto(src) {
  previewSrc.value = src;
  currentPhotoId.value = src;
  showPreview.value = true;
  // 禁止背景滚动
  document.body.style.overflow = 'hidden';
}

// 关闭预览
function closePreview() {
  showPreview.value = false;
  // 恢复滚动
  document.body.style.overflow = 'auto';
}
</script>

<style scoped>
.photo-gallery {
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

.category-filter {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin-bottom: 40px;
}

.filter-btn {
  background-color: var(--card-bg);
  border: 1px solid var(--border-color);
  border-radius: 25px;
  padding: 8px 16px;
  font-size: 0.95rem;
  color: var(--text-color);
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 6px;
}

.filter-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px var(--shadow-color);
}

.filter-btn.active {
  background-color: var(--primary-color);
  color: white;
  border-color: var(--primary-color);
}

.filter-icon {
  font-size: 1.2rem;
}

.masonry-grid {
  columns: 4;
  column-gap: 20px;
}

@media (max-width: 1200px) {
  .masonry-grid { columns: 3; }
}

@media (max-width: 900px) {
  .masonry-grid { columns: 2; }
}

@media (max-width: 600px) {
  .masonry-grid { columns: 1; }
  
  .category-filter {
    flex-direction: row;
    overflow-x: auto;
    justify-content: flex-start;
    padding-bottom: 10px;
  }
  
  .filter-btn {
    flex: 0 0 auto;
  }
}

.photo-item {
  break-inside: avoid;
  margin-bottom: 20px;
}

.photo-card {
  position: relative;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 15px var(--shadow-color);
  transition: all 0.3s ease;
  background-color: var(--card-bg);
  cursor: pointer;
}

.photo-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px var(--shadow-color);
}

.photo-card img {
  width: 100%;
  height: auto;
  display: block;
  transition: all 0.5s ease;
}

.photo-info {
  padding: 12px 15px;
  text-align: left;
}

.photo-info h3 {
  margin: 0;
  font-size: 1rem;
  color: var(--text-color);
}

.photo-date {
  margin: 5px 0 0;
  font-size: 0.85rem;
  color: var(--text-secondary);
}

.photo-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: calc(100% - 50px); /* 不包括底部信息区 */
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: all 0.3s ease;
}

.photo-card:hover .photo-overlay {
  opacity: 1;
}

.view-icon {
  font-size: 2rem;
  color: white;
}

.no-photos {
  padding: 40px 0;
  text-align: center;
}

.empty-state {
  background-color: var(--card-bg);
  padding: 40px;
  border-radius: 12px;
  display: inline-block;
  margin: 0 auto;
}

.empty-icon {
  font-size: 3rem;
  margin-bottom: 15px;
  color: var(--text-secondary);
}

.photo-preview {
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

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(5px);
}

.preview-container {
  position: relative;
  z-index: 10;
  max-width: 90%;
  max-height: 90%;
  animation: zoomIn 0.3s ease;
}

.preview-container img {
  max-width: 100%;
  max-height: 80vh;
  object-fit: contain;
  border-radius: 5px;
  box-shadow: 0 0 30px rgba(0, 0, 0, 0.5);
  display: block;
}

.preview-caption {
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  padding: 10px 15px;
  border-radius: 0 0 5px 5px;
  margin-top: -5px;
}

.preview-caption h3 {
  margin: 0;
  font-size: 1.1rem;
}

.preview-caption p {
  margin: 5px 0 0;
  font-size: 0.85rem;
  opacity: 0.8;
}

.close-button {
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
}

.close-button:hover {
  transform: rotate(90deg);
}

@keyframes zoomIn {
  from {
    opacity: 0;
    transform: scale(0.8);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

@media (max-width: 768px) {
  .section-title {
    font-size: 2rem;
  }
  
  .close-button {
    top: 10px;
    right: 10px;
  }
  
  .preview-container {
    max-width: 95%;
  }
}

/* 添加更精细的移动设备适配 */
@media (max-width: 480px) {
  .photo-gallery {
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
  
  .filter-btn {
    padding: 6px 12px;
    font-size: 0.85rem;
  }
  
  .filter-icon {
    font-size: 1rem;
  }
  
  .photo-info {
    padding: 10px;
  }
  
  .photo-info h3 {
    font-size: 0.9rem;
  }
  
  .photo-date {
    font-size: 0.75rem;
  }
  
  .category-filter {
    margin-bottom: 25px;
    padding-bottom: 5px;
  }
  
  /* 改进照片预览在小屏幕上的体验 */
  .preview-caption {
    padding: 8px 12px;
  }
  
  .preview-caption h3 {
    font-size: 1rem;
  }
  
  .preview-caption p {
    font-size: 0.8rem;
  }
  
  .close-button {
    font-size: 1.8rem;
    top: 5px;
    right: 5px;
  }
  
  /* 优化空状态显示 */
  .empty-state {
    padding: 25px;
  }
  
  .empty-icon {
    font-size: 2.5rem;
  }
}
</style>
  