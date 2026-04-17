<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// 狀態控制
const showCountdown = ref(false)
const showZhuyin = ref(false)
const isDarkMode = ref(false)

// 時間相關
const currentTime = ref('')
const countdownSeconds = ref(0)
const isPaused = ref(false)
let timerInterval = null

// 表單資料綁定
const formData = ref({
  time: null,
  subject: '',
  proctor: ''
})

// 確認送出後的顯示資料
const savedData = ref({
  time: null,
  subject: '',
  proctor: ''
})

// 更新時間與倒數計時邏輯
const updateTime = () => {
  const now = new Date()
  // 更新現在時間
  currentTime.value = now.toLocaleTimeString('zh-TW', { hour12: false })
  
  // 處理倒數計時
  if (countdownSeconds.value > 0 && !isPaused.value) {
    countdownSeconds.value--
  }
}

onMounted(() => {
  updateTime()
  timerInterval = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  clearInterval(timerInterval)
})

// 按鈕功能方法
const toggleTimeMode = () => {
  showCountdown.value = !showCountdown.value
}

const toggleZhuyinMode = () => {
  showZhuyin.value = !showZhuyin.value
}

const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value
}

const togglePause = () => {
  isPaused.value = !isPaused.value
}

const clearData = () => {
  savedData.value = {
    time: null,
    subject: '',
    proctor: ''
  }
  countdownSeconds.value = 0
  isPaused.value = false
}

const saveData = () => {
  savedData.value = { ...formData.value }
  // 將分鐘轉換為秒數進行倒數
  if (formData.value.time && formData.value.time > 0) {
    countdownSeconds.value = Math.floor(formData.value.time * 60)
    isPaused.value = false // 設定新時間時重置為非暫停狀態
  } else {
    countdownSeconds.value = 0
  }
}

// 格式化倒數計時顯示 (MM:SS 或 HH:MM:SS)
const formattedCountdown = computed(() => {
  const h = Math.floor(countdownSeconds.value / 3600)
  const m = Math.floor((countdownSeconds.value % 3600) / 60)
  const s = countdownSeconds.value % 60
  
  const pad = (num) => String(num).padStart(2, '0')
  
  if (h > 0) {
    return `${pad(h)}:${pad(m)}:${pad(s)}`
  }
  return `${pad(m)}:${pad(s)}`
})
</script>

<template>
  <div :class="['app-wrapper', isDarkMode ? 'dark-mode' : 'light-mode', showZhuyin ? 'show-zhuyin' : '']">
    <div class="container">
      
      <!-- 標題區塊 -->
      <header class="header">
        <a href="https://www.et.tku.edu.tw/" target="_blank" class="tku-link">
          <ruby>淡<rt>ㄉㄢˋ</rt></ruby><ruby>江<rt>ㄐㄧㄤ</rt></ruby><ruby>教<rt>ㄐㄧㄠˋ</rt></ruby><ruby>科<rt>ㄎㄜ</rt></ruby><ruby>系<rt>ㄒㄧˋ</rt></ruby>
        </a>
        <h1 class="title">
          <ruby>互<rt>ㄏㄨˋ</rt></ruby><ruby>動<rt>ㄉㄨㄥˋ</rt></ruby><ruby>程<rt>ㄔㄥˊ</rt></ruby><ruby>式<rt>ㄕˋ</rt></ruby><ruby>設<rt>ㄕㄜˋ</rt></ruby><ruby>計<rt>ㄐㄧˋ</rt></ruby>_413730119
        </h1>
      </header>

      <!-- 工具按鈕區塊 -->
      <div class="tools-section">
        <button class="tool-btn" @click="toggleTimeMode">
          <ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby>
          <ruby>時<rt>ㄕˊ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby> / 
          <ruby>倒<rt>ㄉㄠˋ</rt></ruby><ruby>數<rt>ㄕㄨˇ</rt></ruby>
        </button>
        <button class="tool-btn" @click="toggleZhuyinMode">
          <ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby>
          <ruby>注<rt>ㄓㄨˋ</rt></ruby><ruby>音<rt>ㄧㄣ</rt></ruby>
        </button>
        <button class="tool-btn" @click="toggleDarkMode">
          <ruby>夜<rt>ㄧㄝˋ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby>
        </button>
      </div>

      <!-- 時間顯示區塊 -->
      <div class="time-display-section">
        <div v-if="!showCountdown">
          <div class="time-label"><ruby>現<rt>ㄒㄧㄢˋ</rt></ruby><ruby>在<rt>ㄗㄞˋ</rt></ruby><ruby>時<rt>ㄕˊ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby></div>
          <div class="time-value">{{ currentTime }}</div>
        </div>
        <div v-else>
          <div class="time-label"><ruby>倒<rt>ㄉㄠˋ</rt></ruby><ruby>數<rt>ㄕㄨˇ</rt></ruby><ruby>計<rt>ㄐㄧˋ</rt></ruby><ruby>時<rt>ㄕˊ</rt></ruby></div>
          <div class="time-value-container">
            <div class="time-value">{{ formattedCountdown }}</div>
            <button class="action-btn pause-btn" @click="togglePause" v-if="countdownSeconds > 0">
              <template v-if="!isPaused">
                <ruby>暫<rt>ㄗㄢˋ</rt></ruby><ruby>停<rt>ㄊㄧㄥˊ</rt></ruby>
              </template>
              <template v-else>
                <ruby>繼<rt>ㄐㄧˋ</rt></ruby><ruby>續<rt>ㄒㄩˋ</rt></ruby>
              </template>
            </button>
          </div>
        </div>
      </div>

      <!-- 下方表單與資料顯示區塊 -->
      <div class="content-section">
        
        <!-- 資料輸入表單 -->
        <div class="form-card">
          <div class="input-group">
            <label><ruby>時<rt>ㄕˊ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby> (<ruby>分<rt>ㄈㄣ</rt></ruby><ruby>鐘<rt>ㄓㄨㄥ</rt></ruby>)</label>
            <input type="number" v-model.number="formData.time" placeholder="請輸入考試分鐘數">
          </div>
          <div class="input-group">
            <label><ruby>科<rt>ㄎㄜ</rt></ruby><ruby>目<rt>ㄇㄨˋ</rt></ruby></label>
            <input type="text" v-model="formData.subject" placeholder="請輸入考試科目">
          </div>
          <div class="input-group">
            <label><ruby>監<rt>ㄐㄧㄢ</rt></ruby><ruby>考<rt>ㄎㄠˇ</rt></ruby><ruby>老<rt>ㄌㄠˇ</rt></ruby><ruby>師<rt>ㄕ</rt></ruby></label>
            <input type="text" v-model="formData.proctor" placeholder="請輸入監考老師姓名">
          </div>
          <button class="submit-btn" @click="saveData">
            <ruby>確<rt>ㄑㄩㄝˋ</rt></ruby><ruby>認<rt>ㄖㄣˋ</rt></ruby><ruby>設<rt>ㄕㄜˋ</rt></ruby><ruby>定<rt>ㄉㄧㄥˋ</rt></ruby>
          </button>
        </div>

        <!-- 資料顯示畫面 -->
        <div class="display-card" v-if="savedData.subject || savedData.proctor || savedData.time">
          <h3><ruby>考<rt>ㄎㄠˇ</rt></ruby><ruby>試<rt>ㄕˋ</rt></ruby><ruby>資<rt>ㄗ</rt></ruby><ruby>料<rt>ㄌㄧㄠˋ</rt></ruby></h3>
          <p><strong><ruby>科<rt>ㄎㄜ</rt></ruby><ruby>目<rt>ㄇㄨˋ</rt></ruby>：</strong> {{ savedData.subject }}</p>
          <p><strong><ruby>監<rt>ㄐㄧㄢ</rt></ruby><ruby>考<rt>ㄎㄠˇ</rt></ruby><ruby>老<rt>ㄌㄠˇ</rt></ruby><ruby>師<rt>ㄕ</rt></ruby>：</strong> {{ savedData.proctor }}</p>
          <p><strong><ruby>時<rt>ㄕˊ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby>：</strong> {{ savedData.time }} 分鐘</p>
          <button class="action-btn delete-btn" @click="clearData">
            <ruby>刪<rt>ㄕㄢ</rt></ruby><ruby>除<rt>ㄔㄨˊ</rt></ruby><ruby>資<rt>ㄗ</rt></ruby><ruby>料<rt>ㄌㄧㄠˋ</rt></ruby>
          </button>
        </div>

      </div>
    </div>
  </div>
</template>

<style>
/* 移除預設 body 邊界，讓滿版背景色生效 */
body {
  margin: 0;
  padding: 0;
  font-family: "Microsoft JhengHei", "PingFang TC", sans-serif;
}
</style>

<style scoped>
/* 滿版背景與主題切換動畫 */
.app-wrapper {
  min-height: 100vh;
  transition: background-color 0.3s, color 0.3s;
  display: flex;
  justify-content: center;
}

.light-mode {
  background-color: #f5f7fa;
  color: #333;
}

.dark-mode {
  background-color: #1a1a1a;
  color: #f0f0f0;
}

.container {
  width: 100%;
  max-width: 900px;
  padding: 20px;
  box-sizing: border-box;
}

/* Header 響應式排版 */
.header {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  margin-bottom: 30px;
  flex-wrap: wrap; /* 手機版自動折行 */
}

.tku-link {
  background-color: #005A9C;
  color: white;
  padding: 10px 20px;
  text-decoration: none;
  border-radius: 8px;
  font-weight: bold;
  transition: background-color 0.2s;
}

.tku-link:hover {
  background-color: #004070;
}

.title {
  margin: 0;
  font-size: 1.8rem;
  text-align: center;
}

/* 工具按鈕區塊 */
.tools-section {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 40px;
  flex-wrap: wrap;
}

.tool-btn {
  padding: 10px 20px;
  font-size: 1rem;
  cursor: pointer;
  border: 2px solid #ccc;
  border-radius: 8px;
  background-color: transparent;
  transition: all 0.2s;
}

.light-mode .tool-btn {
  color: #333;
  border-color: #ccc;
}

.light-mode .tool-btn:hover {
  background-color: #e0e0e0;
}

.dark-mode .tool-btn {
  color: #fff;
  border-color: #555;
}

.dark-mode .tool-btn:hover {
  background-color: #333;
}

/* 時間顯示 */
.time-display-section {
  text-align: center;
  margin-bottom: 40px;
}

.time-label {
  font-size: 1.2rem;
  margin-bottom: 10px;
  opacity: 0.8;
}

.time-value {
  font-size: 4rem;
  font-weight: bold;
  font-family: 'Courier New', Courier, monospace;
  line-height: 1;
}

.time-value-container {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
}

.action-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
  font-size: 1.1rem;
  transition: background-color 0.2s;
}

.pause-btn {
  background-color: #ff9800;
  color: white;
}

.pause-btn:hover {
  background-color: #e68a00;
}

.delete-btn {
  background-color: #f44336;
  color: white;
  width: 100%;
  margin-top: 20px;
  padding: 12px;
}

.delete-btn:hover {
  background-color: #da190b;
}

/* 下方內容區塊 (表單與顯示) */
.content-section {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

/* 在較大螢幕上並排顯示 */
@media (min-width: 768px) {
  .content-section {
    flex-direction: row;
    align-items: flex-start;
  }
}

.form-card, .display-card {
  flex: 1;
  padding: 25px;
  border-radius: 12px;
  box-sizing: border-box;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

.light-mode .form-card, .light-mode .display-card {
  background-color: #ffffff;
}

.dark-mode .form-card, .dark-mode .display-card {
  background-color: #2a2a2a;
}

.input-group {
  margin-bottom: 15px;
  display: flex;
  flex-direction: column;
}

.input-group label {
  margin-bottom: 8px;
  font-weight: bold;
}

.input-group input {
  padding: 10px;
  border-radius: 6px;
  border: 1px solid #ccc;
  font-size: 1rem;
}

.dark-mode .input-group input {
  background-color: #444;
  color: #fff;
  border-color: #666;
}

.submit-btn {
  width: 100%;
  padding: 12px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 1.1rem;
  cursor: pointer;
  margin-top: 10px;
  font-weight: bold;
}

.submit-btn:hover {
  background-color: #45a049;
}

.display-card h3 {
  margin-top: 0;
  border-bottom: 2px solid #ccc;
  padding-bottom: 10px;
}

.display-card p {
  font-size: 1.1rem;
  margin: 15px 0;
}

/* 注音符號 (Ruby/Rt) 控制 */
ruby {
  ruby-align: center;
}

rt {
  display: none; /* 預設隱藏注音 */
  font-size: 0.6em;
  color: #666;
}

.dark-mode rt {
  color: #aaa;
}

/* 當處於 show-zhuyin 模式時，顯示所有 rt 標籤 */
.show-zhuyin rt {
  display: block;
}
</style>
