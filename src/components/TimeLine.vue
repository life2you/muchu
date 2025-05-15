<template>
  <div class="timeline-container">
    <h2 class="section-title">家庭<span>时光轴</span></h2>
    <p class="section-desc">记录我们家庭成长的每一个重要时刻</p>
    
    <div class="timeline-navigation">
      <button 
        v-for="year in availableYears" 
        :key="year" 
        @click="scrollToYear(year)"
        :class="['year-btn', { active: activeYear === year }]"
      >
        {{ year }}
      </button>
    </div>
    
    <div class="timeline">
      <div 
        v-for="year in timelineData" 
        :key="year.year" 
        class="timeline-year" 
        :id="`year-${year.year}`"
        ref="yearSections"
      >
        <div class="year-marker">
          <div class="year-circle">{{ year.year }}</div>
          <div class="year-line"></div>
        </div>
        
        <div class="timeline-events">
          <div 
            v-for="(event, index) in year.events" 
            :key="index" 
            class="timeline-event"
            :class="{ 'event-right': index % 2 === 1 }"
          >
            <div class="event-date">{{ event.date }}</div>
            <div class="event-card">
              <div class="event-content">
                <div v-if="event.image" class="event-image">
                  <img :src="event.image" :alt="event.title" />
                </div>
                <div class="event-details">
                  <h3>{{ event.title }}</h3>
                  <p>{{ event.description }}</p>
                  <div class="event-tags">
                    <span v-for="(tag, tagIndex) in event.tags" :key="tagIndex" class="event-tag">
                      {{ tag }}
                    </span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- 添加事件按钮 -->
    <div class="add-event">
      <button class="add-event-btn">
        <span class="add-icon">+</span>
        添加新的回忆
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';

// 模拟时间线数据
const timelineEvents = [
  {
    year: 2024,
    events: [
      {
        date: '2024年6月1日',
        title: '家庭旅行 - 海边度假',
        description: '我们全家一起去了海边度假，孩子们第一次看到了大海，非常兴奋！',
        image: 'https://picsum.photos/500/300?random=1',
        tags: ['旅行', '全家福']
      },
      {
        date: '2024年5月10日',
        title: '小明六岁生日',
        description: '小明度过了他六岁的生日，我们举办了一个恐龙主题的派对。',
        image: 'https://picsum.photos/500/300?random=2',
        tags: ['生日', '派对']
      }
    ]
  },
  {
    year: 2023,
    events: [
      {
        date: '2023年12月25日',
        title: '温馨圣诞节',
        description: '全家人在家中一起装饰圣诞树、交换礼物，度过了一个温馨的圣诞节。',
        image: 'https://picsum.photos/500/300?random=3',
        tags: ['节日', '家庭活动']
      },
      {
        date: '2023年8月15日',
        title: '家庭野餐日',
        description: '在附近的公园进行了一次家庭野餐，天气很好，大家玩得很开心。',
        image: 'https://picsum.photos/500/300?random=4',
        tags: ['户外', '野餐']
      },
      {
        date: '2023年2月1日',
        title: '新年团聚',
        description: '春节期间全家团聚，一起包饺子、看春晚，欢度新春佳节。',
        image: 'https://picsum.photos/500/300?random=5',
        tags: ['春节', '团聚']
      }
    ]
  },
  {
    year: 2022,
    events: [
      {
        date: '2022年10月7日',
        title: '国庆出游',
        description: '国庆长假期间，我们全家去了古镇旅游，体验了当地的文化和美食。',
        image: 'https://picsum.photos/500/300?random=6',
        tags: ['旅行', '文化']
      }
    ]
  }
];

const timelineData = ref(timelineEvents);
const yearSections = ref([]);
const activeYear = ref(timelineEvents[0].year); // 默认选中最新的年份

// 获取所有可用的年份
const availableYears = computed(() => {
  return timelineData.value.map(item => item.year);
});

// 滚动到特定年份
function scrollToYear(year) {
  activeYear.value = year;
  const yearElement = document.getElementById(`year-${year}`);
  if (yearElement) {
    yearElement.scrollIntoView({ behavior: 'smooth', block: 'start' });
  }
}

// 监听滚动，更新当前激活的年份
onMounted(() => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const year = parseInt(entry.target.id.split('-')[1]);
        activeYear.value = year;
      }
    });
  }, { threshold: 0.5 });
  
  setTimeout(() => {
    const yearElements = document.querySelectorAll('.timeline-year');
    yearElements.forEach(el => observer.observe(el));
  }, 100);
});
</script>

<style scoped>
.timeline-container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 60px 20px 40px;
  position: relative;
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

.timeline-navigation {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 40px;
  flex-wrap: wrap;
}

.year-btn {
  background-color: var(--card-bg);
  border: 1px solid var(--border-color);
  color: var(--text-color);
  padding: 8px 16px;
  font-size: 1rem;
  border-radius: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.year-btn:hover {
  transform: translateY(-3px);
  box-shadow: 0 4px 8px var(--shadow-color);
}

.year-btn.active {
  background-color: var(--primary-color);
  color: white;
  border-color: var(--primary-color);
}

.timeline {
  position: relative;
  margin: 0 auto;
  padding: 20px 0;
}

.timeline::before {
  content: '';
  position: absolute;
  top: 0;
  bottom: 0;
  left: 50%;
  width: 4px;
  background-color: var(--primary-color);
  transform: translateX(-50%);
  opacity: 0.3;
}

.timeline-year {
  position: relative;
  margin-bottom: 60px;
  scroll-margin-top: 100px;
}

.year-marker {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  z-index: 2;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.year-circle {
  width: 60px;
  height: 60px;
  background-color: var(--primary-color);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
  font-size: 1.2rem;
  margin-bottom: 10px;
  box-shadow: 0 3px 10px var(--shadow-color);
}

.year-line {
  width: 2px;
  height: 30px;
  background-color: var(--primary-color);
}

.timeline-events {
  position: relative;
  margin-top: 70px;
}

.timeline-event {
  position: relative;
  margin-bottom: 40px;
  width: 45%;
  margin-left: 55%;
}

.timeline-event.event-right {
  margin-left: 0;
  margin-right: 55%;
  text-align: right;
}

.event-date {
  margin-bottom: 10px;
  font-weight: bold;
  color: var(--primary-color);
  font-size: 0.95rem;
}

.event-card {
  background-color: var(--card-bg);
  border-radius: 10px;
  box-shadow: 0 3px 10px var(--shadow-color);
  overflow: hidden;
  transition: all 0.3s ease;
  border: 1px solid var(--border-color);
}

.event-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 20px var(--shadow-color);
}

.event-content {
  display: flex;
  flex-direction: column;
}

.event-image {
  width: 100%;
  height: 180px;
  overflow: hidden;
}

.event-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.event-card:hover .event-image img {
  transform: scale(1.05);
}

.event-details {
  padding: 15px;
}

.event-details h3 {
  margin: 0 0 10px;
  color: var(--text-color);
  font-size: 1.2rem;
}

.event-details p {
  margin: 0 0 12px;
  color: var(--text-secondary);
  font-size: 0.95rem;
  line-height: 1.6;
}

.event-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.event-tag {
  background-color: var(--primary-color);
  color: white;
  font-size: 0.75rem;
  padding: 3px 10px;
  border-radius: 12px;
  opacity: 0.8;
}

.add-event {
  text-align: center;
  margin-top: 40px;
}

.add-event-btn {
  background-color: var(--accent-color);
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  font-size: 1rem;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  transition: all 0.3s ease;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.add-event-btn:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
}

.add-icon {
  font-size: 1.2rem;
  font-weight: bold;
}

@media (max-width: 768px) {
  .timeline::before {
    left: 30px;
  }
  
  .year-marker {
    left: 30px;
    transform: none;
  }
  
  .timeline-event, .timeline-event.event-right {
    width: calc(100% - 60px);
    margin-left: 60px;
    margin-right: 0;
    text-align: left;
  }
  
  .section-title {
    font-size: 2rem;
  }
  
  .timeline-navigation {
    overflow-x: auto;
    justify-content: flex-start;
    padding-bottom: 10px;
  }
  
  .year-btn {
    flex: 0 0 auto;
  }
}

/* 添加更细粒度的移动设备适配 */
@media (max-width: 480px) {
  .timeline-container {
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
  
  .year-circle {
    width: 50px;
    height: 50px;
    font-size: 1rem;
  }
  
  .event-card {
    margin-bottom: 25px;
  }
  
  .event-details h3 {
    font-size: 1.1rem;
  }
  
  .event-details p {
    font-size: 0.9rem;
  }
  
  .event-tag {
    font-size: 0.7rem;
    padding: 2px 8px;
  }
  
  .timeline-events {
    margin-top: 55px;
  }
  
  /* 改进年份导航在超小屏幕上的显示 */
  .year-btn {
    padding: 6px 12px;
    font-size: 0.9rem;
  }
  
  /* 优化添加事件按钮 */
  .add-event-btn {
    padding: 8px 16px;
    font-size: 0.9rem;
  }
}
</style> 