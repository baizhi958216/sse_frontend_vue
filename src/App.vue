<template>
  <div style="display: flex;justify-content: center;align-items: center">
    <ul style="height:600px;overflow-y:auto;">
      <li v-for="message in messages">{{ message }}</li>
    </ul>
    <div style="font-size: 200px;position: relative">🔔
      <div style="position: absolute;right: 60px;top: 60px; width: 64px;height: 64px;border-radius: 50%;background: red;display: flex;justify-content: center;align-items: center;font-size: 32px">
        {{ messageCount }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
const messages = ref<string[]>([])
const messageCount = ref<number>(0)
onMounted(() => {
  setupSSE();
})
const setupSSE = () => {
  // 连接 SSE 服务器
  const eventSource = new EventSource('http://localhost:8080/sse');
  // 处理接收到的消息
  eventSource.onmessage = (event) => {
    messages.value.push(event.data);
    messageCount.value+=1
  };
  // 处理错误
  eventSource.onerror = (error) => {
    console.error('SSE Error:', error);
    eventSource.close();
  };
}
</script>
