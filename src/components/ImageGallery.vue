<template>
  <div class="image-gallery">
    <h2 class="section-title">时光<span>流影</span></h2>
    <p class="section-desc">以轮播方式欣赏家庭的精彩瞬间</p>
    
    <div class="timeline-integration">
      <div class="integration-info">
        <div class="info-icon">🔄</div>
        <p class="info-text">这里展示了来自时间线的精选照片，以轮播方式进行展示。您可以在<a href="#timeline" class="timeline-link">时间线</a>中查看所有照片的完整时间脉络。</p>
      </div>
    </div>
    
    <div class="carousel-container">
      <div class="carousel">
        <div 
          v-for="(image, index) in images" 
          :key="index"
          class="carousel-slide"
          @click="openLightbox(index)"
        >
          <img :src="image.src" :alt="`相册图片 ${index + 1}`" />
          <div class="image-caption">
            <h3>{{ image.title }}</h3>
            <p>{{ image.date }}</p>
          </div>
        </div>
      </div>
      
      <button class="carousel-btn prev" @click="prevSlide">❮</button>
      <button class="carousel-btn next" @click="nextSlide">❯</button>
    </div>
    
    <div class="carousel-dots">
      <span 
        v-for="(_, index) in images" 
        :key="index" 
        :class="['dot', { active: currentSlide === index }]"
        @click="goToSlide(index)"
      ></span>
    </div>
    
    <!-- 图片灯箱 -->
    <div v-if="lightboxOpen" class="lightbox">
      <div class="lightbox-content">
        <button class="close-lightbox" @click="closeLightbox">×</button>
        <button class="lightbox-nav prev" @click="prevImage" v-if="images.length > 1">❮</button>
        <img :src="images[currentIndex].src" alt="大图预览" />
        <div class="lightbox-info">
          <div class="lightbox-title">{{ images[currentIndex].title }}</div>
          <div class="lightbox-date">{{ images[currentIndex].date }}</div>
          <div class="lightbox-desc">{{ images[currentIndex].description }}</div>
          <div class="lightbox-counter">{{ currentIndex + 1 }} / {{ images.length }}</div>
          <a :href="`#timeline`" class="view-on-timeline" @click="closeLightbox">在时间线上查看</a>
        </div>
        <button class="lightbox-nav next" @click="nextImage" v-if="images.length > 1">❯</button>
      </div>
      <div class="lightbox-overlay" @click="closeLightbox"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';

const images = ref([
  {
    src: new URL('../assets/WechatIMG4.jpg', import.meta.url).href,
    title: '家庭海边度假',
    date: '2024年6月1日',
    description: '一起去海边度假的美好时光，阳光、沙滩和我们一家人的欢笑。'
  },
  {
    src: new URL('../assets/WechatIMG5.jpg', import.meta.url).href,
    title: '小明生日派对',
    date: '2024年5月10日',
    description: '小明六岁生日派对上的全家福，每个人都笑得很开心！'
  },
  {
    src: new URL('../assets/WechatIMG4.jpg', import.meta.url).href,
    title: '温馨圣诞节',
    date: '2023年12月25日',
    description: '圣诞节当天，全家人一起装饰圣诞树，交换礼物的温馨时刻。'
  },
  {
    src: new URL('../assets/WechatIMG5.jpg', import.meta.url).href,
    title: '家庭野餐日',
    date: '2023年8月15日',
    description: '周末野餐是我们家最喜欢的活动之一，这是在城市公园的一次美好回忆。'
  }
]);

// 轮播图相关
const currentSlide = ref(0);
const slideWidth = ref(0);
const autoPlayInterval = ref(null);
const autoPlayDuration = 5000; // 5秒切换

// 灯箱相关
const lightboxOpen = ref(false);
const currentIndex = ref(0);

onMounted(() => {
  // 计算滑动宽度
  updateSlideWidth();
  
  // 响应窗口大小变化
  window.addEventListener('resize', updateSlideWidth);
  
  // 自动播放
  startAutoPlay();
});

function updateSlideWidth() {
  if (window.innerWidth > 1200) {
    slideWidth.value = window.innerWidth * 0.8 / 3; // 在大屏上显示3张
  } else if (window.innerWidth > 768) {
    slideWidth.value = window.innerWidth * 0.8 / 2; // 在中屏上显示2张
  } else {
    slideWidth.value = window.innerWidth * 0.8; // 在小屏上全屏显示
  }
}

function startAutoPlay() {
  stopAutoPlay();
  autoPlayInterval.value = setInterval(() => {
    nextSlide();
  }, autoPlayDuration);
}

function stopAutoPlay() {
  if (autoPlayInterval.value) {
    clearInterval(autoPlayInterval.value);
    autoPlayInterval.value = null;
  }
}

function nextSlide() {
  if (currentSlide.value < images.value.length - 1) {
    currentSlide.value++;
  } else {
    currentSlide.value = 0; // 循环到第一张
  }
}

function prevSlide() {
  stopAutoPlay(); // 手动操作时停止自动播放
  if (currentSlide.value > 0) {
    currentSlide.value--;
  } else {
    currentSlide.value = images.value.length - 1; // 循环到最后一张
  }
  startAutoPlay(); // 重新开始自动播放
}

function goToSlide(index) {
  stopAutoPlay(); // 手动操作时停止自动播放
  currentSlide.value = index;
  startAutoPlay(); // 重新开始自动播放
}

function openLightbox(index) {
  currentIndex.value = index;
  lightboxOpen.value = true;
  document.body.style.overflow = 'hidden';
  stopAutoPlay(); // 打开灯箱时停止自动播放
}

function closeLightbox() {
  lightboxOpen.value = false;
  document.body.style.overflow = 'auto';
  startAutoPlay(); // 关闭灯箱时重新开始自动播放
}

function nextImage() {
  if (currentIndex.value < images.value.length - 1) {
    currentIndex.value++;
  } else {
    currentIndex.value = 0;
  }
}

function prevImage() {
  if (currentIndex.value > 0) {
    currentIndex.value--;
  } else {
    currentIndex.value = images.value.length - 1;
  }
}

// 组件卸载时清除自动播放
watch(() => {
  return {
    autoPlayInterval: autoPlayInterval.value
  };
}, () => {
  stopAutoPlay();
});
</script>

<style scoped>
.image-gallery {
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
  margin-bottom: 20px;
}

.timeline-integration {
  background: linear-gradient(135deg, rgba(106, 140, 175, 0.1), rgba(255, 157, 125, 0.1));
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 30px;
  text-align: center;
  border: 1px solid var(--border-color);
}

.integration-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.info-icon {
  font-size: 2rem;
  color: var(--primary-color);
}

.info-text {
  margin: 0;
  color: var(--text-color);
  font-size: 1rem;
  line-height: 1.6;
}

.timeline-link {
  color: var(--primary-color);
  text-decoration: none;
  font-weight: 500;
  transition: all 0.2s ease;
}

.timeline-link:hover {
  text-decoration: underline;
}

.carousel-container {
  position: relative;
  width: 100%;
  overflow: hidden;
  padding: 20px 0;
  border-radius: 12px;
}

.carousel {
  display: flex;
  transition: transform 0.5s ease;
  transform: translateX(calc(-1 * v-bind('currentSlide * slideWidth') + 'px'));
}

.carousel-slide {
  flex: 0 0 auto;
  width: v-bind('slideWidth + "px"');
  padding: 0 15px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.carousel-slide:hover {
  transform: translateY(-10px) scale(1.02);
}

.carousel-slide img {
  width: 100%;
  height: 300px;
  object-fit: cover;
  border-radius: 8px;
  box-shadow: 0 5px 15px var(--shadow-color);
  transition: all 0.3s ease;
}

.image-caption {
  margin-top: 10px;
  text-align: center;
}

.image-caption h3 {
  font-size: 1.1rem;
  color: var(--text-color);
  margin: 0 0 5px;
}

.image-caption p {
  font-size: 0.85rem;
  color: var(--text-secondary);
  margin: 0;
}

.carousel-dots {
  display: flex;
  justify-content: center;
  margin-top: 20px;
  gap: 8px;
}

.dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background-color: var(--border-color);
  cursor: pointer;
  transition: all 0.3s ease;
}

.dot.active {
  background-color: var(--primary-color);
  transform: scale(1.2);
}

.carousel-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 50px;
  height: 50px;
  background: rgba(255, 255, 255, 0.7);
  border: none;
  border-radius: 50%;
  font-size: 1.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 2px 10px var(--shadow-color);
  z-index: 5;
  transition: all 0.3s ease;
}

.dark-theme .carousel-btn {
  background: rgba(50, 50, 50, 0.7);
  color: white;
}

.carousel-btn:hover {
  background: var(--card-bg);
  box-shadow: 0 5px 15px var(--shadow-color);
}

.carousel-btn.prev {
  left: 20px;
}

.carousel-btn.next {
  right: 20px;
}

/* 灯箱样式 */
.lightbox {
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

.lightbox-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.9);
  backdrop-filter: blur(5px);
}

.lightbox-content {
  position: relative;
  z-index: 10;
  max-width: 90%;
  max-height: 90vh;
  animation: zoomIn 0.3s ease;
  display: flex;
  flex-direction: column;
}

.lightbox-content img {
  max-width: 100%;
  max-height: 70vh;
  border-radius: 5px 5px 0 0;
  box-shadow: 0 0 30px rgba(0, 0, 0, 0.5);
  display: block;
}

.lightbox-info {
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  padding: 15px 20px;
  border-radius: 0 0 5px 5px;
  position: relative;
}

.lightbox-title {
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 5px;
}

.lightbox-date {
  font-size: 0.9rem;
  opacity: 0.8;
  margin-bottom: 8px;
}

.lightbox-desc {
  font-size: 0.95rem;
  margin-bottom: 10px;
  line-height: 1.5;
}

.lightbox-counter {
  position: absolute;
  bottom: 15px;
  right: 20px;
  font-size: 0.85rem;
  opacity: 0.7;
}

.view-on-timeline {
  display: inline-block;
  background-color: var(--primary-color);
  color: white;
  padding: 6px 12px;
  border-radius: 4px;
  text-decoration: none;
  font-size: 0.85rem;
  margin-top: 10px;
  transition: all 0.3s ease;
}

.view-on-timeline:hover {
  background-color: var(--accent-color);
}

.close-lightbox {
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

.close-lightbox:hover {
  transform: rotate(90deg);
}

.lightbox-nav {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 50px;
  height: 50px;
  background: rgba(255, 255, 255, 0.2);
  border: none;
  border-radius: 50%;
  color: white;
  font-size: 1.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.lightbox-nav:hover {
  background: rgba(255, 255, 255, 0.3);
}

.lightbox-nav.prev {
  left: -70px;
}

.lightbox-nav.next {
  right: -70px;
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
  
  .lightbox-nav.prev {
    left: 10px;
  }
  
  .lightbox-nav.next {
    right: 10px;
  }
  
  .close-lightbox {
    top: 10px;
    right: 10px;
  }
  
  .carousel-btn {
    width: 40px;
    height: 40px;
    font-size: 1.2rem;
  }
  
  .carousel-btn.prev {
    left: 10px;
  }
  
  .carousel-btn.next {
    right: 10px;
  }
}
</style>
