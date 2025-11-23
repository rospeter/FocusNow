<script setup>
import { ref, computed, onUnmounted, watch } from "vue";

// 🎵 1. 定义音效
const finishSound = new Audio("/ding.mp3");

// --- 2. 计时器状态 ---
const totalSeconds = ref(25 * 60);
const remainingSeconds = ref(25 * 60);
const isRunning = ref(false);
let timerInterval = null;

// 自定义时间状态
const isCustomizing = ref(false);
const customTimeInput = ref("");

// --- 3. 计算属性 ---
const timeLeftDisplay = computed(() => {
  const minutes = Math.floor(remainingSeconds.value / 60);
  const seconds = remainingSeconds.value % 60;
  return `${minutes}:${seconds < 10 ? "0" + seconds : seconds}`;
});

// --- 4. 核心函数 ---
const updateTimer = () => {
  if (remainingSeconds.value <= 0) {
    clearInterval(timerInterval);
    isRunning.value = false;
    remainingSeconds.value = 0;
    finishSound.play();
    document.title = "✅ 完成！FocusNow";
    return;
  }
  remainingSeconds.value--;
};

const toggleTimer = () => {
  isRunning.value = !isRunning.value;
  if (isRunning.value) {
    timerInterval = setInterval(updateTimer, 1000);
  } else {
    clearInterval(timerInterval);
  }
};

const setTimer = (minutes) => {
  isRunning.value = false;
  clearInterval(timerInterval);
  totalSeconds.value = minutes * 60;
  remainingSeconds.value = minutes * 60;
  document.title = "FocusNow - 专注时钟";
};

// --- 5. 自定义时间逻辑 ---
const enableCustomMode = () => {
  isCustomizing.value = true;
  setTimeout(() => document.getElementById("custom-timer-input")?.focus(), 50);
};

const confirmCustomTime = () => {
  const minutes = parseFloat(customTimeInput.value);
  if (minutes > 0 && !isNaN(minutes)) {
    setTimer(minutes);
  }
  isCustomizing.value = false;
  customTimeInput.value = "";
};

const cancelCustomMode = () => {
  isCustomizing.value = false;
  customTimeInput.value = "";
};

// --- 6. 杂项 ---
// 标题联动
watch(timeLeftDisplay, (newTime) => {
  if (isRunning.value) document.title = `(${newTime}) FocusNow`;
});

// 销毁清理
onUnmounted(() => {
  if (timerInterval) clearInterval(timerInterval);
});
</script>

<template>
  <section class="card focus-card">
    <h1 class="logo">FocusNow<span class="dot">.</span></h1>
    <div class="timer-display">{{ timeLeftDisplay }}</div>

    <div class="timer-controls">
      <div v-if="!isCustomizing" class="btn-group">
        <button class="btn btn-preset" @click="setTimer(45)">45m</button>
        <button class="btn btn-preset" @click="setTimer(25)">25m</button>
        <button class="btn btn-preset" @click="setTimer(10)">10m</button>
        <button
          class="btn btn-icon-only"
          @click="enableCustomMode"
          title="自定义"
        >
          ⚙️
        </button>
        <button class="btn btn-primary" @click="toggleTimer">
          {{ isRunning ? "暂停" : "开始专注" }}
        </button>
      </div>

      <div v-else class="custom-input-box">
        <input
          id="custom-timer-input"
          type="number"
          v-model="customTimeInput"
          placeholder="分钟"
          @keyup.enter="confirmCustomTime"
          @keyup.esc="cancelCustomMode"
        />
        <button class="btn-mini-action confirm" @click="confirmCustomTime">
          Go
        </button>
        <button class="btn-mini-action cancel" @click="cancelCustomMode">
          ×
        </button>
      </div>
    </div>
  </section>
</template>

<style scoped>
.card {
  background: #ffffff;
  border-radius: 24px;
  box-shadow: 0 10px 30px -10px rgba(0, 0, 0, 0.05);
  padding: 25px;
  transition: transform 0.2s;
}
.focus-card {
  flex: 0 0 auto;
  text-align: center;
  border: 1px solid rgba(0, 0, 0, 0.02);
}
.logo {
  font-size: 1.2rem;
  font-weight: 800;
  color: #374151;
  margin-bottom: 15px;
  letter-spacing: -0.5px;
}
.dot {
  color: #6366f1;
}
.timer-display {
  font-size: 4.5rem;
  font-weight: 800;
  font-family: "Courier New", Courier, monospace;
  color: #111827;
  margin: 10px 0 20px 0;
  letter-spacing: -2px;
}

.timer-controls {
  display: flex;
  justify-content: center;
  height: 48px;
}
.btn-group {
  display: flex;
  gap: 10px;
  animation: fadeIn 0.3s ease;
}

.btn {
  border: none;
  border-radius: 12px;
  padding: 10px 20px;
  font-size: 0.95rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}
.btn:active {
  transform: scale(0.96);
}
.btn-primary {
  background-color: #6366f1;
  color: white;
  box-shadow: 0 4px 12px rgba(99, 102, 241, 0.3);
}
.btn-primary:hover {
  background-color: #4f46e5;
}
.btn-preset {
  background-color: #f3f4f6;
  color: #4b5563;
}
.btn-preset:hover {
  background-color: #e5e7eb;
}

.btn-icon-only {
  width: 42px;
  height: 42px;
  border-radius: 50%;
  background: #f3f4f6;
  border: none;
  cursor: pointer;
  font-size: 1.1rem;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
}
.btn-icon-only:hover {
  background: #e5e7eb;
  transform: rotate(45deg);
}

.custom-input-box {
  display: flex;
  align-items: center;
  gap: 8px;
  background: #fff;
  border: 2px solid #6366f1;
  border-radius: 50px;
  padding: 4px 6px 4px 15px;
  animation: slideUp 0.2s ease;
  box-shadow: 0 4px 12px rgba(99, 102, 241, 0.2);
}
.custom-input-box input {
  width: 60px;
  border: none;
  outline: none;
  font-size: 1.1rem;
  font-weight: bold;
  color: #6366f1;
  background: transparent;
}
.custom-input-box input::-webkit-outer-spin-button,
.custom-input-box input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.btn-mini-action {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: none;
  cursor: pointer;
  font-weight: bold;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: 0.2s;
}
.confirm {
  background: #6366f1;
  color: white;
  font-size: 0.8rem;
}
.confirm:hover {
  background: #4f46e5;
}
.cancel {
  background: transparent;
  color: #9ca3af;
  font-size: 1.2rem;
}
.cancel:hover {
  color: #ef4444;
  background: #fee2e2;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(5px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
@keyframes slideUp {
  from {
    opacity: 0;
    transform: scale(0.9);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
