<template>
  <div>
    <h1 @click="sendEvent">Click to Send Message</h1>
    <ul>
      <li v-for="message in messages">{{ message }}</li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';

const messages = ref<string[]>([])
onMounted(() => {
  setupSSE();
})
const setupSSE = () => {
  // 连接 SSE 服务器
  const eventSource = new EventSource('http://localhost:8080/sse');
  // 处理接收到的消息
  eventSource.onmessage = (event) => {
    messages.value.push(event.data);
  };
  // 处理错误
  eventSource.onerror = (error) => {
    console.error('SSE Error:', error);
    eventSource.close();
  };
}
const sendEvent = () => {
  fetch(`http://localhost:8080/sse/sendEvent`)
}
</script>