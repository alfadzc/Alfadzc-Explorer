<script lang="ts" setup>
import { ref, onMounted } from 'vue';

const loading = ref(true);
const fadeOut = ref(false);

onMounted(() => {
  window.scrollTo(0, 0);
  document.documentElement.style.overflow = 'hidden';

  setTimeout(() => { fadeOut.value = true; }, 2200);
  setTimeout(() => {
    loading.value = false;
    document.documentElement.style.overflow = '';
  }, 2700);
});
</script>

<template>
  <Transition name="fade">
    <div v-if="loading" :style="{ opacity: fadeOut ? 0 : 1 }" style="
      position: fixed; inset: 0; z-index: 9999;
      display: flex; flex-direction: column;
      align-items: center; justify-content: center;
      background: linear-gradient(135deg, #0a0a1a 0%, #0d0d2b 40%, #0a0a1a 100%);
      transition: opacity 0.5s ease;
    ">
      <!-- Border frame -->
      <div style="
        position: absolute; inset: 12px;
        border: 1.5px solid rgba(168,85,247,0.5);
        border-radius: 16px;
        box-shadow: 0 0 30px rgba(168,85,247,0.15), inset 0 0 30px rgba(168,85,247,0.05);
        pointer-events: none;
      "/>

      <!-- Logo container -->
      <div style="position: relative; width: 220px; height: 220px; display: flex; align-items: center; justify-content: center; margin-bottom: 8px;">
        <!-- Ring 2 -->
        <div class="ring2" style="
          position: absolute; width: 210px; height: 210px;
          border-radius: 50%; border: 1px solid rgba(168,85,247,0.35);
          top: 0; left: 0;
        "/>
        <!-- Ring 1 -->
        <div class="ring1" style="
          position: absolute; width: 160px; height: 160px;
          border-radius: 50%; border: 1.5px solid rgba(168,85,247,0.55);
          top: 30px; left: 30px;
        "/>
        <!-- Coin flip logo -->
        <div class="coin-flip" style="
          width: 120px; height: 120px; border-radius: 50%;
          overflow: hidden; border: 3px solid #A855F7;
          box-shadow: 0 0 30px rgba(168,85,247,0.7), 0 0 60px rgba(168,85,247,0.3);
        ">
          <img src="/logo.png" alt="alfadzc logo" style="width: 100%; height: 100%; object-fit: cover;" />
        </div>
      </div>

      <!-- Text -->
      <div class="fade-in-text" style="text-align: center;">
        <p style="
          font-size: 13px; letter-spacing: 0.15em;
          color: rgba(255,255,255,1); font-family: monospace;
          display: flex; align-items: center; justify-content: center; gap: 4px;
        ">
          𝙿𝚕𝚎𝚊𝚜𝚎 𝚠𝚊𝚒𝚝
          <span class="dot1" style="display: inline-block;">•</span>
          <span class="dot2" style="display: inline-block;">•</span>
          <span class="dot3" style="display: inline-block;">•</span>
        </p>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
@keyframes coinFlip {
  0%   { transform: rotateY(0deg); }
  100% { transform: rotateY(360deg); }
}
@keyframes pulse-ring {
  0%   { opacity: 0.6; transform: scale(0.85); }
  50%  { opacity: 0.2; transform: scale(1.1); }
  100% { opacity: 0.6; transform: scale(0.85); }
}
@keyframes pulse-ring2 {
  0%   { opacity: 0.4; transform: scale(0.75); }
  50%  { opacity: 0.1; transform: scale(1.2); }
  100% { opacity: 0.4; transform: scale(0.75); }
}
@keyframes dotBounce {
  0%, 80%, 100% { transform: translateY(0); opacity: 0.4; }
  40%           { transform: translateY(-6px); opacity: 1; }
}
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
}
.coin-flip { animation: coinFlip 1.2s linear infinite; transform-style: preserve-3d; }
.ring1     { animation: pulse-ring  2s ease-in-out infinite; }
.ring2     { animation: pulse-ring2 2s ease-in-out infinite 0.3s; }
.dot1      { animation: dotBounce 1.2s ease-in-out infinite 0s; }
.dot2      { animation: dotBounce 1.2s ease-in-out infinite 0.2s; }
.dot3      { animation: dotBounce 1.2s ease-in-out infinite 0.4s; }
.fade-in-text { animation: fadeInUp 0.8s ease forwards 0.4s; opacity: 0; }
</style>
