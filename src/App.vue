<template>
  <div style="display: flex; justify-content: center; align-items: center">
    <ul style="height: 600px; overflow-y: auto">
      <li v-for="message in messages">{{ message }}</li>
    </ul>
    <div style="font-size: 200px; position: relative">
      🔔
      <div
        style="
          position: absolute;
          right: 60px;
          top: 60px;
          width: 64px;
          height: 64px;
          border-radius: 50%;
          background: red;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 32px;
        "
      >
        {{ messageCount }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
const messages = ref<string[]>([]);
const messageCount = ref<number>(0);
onMounted(() => {
  setupSSE();
});
const setupSSE = () => {
  // 连接 SSE 服务器
  const eventSource = new EventSource("http://localhost:8080/sse");
  // 监听服务器发送的事件
  eventSource.addEventListener("elysia", function (message) {
    messages.value.push(message.data);
    messageCount.value++;
  });

  eventSource.onerror = function (error) {
    // 处理连接错误
    console.error("Error occurred:", error);
  };

  // 在页面关闭或不需要连接时，记得关闭EventSource
  window.onbeforeunload = function () {
    eventSource.close();
  };
};
</script>
