<template>
  <view class="container">
    <!-- 水印背景层 -->
    <view class="watermark-container">
      <view class="watermark" v-for="i in 80" :key="i">{{watermarkText}}</view>
    </view>
    
    <!-- 导航栏 -->
    <view class="navbar">
      <view class="nav-back" @click="goBack">
        <uni-icons type="left" size="26" color="#fff"></uni-icons>
        <text class="back-text">返回</text>
      </view>
      <text class="nav-title">新闻详情</text>
      <view class="nav-placeholder"></view>
    </view>

    <!-- 新闻内容区域 -->
    <scroll-view class="content-scroll" scroll-y :scroll-top="scrollTop" @scroll="onScroll">
      <!-- 新闻头部 -->
      <view class="news-header" :class="{ 'header-shadow': showHeaderShadow }">
        <!-- 新闻标题 -->
        <view class="news-title">{{ currentNews.title }}</view>

        <!-- 新闻元信息 -->
        <view class="news-meta">
          <view class="meta-item">
            <uni-icons type="calendar" size="18" color="#64748B"></uni-icons>
            <text class="meta-text">{{ currentNews.date }}</text>
          </view>
          <view class="meta-item">
            <uni-icons type="person" size="18" color="#64748B"></uni-icons>
            <text class="meta-text">{{ currentNews.source }}</text>
          </view>
          <view class="meta-item" v-if="currentNews.views">
            <uni-icons type="eye" size="18" color="#64748B"></uni-icons>
            <text class="meta-text">{{ currentNews.views }}阅读</text>
          </view>
        </view>

        <!-- 分割线 -->
        <view class="divider"></view>
      </view>

      <!-- 新闻正文 -->
      <view class="content-container">
        <view class="rich-text-container">
          <rich-text :nodes="currentNews.content" class="rich-text-content"></rich-text>
        </view>

        <!-- 相关推荐 -->
        <view class="related-news" v-if="relatedNews.length > 0">
          <view class="section-title">
            <view class="title-line"></view>
            <text class="title-text">202311000338 付博弈的相关推荐</text>
          </view>
          <view class="related-list">
            <view class="related-item" v-for="news in relatedNews" :key="news.id" @click="goToRelated(news.id)">
              <text class="related-title">{{ news.title }}</text>
              <text class="related-date">{{ formatDate(news.date) }}</text>
            </view>
          </view>
        </view>
      </view>
    </scroll-view>

    <!-- 底部操作区 -->
    <view class="action-bar" :class="{ 'action-bar-shadow': showActionShadow }">
      <view class="action-btn" :class="{ active: isLiked }" @click="handleLike">
        <uni-icons :type="isLiked ? 'heart-filled' : 'heart'" size="26" :color="isLiked ? '#EC4899' : '#64748B'"></uni-icons>
        <text class="btn-text">{{ isLiked ? '已点赞' : '点赞' }}</text>
        <text class="btn-count" v-if="likeCount > 0">{{ likeCount }}</text>
      </view>
      
      <view class="action-btn" :class="{ active: isCollected }" @click="handleCollect">
        <uni-icons :type="isCollected ? 'star-filled' : 'star'" size="26" :color="isCollected ? '#F59E0B' : '#64748B'"></uni-icons>
        <text class="btn-text">{{ isCollected ? '已收藏' : '收藏' }}</text>
      </view>
      
      <view class="action-btn share-btn" @click="handleShare">
        <uni-icons type="redo" size="26" color="#fff"></uni-icons>
        <text class="btn-text">分享</text>
      </view>
    </view>

    <!-- 回到顶部 -->
    <view class="back-to-top" :class="{ show: showBackTop }" @click="scrollToTop">
      <uni-icons type="top" size="22" color="#fff"></uni-icons>
    </view>
  </view>
</template>

<script>
import newsData from '../../data/news_data.js'

export default {
  data() {
    return {
      currentNews: {},
      isLiked: false,
      isCollected: false,
      likeCount: 0,
      showBackTop: false,
      scrollTop: 0,
      showHeaderShadow: false,
      showActionShadow: false,
      relatedNews: [],
      watermarkText: '202311000338 付博弈'
    }
  },
  onLoad(options) {
    const newsId = parseInt(options.id)
    this.currentNews = newsData.find(news => news.id === newsId) || {}
    this.loadRelatedNews(newsId)
    
    // 模拟数据
    this.likeCount = Math.floor(Math.random() * 100) + 10
  },
  methods: {
    goBack() {
      uni.navigateBack()
    },
    goToRelated(newsId) {
      uni.redirectTo({
        url: `/pages/detail/detail?id=${newsId}`
      })
    },
    handleLike() {
      this.isLiked = !this.isLiked
      this.likeCount += this.isLiked ? 1 : -1
      uni.showToast({
        title: this.isLiked ? '点赞成功' : '已取消点赞',
        icon: 'none'
      })
    },
    handleCollect() {
      this.isCollected = !this.isCollected
      uni.showToast({
        title: this.isCollected ? '收藏成功' : '已取消收藏',
        icon: 'none'
      })
    },
    handleShare() {
      uni.showShareMenu({
        withShareTicket: true
      })
    },
    onScroll(e) {
      const scrollTop = e.detail.scrollTop
      this.showBackTop = scrollTop > 400
      this.showHeaderShadow = scrollTop > 10
      this.showActionShadow = scrollTop > 0
    },
    scrollToTop() {
      this.scrollTop = 0
    },
    loadRelatedNews(currentId) {
      // 模拟相关新闻数据
      this.relatedNews = newsData
        .filter(news => news.id !== currentId)
        .slice(0, 3)
    },
    formatDate(date) {
      return date.split(' ')[0]
    }
  },
  onShareAppMessage() {
    return {
      title: this.currentNews.title,
      path: `/pages/detail/detail?id=${this.currentNews.id}`,
      imageUrl: this.currentNews.thumbnail
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  width: 100%;
  min-height: 100vh;
  background: linear-gradient(180deg, #F8FAFC 0%, #FFFFFF 100%);
  position: relative;
}

.watermark-container {

  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;  
  height: 100vh;  
  pointer-events: none; 

  z-index: 0; 

  display: flex;
  flex-wrap: wrap;

  align-content: space-around;
  justify-content: space-around;
  
  padding: 50rpx;

  opacity: 0.15;
}

.watermark {

  color: #000000; 
  font-size: 36rpx;
  font-weight: 500;
  letter-spacing: 2rpx;
  
  transform: rotate(-15deg);
  white-space: nowrap;

  margin: 20rpx;
  
  
  background-color: rgba(255, 255, 255, 0.2);
  padding: 10rpx 20rpx;
  border-radius: 4rpx;
}
/* ====================================================== */

/* 确保所有内容都在水印之上 */
.navbar, .content-scroll, .action-bar, .back-to-top {
  position: relative;
  z-index: 1; // 内容的 z-index 必须高于水印的 0
}

.navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 128rpx;
  padding: 0 32rpx;
  background: linear-gradient(135deg, #6366F1 0%, #4F46E5 100%);
  color: white;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100; // 导航栏层级最高
  padding-top: env(safe-area-inset-top);
}

.nav-back {
  display: flex;
  align-items: center;
  padding: 20rpx;
  border-radius: 16rpx;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10rpx);
}

.back-text {
  font-size: 30rpx;
  margin-left: 10rpx;
  font-weight: 500;
}

.nav-title {
  font-size: 38rpx;
  font-weight: 700;
}

.nav-placeholder {
  width: 120rpx;
}

.content-scroll {
  width: 100%;
  height: 100vh;
  padding-top: 128rpx;
  padding-bottom: 160rpx;
  // 为滚动区域添加一个轻微的背景色和模糊，增强层次感
  background-color: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(5rpx);
}

/* 其他原有样式保持不变 */
.news-header {
  background: #fff;
  padding: 48rpx 32rpx 32rpx;
  transition: box-shadow 0.3s ease;
  border-radius: 0 0 32rpx 32rpx;
}

.header-shadow {
  box-shadow: 0 4rpx 24rpx rgba(0, 0, 0, 0.08);
}

.news-title {
  font-size: 46rpx;
  font-weight: 700;
  line-height: 1.5;
  color: #1E293B;
  margin-bottom: 32rpx;
}

.news-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 24rpx;
  margin-bottom: 32rpx;
}

.meta-item {
  display: flex;
  align-items: center;
  background: #F8FAFC;
  padding: 12rpx 20rpx;
  border-radius: 12rpx;
  border: 1rpx solid #F1F5F9;
}

.meta-text {
  font-size: 26rpx;
  color: #64748B;
  margin-left: 10rpx;
  font-weight: 500;
}

.divider {
  height: 2rpx;
  background: linear-gradient(90deg, transparent, #6366F1, transparent);
  opacity: 0.3;
}

.content-container {
  background: rgba(255, 255, 255, 0.95);
  margin-top: 24rpx;
  border-radius: 32rpx 32rpx 0 0;
  overflow: hidden;
  box-shadow: 0 -4rpx 24rpx rgba(0, 0, 0, 0.04);
}

.rich-text-container {
  padding: 48rpx 32rpx;
}

.rich-text-content {
  font-size: 34rpx;
  line-height: 1.8;
  color: #334155;
  letter-spacing: 0.5rpx;
}

.rich-text-content img {
  max-width: 100% !important;
  max-height: 520rpx !important;
  object-fit: contain !important;
  display: block !important;
  margin: 48rpx auto !important;
  border-radius: 20rpx !important;
  box-shadow: 0 8rpx 40rpx rgba(0, 0, 0, 0.12) !important;
  transition: transform 0.3s ease !important;
  border: 1rpx solid #F1F5F9 !important;
}

.rich-text-content img:active {
  transform: scale(0.98) !important;
}

.rich-text-content p {
  margin-bottom: 36rpx !important;
  text-align: justify !important;
  font-size: 34rpx !important;
}

.rich-text-content h4 {
  font-size: 38rpx !important;
  font-weight: 700 !important;
  margin: 64rpx 0 32rpx !important;
  color: #1E293B !important;
  position: relative;
  padding-left: 24rpx;
}

.rich-text-content h4::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 8rpx;
  height: 36rpx;
  background: linear-gradient(135deg, #6366F1, #8B5CF6);
  border-radius: 4rpx;
}

.related-news {
  padding: 48rpx 32rpx;
  border-top: 1rpx solid #F1F5F9;
}

.section-title {
  display: flex;
  align-items: center;
  margin-bottom: 32rpx;
}

.title-line {
  width: 8rpx;
  height: 36rpx;
  background: linear-gradient(135deg, #6366F1, #8B5CF6);
  border-radius: 4rpx;
  margin-right: 20rpx;
}

.title-text {
  font-size: 34rpx;
  font-weight: 700;
  color: #1E293B;
}

.related-list {
  display: flex;
  flex-direction: column;
  gap: 24rpx;
}

.related-item {
  padding: 28rpx;
  background: #F8FAFC;
  border-radius: 16rpx;
  transition: all 0.3s ease;
  border: 1rpx solid #F1F5F9;
}

.related-item:active {
  background: #F1F5F9;
  transform: scale(0.98);
  border-color: #6366F1;
}

.related-title {
  display: block;
  font-size: 30rpx;
  color: #334155;
  line-height: 1.5;
  margin-bottom: 16rpx;
  font-weight: 500;
}

.related-date {
  font-size: 24rpx;
  color: #94A3B8;
}

.action-bar {
  display: flex;
  align-items: center;
  height: 140rpx;
  background: #fff;
  border-top: 1rpx solid #F1F5F9;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  padding-bottom: env(safe-area-inset-bottom);
  transition: box-shadow 0.3s ease;
  z-index: 100;
}

.action-bar-shadow {
  box-shadow: 0 -4rpx 24rpx rgba(0, 0, 0, 0.08);
}

.action-btn {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #64748B;
  transition: all 0.3s ease;
  position: relative;
  padding: 20rpx 0;
  border-radius: 12rpx;
  margin: 0 8rpx;
}

.action-btn.active {
  color: #6366F1;
  background: #F8FAFC;
}

.action-btn:active {
  background: #F1F5F9;
}

.btn-text {
  font-size: 26rpx;
  margin-top: 10rpx;
  font-weight: 500;
}

.btn-count {
  position: absolute;
  top: 12rpx;
  right: 40rpx;
  background: #EC4899;
  color: white;
  font-size: 22rpx;
  padding: 4rpx 12rpx;
  border-radius: 20rpx;
  min-width: 28rpx;
  text-align: center;
  font-weight: 600;
}

.share-btn {
  background: linear-gradient(135deg, #6366F1, #4F46E5);
  color: white;
  border-radius: 20rpx;
  margin: 0 24rpx;
  box-shadow: 0 4rpx 16rpx rgba(99, 102, 241, 0.3);
}

.share-btn .btn-text {
  color: white;
}

.back-to-top {
  position: fixed;
  right: 32rpx;
  bottom: 180rpx;
  width: 88rpx;
  height: 88rpx;
  background: linear-gradient(135deg, #6366F1, #4F46E5);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 8rpx 32rpx rgba(99, 102, 241, 0.3);
  opacity: 0;
  transform: translateY(20rpx);
  transition: all 0.3s ease;
  z-index: 99;
  border: 2rpx solid rgba(255, 255, 255, 0.2);
}

.back-to-top.show {
  opacity: 1;
  transform: translateY(0);
}

.back-to-top:active {
  transform: scale(0.95);
}
</style>