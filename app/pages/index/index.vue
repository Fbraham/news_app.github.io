<template>
  <view class="content">
    <!-- 水印背景层 -->
    <view class="watermark-container">
      <view class="watermark" v-for="i in 80" :key="i">{{watermarkText}}</view>
    </view>
    
    <!-- 顶部导航栏 -->
    <view class="navbar">
      <view class="nav-content">
        <text class="nav-title">202311000338 付博弈的新闻资讯</text>
        <view class="nav-subtitle">202311000338 付博弈的最新热点资讯</view>
      </view>
    </view>

    <!-- 新闻列表 -->
    <scroll-view scroll-y class="news-list" refresher-enabled @refresherrefresh="onRefresh" :refresher-triggered="isRefreshing">
      <!-- 下拉刷新 -->
      <view class="refresh-indicator" v-if="isRefreshing">
        <uni-loading type="circle" color="#6366F1"></uni-loading>
        <text class="refresh-text">刷新中...</text>
      </view>

      <!-- 新闻项 -->
      <view class="news-item" 
            v-for="(news, index) in newsList" 
            :key="news.id" 
            @click="goToDetail(news.id)"
            :style="{ animationDelay: `${index * 0.05}s` }">
        <view class="news-card">
          <!-- 新闻图片 -->
          <view class="img-container">
            <image :src="news.thumbnail" class="thumbnail" mode="aspectFill"></image>
            <view class="image-overlay"></view>
          </view>
          
          <!-- 新闻信息 -->
          <view class="news-info">
            <view class="title-container">
              <text class="title">{{ news.title }}</text>
              <view class="tag" v-if="news.tag">{{ news.tag }}</view>
            </view>
            
            <view class="meta-bar">
              <view class="meta-left">
                <text class="source">{{ news.source }}</text>
                <text class="date">{{ formatDate(news.date) }}</text>
              </view>
              <view class="meta-right">
                <uni-icons type="right" size="18" color="#94A3B8"></uni-icons>
              </view>
            </view>
          </view>
        </view>
      </view>

      <!-- 列表底部提示 -->
      <view class="list-footer" v-if="newsList.length > 0">
        <view class="footer-line"></view>
        <text class="footer-text">202311000338 付博弈提示：已经到底啦~</text>
        <view class="footer-line"></view>
      </view>

      <!-- 空状态 -->
      <view class="empty-state" v-if="newsList.length === 0 && !isRefreshing">
        <image src="/static/empty-news.png" class="empty-image" mode="aspectFit"></image>
        <text class="empty-text">202311000338 付博弈提示暂无新闻内容</text>
      </view>
    </scroll-view>
  </view>
</template>

<script>
import newsData from '../../data/news_data.js';

export default {
  data() {
    return {
      newsList: [],
      isRefreshing: false,
      watermarkText: '202311000338 付博弈'
    };
  },
  onLoad() {
    this.newsList = newsData;
  },
  methods: {
    goToDetail(newsId) {
      uni.navigateTo({
        url: `/pages/detail/detail?id=${newsId}`
      });
    },
    onRefresh() {
      this.isRefreshing = true;
      setTimeout(() => {
        this.isRefreshing = false;
        uni.showToast({
          title: '刷新完成',
          icon: 'success',
          duration: 1000
        });
      }, 1000);
    },
    formatDate(date) {
      return date.split(' ')[0];
    }
  }
}
</script>

<style lang="scss" scoped>
.content {
  background: linear-gradient(135deg, #F8FAFC 0%, #F1F5F9 100%);
  min-height: 100vh;
  position: relative;
  // 移除可能影响水印的 overflow: hidden;
}

/* ==================== 水印样式 (重点修改) ==================== */
.watermark-container {
  // 关键：使用 fixed 定位，确保覆盖整个视口
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;   // 使用视口宽度
  height: 100vh;  // 使用视口高度
  pointer-events: none; // 确保水印不拦截点击事件
  
  // 关键：设置一个明确的、较低的 z-index
  z-index: 0; 
  
  // 使用 flex 布局，强制换行，让水印填满屏幕
  display: flex;
  flex-wrap: wrap;
  // 分散对齐，让水印分布更均匀
  align-content: space-around;
  justify-content: space-around;
  
  // 增加内边距，避免水印太靠近边缘
  padding: 50rpx;
  
  // 提高整体不透明度，让黑色水印更明显
  opacity: 0.15;
}

.watermark {
  // 关键：颜色改为纯黑色
  color: #000000; 
  font-size: 36rpx;
  font-weight: 500;
  letter-spacing: 2rpx;
  // 旋转角度
  transform: rotate(-15deg);
  white-space: nowrap;
  // 给每个水印一个 margin，控制密度
  margin: 20rpx;
  
  // 关键：为文字添加一个半透明的白色背景，使其在任何底色上都更清晰
  background-color: rgba(255, 255, 255, 0.2);
  padding: 10rpx 20rpx;
  border-radius: 4rpx;
}
/* ====================================================== */

/* 确保所有内容都在水印之上 */
.navbar, .news-list {
  position: relative;
  z-index: 1; // 内容的 z-index 必须高于水印的 0
}

.navbar {
  background: linear-gradient(135deg, #6366F1 0%, #4F46E5 100%);
  padding: 24rpx 0;
  box-shadow: 0 8rpx 32rpx rgba(99, 102, 241, 0.2);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100; // 导航栏层级最高
}

.nav-content {
  padding: 0 32rpx;
}

.nav-title {
  color: #fff;
  font-size: 42rpx;
  font-weight: 700;
  display: block;
  margin-bottom: 8rpx;
}

.nav-subtitle {
  color: rgba(255, 255, 255, 0.9);
  font-size: 28rpx;
}

.news-list {
  padding-top: 160rpx;
  padding-left: 24rpx;
  padding-right: 24rpx;
  padding-bottom: 24rpx;
  // 为列表添加一个轻微的背景色和模糊，增强层次感
  background-color: rgba(248, 250, 252, 0.8);
  backdrop-filter: blur(5rpx);
}

/* 其他原有样式保持不变 */
.refresh-indicator {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 32rpx 0;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 24rpx;
  margin-bottom: 24rpx;
  backdrop-filter: blur(10rpx);
}

.refresh-text {
  font-size: 28rpx;
  color: #64748B;
  margin-top: 12rpx;
}

.news-item {
  margin-bottom: 28rpx;
  animation: slideInUp 0.5s ease-out both;
}

@keyframes slideInUp {
  from {
    opacity: 0;
    transform: translateY(30rpx);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.news-card {
  display: flex;
  background: #fff;
  border-radius: 24rpx;
  overflow: hidden;
  box-shadow: 0 4rpx 24rpx rgba(0, 0, 0, 0.06);
  transition: all 0.3s ease;
  position: relative;
  border: 1rpx solid #F1F5F9;
}

.news-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4rpx;
  background: linear-gradient(90deg, #6366F1, #8B5CF6);
}

.news-card:active {
  transform: scale(0.98);
  box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.1);
}

.img-container {
  width: 260rpx;
  height: 200rpx;
  overflow: hidden;
  flex-shrink: 0;
  position: relative;
}

.thumbnail {
  width: 100%;
  height: 100%;
  transition: transform 0.3s ease;
}

.image-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(45deg, rgba(99, 102, 241, 0.08), transparent);
}

.news-card:hover .thumbnail {
  transform: scale(1.08);
}

.news-info {
  flex: 1;
  padding: 28rpx;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.title-container {
  margin-bottom: 24rpx;
}

.title {
  font-size: 34rpx;
  color: #1E293B;
  line-height: 1.5;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  font-weight: 600;
  letter-spacing: 0.5rpx;
}

.tag {
  display: inline-block;
  background: linear-gradient(135deg, #EC4899, #D946EF);
  color: white;
  padding: 6rpx 16rpx;
  border-radius: 12rpx;
  font-size: 22rpx;
  margin-top: 12rpx;
  font-weight: 500;
}

.meta-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.meta-left {
  display: flex;
  align-items: center;
  flex: 1;
}

.source {
  font-size: 26rpx;
  color: #6366F1;
  font-weight: 600;
  margin-right: 20rpx;
}

.date {
  font-size: 24rpx;
  color: #64748B;
}

.meta-right {
  display: flex;
  align-items: center;
}

.list-footer {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 48rpx 0;
}

.footer-line {
  flex: 1;
  height: 1rpx;
  background: linear-gradient(90deg, transparent, #E2E8F0, transparent);
}

.footer-text {
  margin: 0 32rpx;
  color: #94A3B8;
  font-size: 26rpx;
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 120rpx 0;
}

.empty-image {
  width: 240rpx;
  height: 240rpx;
  opacity: 0.5;
  margin-bottom: 32rpx;
}

.empty-text {
  color: #94A3B8;
  font-size: 30rpx;
}
</style>