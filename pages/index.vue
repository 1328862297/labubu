
<template>
  <div class="container">
    <div class="header">
      <h1 class="title">Labu wallpaper</h1>
      <p class="subtitle">Explore stunning dynamic wallpapers</p>
    </div>

    <div class="video-grid">
      <div v-for="(video, index) in videos" :key="video.id" class="video-card">
        <div class="video-container">
          <video
            :src="video.src"
            class="video"
            autoplay
            muted
            loop
            @mouseenter="playVideo"
            @mouseleave="pauseVideo"
            :ref="'video-' + index"
          ></video>
          <div class="video-overlay"></div>
          <div class="play-button" @click="playFullscreen(index)">
            <div class="play-icon"></div>
          </div>
        </div>

        <div class="card-content">
          <h3 class="video-title">{{ video.title }}</h3>

          <div class="video-stats">
            <div class="stat-item">
              <svg class="stat-icon" viewBox="0 0 24 24">
                <path
                  d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                />
              </svg>
              {{ formatNumber(video.likes) }}
            </div>
            <div class="stat-item">
              <svg class="stat-icon" viewBox="0 0 24 24">
                <path
                  d="M12 4.5C7 4.5 2.73 7.61 1 12c1.73 4.39 6 7.5 11 7.5s9.27-3.11 11-7.5c-1.73-4.39-6-7.5-11-7.5zM12 17c-2.76 0-5-2.24-5-5s2.24-5 5-5 5 2.24 5 5-2.24 5-5 5zm0-8c-1.66 0-3 1.34-3 3s1.34 3 3 3 3-1.34 3-3-1.34-3-3-3z"
                />
              </svg>
              {{ formatNumber(video.views) }}
            </div>
          </div>

          <div class="action-buttons">
            <button
              class="btn btn-like"
              :class="{ liked: video.isLiked }"
              @click="toggleLike(video, $event)"
            >
              <svg class="btn-icon" viewBox="0 0 24 24">
                <path
                  d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                />
              </svg>
              {{ video.isLiked ? "已点赞" : "点赞" }}
            </button>
            <input type="file" id="fileInput" style="display:none;" accept="image/*">
            <button class="btn btn-download" @click="downloadVideo(video)">
              <svg class="btn-icon" viewBox="0 0 24 24">
                <path d="M19 9h-4V3H9v6H5l7 7 7-7zM5 18v2h14v-2H5z" />
              </svg>
              下载
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
 
<script setup>
import {
  createApp,
  ref,
  computed,
  onMounted,
  onUnmounted,
  nextTick,
} from "vue";
import videoSrc from '@/assets/videos/labb1.mp4';

const videos = ref([
  {
    id: 1,
    title: "labubu1",
    src: videoSrc,
    dowSrc:'https://www.quarkquant.com/quark/videos/labb1.livp',
    likes: 12500,
    views: 89000,
    isLiked: false,
  },
  // {
  //   id: 2,
  //   title: "城市夜景延时摄影",
  //   src:videoSrc,
  //   dowSrc:'https://www.quarkquant.com/quark/videos/labb1.livp',
  //   likes: 8900,
  //   views: 45000,
  //   isLiked: false,
  // },
  // {
  //   id: 3,
  //   title: "海浪拍岸慢镜头",
  //   src: videoSrc,
  //   dowSrc:'https://www.quarkquant.com/quark/videos/labb1.livp',
  //   likes: 15600,
  //   views: 67000,
  //   isLiked: true,
  // }
]);

const playVideo = (event) => {
  const video = event.target;
  video.play().catch(() => {
    // 处理自动播放策略限制
  });
};

const pauseVideo = (event) => {
  const video = event.target;
  video.pause();
};

const playFullscreen = (index) => {
  const videoRef = document.querySelector(`[data-video-index="${index}"]`);
  if (videoRef) {
    if (videoRef.requestFullscreen) {
      videoRef.requestFullscreen();
    }
    videoRef.play();
  }
};

const toggleLike = (video, event) => {
  video.isLiked = !video.isLiked;

  if (video.isLiked) {
    video.likes++;
    // 添加点赞动画
    const button = event.currentTarget;
    button.classList.add("like-animation");

    // 创建飘心效果
    createFloatingHeart(button);

    setTimeout(() => {
      button.classList.remove("like-animation");
    }, 300);
  } else {
    video.likes--;
  }
};

const createFloatingHeart = (button) => {
  const heart = document.createElement("div");
  heart.innerHTML = "♥";
  heart.className = "heart";
  heart.style.left = Math.random() * 20 - 10 + "px";

  const container = document.createElement("div");
  container.className = "floating-hearts";
  container.appendChild(heart);

  button.appendChild(container);

  setTimeout(() => {
    container.remove();
  }, 2000);
};

const downloadVideo = (video) => {
  // 模拟下载
//  document.getElementById('fileInput').click();
  const link = document.createElement("a");
  link.href = video.dowSrc;
  link.download = `${video.title}.mp4`;
  link.click();
};

const formatNumber = (num) => {
  if (num >= 1000000) {
    return (num / 1000000).toFixed(1) + "M";
  } else if (num >= 1000) {
    return (num / 1000).toFixed(1) + "K";
  }
  return num.toString();
};

onMounted(() => {
  // 为每个视频添加索引属性用于全屏播放
  const videoElements = document.querySelectorAll("video");
  videoElements.forEach((video, index) => {
    video.setAttribute("data-video-index", index);
  });
});
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, sans-serif;
  background: #000;
  color: #f5f5f7;
  overflow-x: hidden;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 60px 20px;
}

.header {
  text-align: center;
  margin-bottom: 80px;
}

.title {
  font-size: 3.5rem;
  font-weight: 700;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin-bottom: 20px;
  animation: slideInUp 1s ease-out;
}

.subtitle {
  font-size: 1.2rem;
  color: #a1a1a6;
  animation: slideInUp 1s ease-out 0.2s both;
}

.video-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 40px;
  margin-top: 60px;
}

.video-card {
  background: rgba(29, 29, 31, 0.8);
  border-radius: 20px;
  padding: 0;
  overflow: hidden;
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  transition: all 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  transform: perspective(1000px) rotateX(0deg) rotateY(0deg);
  animation: cardSlideIn 0.8s ease-out forwards;
  opacity: 0;
}

.video-card:nth-child(1) {
  animation-delay: 0.1s;
}
.video-card:nth-child(2) {
  animation-delay: 0.2s;
}
.video-card:nth-child(3) {
  animation-delay: 0.3s;
}
.video-card:nth-child(4) {
  animation-delay: 0.4s;
}
.video-card:nth-child(5) {
  animation-delay: 0.5s;
}
.video-card:nth-child(6) {
  animation-delay: 0.6s;
}

.video-card:hover {
  transform: perspective(1000px) rotateX(2deg) rotateY(-2deg) translateY(-10px);
  box-shadow: 0 40px 80px rgba(0, 0, 0, 0.6);
  border-color: rgba(255, 255, 255, 0.2);
}

.video-container {
  position: relative;
  width: 100%;
  height: 200px;
  overflow: hidden;
  border-radius: 20px 20px 0 0;
}

.video {
  width: 100%;
  height: 100%;

  transition: transform 0.4s ease;
}

.video-card:hover .video {
  transform: scale(1.05);
}

.video-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.7) 0%, transparent 100%);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.video-card:hover .video-overlay {
  opacity: 1;
}

.play-button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 60px;
  height: 60px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  opacity: 0;
  backdrop-filter: blur(10px);
}

.video-card:hover .play-button {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1.1);
}

.play-icon {
  width: 0;
  height: 0;
  border-left: 18px solid #000;
  border-top: 12px solid transparent;
  border-bottom: 12px solid transparent;
  margin-left: 4px;
}

.card-content {
  padding: 25px;
}

.video-title {
  font-size: 1.3rem;
  font-weight: 600;
  margin-bottom: 15px;
  line-height: 1.3;
  color: #f5f5f7;
}

.video-stats {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 20px;
  font-size: 0.9rem;
  color: #a1a1a6;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 6px;
}

.stat-icon {
  width: 16px;
  height: 16px;
  fill: currentColor;
}

.action-buttons {
  display: flex;
  gap: 12px;
}

.btn {
  flex: 1;
  padding: 12px 20px;
  border: none;
  border-radius: 12px;
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.btn-like {
  background: rgba(255, 69, 58, 0.1);
  color: #ff453a;
  border: 1px solid rgba(255, 69, 58, 0.3);
}

.btn-like:hover {
  background: rgba(255, 69, 58, 0.2);
  transform: translateY(-2px);
}

.btn-like.liked {
  background: #ff453a;
  color: white;
}

.btn-download {
  background: rgba(48, 209, 88, 0.1);
  color: #30d158;
  border: 1px solid rgba(48, 209, 88, 0.3);
}

.btn-download:hover {
  background: rgba(48, 209, 88, 0.2);
  transform: translateY(-2px);
}

.btn-icon {
  width: 16px;
  height: 16px;
  fill: currentColor;
}

@keyframes slideInUp {
  from {
    opacity: 0;
    transform: translateY(50px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes cardSlideIn {
  from {
    opacity: 0;
    transform: translateY(60px) scale(0.9);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.like-animation {
  animation: likeScale 0.3s ease;
}

@keyframes likeScale {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}

.floating-hearts {
  position: absolute;
  pointer-events: none;
}

.heart {
  position: absolute;
  color: #ff453a;
  font-size: 20px;
  animation: floatUp 2s ease-out forwards;
}

@keyframes floatUp {
  0% {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
  100% {
    opacity: 0;
    transform: translateY(-100px) scale(0.5);
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .title {
    font-size: 2.5rem;
  }

  .video-grid {
    grid-template-columns: 1fr;
    gap: 30px;
  }

  .container {
    padding: 40px 15px;
  }
}
</style>