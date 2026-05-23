<template>
  <div class="app">
    <div class="container">
      <!-- 标题 -->
      <h1 class="title">🔐 密码找回神器</h1>
      <p class="subtitle">支持全网所有应用，一键找回，安全可靠*</p>

      <!-- 第一步：选择应用 -->
      <div v-if="step === 1" class="step">
        <h2>选择你要找回密码的应用</h2>
        <div class="app-grid">
          <div
            v-for="app in apps"
            :key="app.name"
            :class="['app-card', { selected: selectedApp?.name === app.name }]"
            @click="selectApp(app)"
          >
            <span class="app-icon">{{ app.icon }}</span>
            <span class="app-name">{{ app.name }}</span>
          </div>
        </div>
        <button class="btn" :disabled="!selectedApp" @click="step = 2">
          下一步
        </button>
      </div>

      <!-- 第二步：输入账号 -->
      <div v-if="step === 2" class="step">
        <h2>输入你的{{ selectedApp?.name }}账号</h2>
        <div class="selected-app-badge">
          <span>{{ selectedApp?.icon }}</span>
          <span>{{ selectedApp?.name }}</span>
          <span class="change-btn" @click="step = 1">更换</span>
        </div>
        <input
          v-model="account"
          class="input"
          :placeholder="selectedApp?.placeholder"
          @keyup.enter="startRetrieve"
        />
        <button class="btn btn-primary" :disabled="!account.trim()" @click="startRetrieve">
          🚀 一键找回
        </button>
      </div>

      <!-- 第三步：进度条 -->
      <div v-if="step === 3" class="step">
        <h2>正在找回密码中...</h2>
        <div class="progress-wrapper">
          <div class="progress-bar">
            <div class="progress-fill" :style="{ width: progress + '%' }">
              <span class="progress-text">{{ progress }}%</span>
            </div>
          </div>
        </div>
        <div class="tip-box">
          <p class="tip-text">{{ currentTip }}</p>
        </div>
      </div>

      <!-- 第四步：结果 -->
      <div v-if="step === 4" class="step result-step">
        <div class="success-icon">🎉</div>
        <h2>找回成功！</h2>
        <div class="result-card">
          <div class="result-row">
            <span class="result-label">应用</span>
            <span class="result-value">{{ selectedApp?.icon }} {{ selectedApp?.name }}</span>
          </div>
          <div class="result-row">
            <span class="result-label">账号</span>
            <span class="result-value">{{ account }}</span>
          </div>
          <div class="result-row">
            <span class="result-label">密码</span>
            <span class="result-value password">*********</span>
          </div>
        </div>
        <div class="disclaimer-box">
          <span class="disclaimer-icon">⚠️</span>
          <span>密码已脱敏处理，请妥善保管</span>
        </div>
        <button class="btn" @click="reset">再找回一个</button>
      </div>

      <p class="footer-note"></p>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'App',
  setup() {
    const step = ref(1)
    const selectedApp = ref(null)
    const account = ref('')
    const progress = ref(0)

    const apps = [
      { name: 'QQ', icon: '🐧', placeholder: '请输入QQ号' },
      { name: '微信', icon: '💬', placeholder: '请输入微信号/手机号' },
      { name: '抖音', icon: '🎵', placeholder: '请输入抖音号' },
      { name: '微博', icon: '📱', placeholder: '请输入微博账号' },
      { name: '支付宝', icon: '💰', placeholder: '请输入支付宝账号' },
      { name: '淘宝', icon: '🛒', placeholder: '请输入淘宝账号' },
      { name: 'B站', icon: '📺', placeholder: '请输入B站账号' },
      { name: '小红书', icon: '📕', placeholder: '请输入小红书账号' },
      { name: '王者荣耀', icon: '🎮', placeholder: '请输入游戏账号' },
      { name: '原神', icon: '⚔️', placeholder: '请输入米哈游账号' },
      { name: '网易邮箱', icon: '📧', placeholder: '请输入邮箱地址' },
      { name: 'GitHub', icon: '💻', placeholder: '请输入GitHub用户名' },
    ]

    const tips = [
      '🌐 正在联网搜索...',
      '🧠 调用AI大模型...',
      '⚡ 密码已成功定位，正在尝试暴力破解MD5...',
    ]

    const currentTip = computed(() => {
      const p = progress.value
      if (p < 20) return tips[0]
      if (p < 60) return tips[1]
      return tips[2]
    })

    const selectApp = (app) => {
      selectedApp.value = app
    }

    const startRetrieve = () => {
      if (!account.value.trim()) return
      step.value = 3
      progress.value = 0

      const timer = setInterval(() => {
        progress.value++
        if (progress.value >= 100) {
          progress.value = 100
          clearInterval(timer)
          setTimeout(() => {
            step.value = 4
          }, 500)
        }
      }, 50)
    }

    const reset = () => {
      step.value = 1
      selectedApp.value = null
      account.value = ''
      progress.value = 0
    }

    return {
      step,
      selectedApp,
      account,
      progress,
      currentTip,
      apps,
      selectApp,
      startRetrieve,
      reset,
    }
  },
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: linear-gradient(135deg, #0c0c1d 0%, #1a1a3e 50%, #0c0c1d 100%);
  min-height: 100vh;
  color: #e0e0e0;
}

.app {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
}

.container {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 24px;
  padding: 40px;
  max-width: 520px;
  width: 100%;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.title {
  text-align: center;
  font-size: 28px;
  margin-bottom: 4px;
  background: linear-gradient(90deg, #f7971e, #ffd200, #f7971e);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.subtitle {
  text-align: center;
  font-size: 13px;
  color: #888;
  margin-bottom: 30px;
}

.step {
  animation: fadeIn 0.4s ease;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.step h2 {
  font-size: 18px;
  margin-bottom: 20px;
  text-align: center;
  color: #fff;
}

/* 应用选择网格 */
.app-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  margin-bottom: 24px;
}

.app-card {
  background: rgba(255, 255, 255, 0.05);
  border: 2px solid rgba(255, 255, 255, 0.08);
  border-radius: 16px;
  padding: 16px 8px;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.app-card:hover {
  background: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 215, 0, 0.3);
  transform: translateY(-2px);
}

.app-card.selected {
  background: rgba(255, 215, 0, 0.1);
  border-color: #ffd200;
  box-shadow: 0 0 20px rgba(255, 215, 0, 0.15);
}

.app-icon {
  font-size: 32px;
  display: block;
  margin-bottom: 6px;
}

.app-name {
  font-size: 13px;
  color: #ccc;
}

.app-card.selected .app-name {
  color: #ffd200;
}

/* 按钮 */
.btn {
  display: block;
  width: 100%;
  padding: 14px 24px;
  font-size: 16px;
  font-weight: 600;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  background: rgba(255, 255, 255, 0.1);
  color: #fff;
}

.btn:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.18);
  transform: translateY(-1px);
}

.btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.btn-primary {
  background: linear-gradient(135deg, #f7971e, #ffd200);
  color: #1a1a3e;
}

.btn-primary:hover:not(:disabled) {
  background: linear-gradient(135deg, #ffa734, #ffe033);
  box-shadow: 0 4px 20px rgba(255, 215, 0, 0.3);
}

/* 已选应用徽标 */
.selected-app-badge {
  display: flex;
  align-items: center;
  gap: 8px;
  background: rgba(255, 215, 0, 0.1);
  border: 1px solid rgba(255, 215, 0, 0.3);
  border-radius: 10px;
  padding: 10px 16px;
  margin-bottom: 20px;
  font-size: 15px;
}

.change-btn {
  margin-left: auto;
  font-size: 13px;
  color: #ffd200;
  cursor: pointer;
}

.change-btn:hover {
  text-decoration: underline;
}

/* 输入框 */
.input {
  width: 100%;
  padding: 14px 16px;
  font-size: 16px;
  border: 2px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  background: rgba(255, 255, 255, 0.05);
  color: #fff;
  outline: none;
  transition: all 0.3s ease;
  margin-bottom: 20px;
}

.input:focus {
  border-color: #ffd200;
  box-shadow: 0 0 15px rgba(255, 215, 0, 0.1);
}

.input::placeholder {
  color: #666;
}

/* 进度条 */
.progress-wrapper {
  margin: 30px 0;
}

.progress-bar {
  height: 32px;
  background: rgba(255, 255, 255, 0.08);
  border-radius: 16px;
  overflow: hidden;
  position: relative;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #f7971e, #ffd200);
  border-radius: 16px;
  transition: width 0.1s linear;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding-right: 12px;
  min-width: 50px;
  position: relative;
}

.progress-fill::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(
    90deg,
    transparent 0%,
    rgba(255, 255, 255, 0.2) 50%,
    transparent 100%
  );
  animation: shimmer 1.5s infinite;
}

@keyframes shimmer {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

.progress-text {
  font-size: 13px;
  font-weight: 700;
  color: #1a1a3e;
  position: relative;
  z-index: 1;
}

/* 提示 */
.tip-box {
  min-height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.tip-text {
  font-size: 15px;
  color: #ffd200;
  text-align: center;
  animation: tipPulse 1.5s ease-in-out infinite;
}

@keyframes tipPulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.6; }
}

/* 结果 */
.result-step {
  text-align: center;
}

.success-icon {
  font-size: 64px;
  margin-bottom: 10px;
  animation: bounce 0.6s ease;
}

@keyframes bounce {
  0% { transform: scale(0); }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); }
}

.result-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 20px;
  margin: 20px 0;
  text-align: left;
}

.result-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
}

.result-row:last-child {
  border-bottom: none;
}

.result-label {
  color: #888;
  font-size: 14px;
}

.result-value {
  color: #fff;
  font-size: 15px;
  font-weight: 500;
}

.result-value.password {
  color: #ffd200;
  font-size: 20px;
  letter-spacing: 3px;
}

.disclaimer-box {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  background: rgba(255, 170, 0, 0.1);
  border: 1px solid rgba(255, 170, 0, 0.3);
  border-radius: 8px;
  padding: 10px 16px;
  margin-bottom: 20px;
  font-size: 14px;
  color: #ffaa00;
  font-weight: 500;
}

.disclaimer-icon {
  font-size: 18px;
}

.footer-note {
  text-align: center;
  font-size: 11px;
  color: #444;
  margin-top: 24px;
  line-height: 1.5;
}
</style>
