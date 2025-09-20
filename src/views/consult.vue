<template>
  <div class="ai-chat-container">
    <h1 class="chat-title">👵 颐养中心智能小助手</h1>

    <!-- 聊天消息区域 -->
    <div class="chat-messages" ref="chatContainer">
      <div 
        v-for="(msg, index) in messages" 
        :key="index" 
        :class="['message', msg.type === 'user' ? 'user-message' : 'ai-message']"
      >
        <div class="message-content">{{ msg.text }}</div>
      </div>
      <!-- 正在输入状态 -->
      <div v-if="loading" class="message ai-message typing-indicator">
        <span>AI正在输入</span>
        <span class="dot">.</span><span class="dot">.</span><span class="dot">.</span>
      </div>
    </div>

    <!-- 输入区域 - 腾讯风格 -->
    <div class="input-area">
      <div class="input-wrapper">
        <input
          v-model="inputText"
          @keypress.enter="sendMessage"
          type="text"
          class="input-field"
          placeholder="有什么可以帮您？输入问题后按回车或点发送"
          maxlength="500"
        />
        <button 
          @click="sendMessage" 
          class="send-button" 
          :disabled="loading || !inputText.trim()"
          :class="{ 'send-button-disabled': loading || !inputText.trim() }"
        >
          {{ loading ? '发送中' : '发送' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Consult',
  data() {
    return {
      messages: [
        {
          type: 'ai',
          text: '您好！我是颐养中心AI顾问，可以为您解答关于设施、服务、政策等问题。请问有什么可以帮您？'
        }
      ],
      inputText: '',
      loading: false
    };
  },
  methods: {
    async sendMessage() {
      const text = this.inputText.trim();
      if (!text) return;

      // 添加用户消息
      this.messages.push({ type: 'user', text });
      this.inputText = '';
      this.loading = true;

      this.$nextTick(() => {
        this.scrollToBottom();
      });

      try {
        const response = await axios.post('http://127.0.0.1:8080/ai/rag', {
          question: text
        });

        this.messages.push({ type: 'ai', text: response.data });
      } catch (error) {
        this.messages.push({
          type: 'ai',
          text: '抱歉，服务暂时繁忙，请稍后再试。'
        });
        console.error("AI 调用失败:", error);
      } finally {
        this.loading = false;
        this.$nextTick(() => {
          this.scrollToBottom();
        });
      }
    },
    scrollToBottom() {
      this.$nextTick(() => {
        const container = this.$refs.chatContainer;
        if (container) {
          container.scrollTop = container.scrollHeight;
        }
      });
    }
  },
  mounted() {
    this.scrollToBottom();
  }
};
</script>

<style scoped>
.ai-chat-container {
  max-width: 1000px; /* ✅ 加宽到 1000px，更适应大屏 */
  margin: 20px auto;
  padding: 0 20px;
  height: 90vh;
  display: flex;
  flex-direction: column;
  background-color: #f5f5f5;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0,0,0,0.1);
}

.chat-title {
  text-align: center;
  font-size: 1.5rem;
  font-weight: 600;
  color: #333;
  margin: 20px 0 10px;
  padding-bottom: 10px;
  border-bottom: 1px solid #eaeaea;
}

.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 20px 10px;
  background-color: #fff;
  border-radius: 8px;
  margin-bottom: 16px;
  display: flex;
  flex-direction: column;
}

.message {
  max-width: 75%;
  margin-bottom: 16px;
  display: flex;
  flex-direction: column;
}

.message-content {
  padding: 12px 16px;
  border-radius: 18px;
  font-size: 15px;
  line-height: 1.5;
  word-break: break-word;
  position: relative;
}

.user-message {
  align-self: flex-end;
}

.user-message .message-content {
  background-color: #9eea6a; /* 微信绿色 */
  color: #000;
  border-bottom-right-radius: 4px;
}

.ai-message {
  align-self: flex-start;
}

.ai-message .message-content {
  background-color: #ffffff;
  color: #333;
  border: 1px solid #e0e0e0;
  border-bottom-left-radius: 4px;
}

/* 输入区域 - 腾讯风格 */
.input-area {
  flex-shrink: 0;
  padding: 0 10px 20px;
}

.input-wrapper {
  display: flex;
  align-items: center;
  background-color: #fff;
  border-radius: 20px;
  padding: 8px 12px;
  box-shadow: 0 1px 4px rgba(0,0,0,0.1);
  border: 1px solid #e0e0e0;
}

.input-field {
  flex: 1;
  border: none;
  outline: none;
  font-size: 15px;
  padding: 8px 0;
  resize: none;
  max-height: 120px;
  line-height: 1.5;
}

.input-field::placeholder {
  color: #999;
}

.send-button {
  background-color: #07c160; /* 微信绿色 */
  color: white;
  border: none;
  border-radius: 20px;
  padding: 8px 20px;
  font-size: 15px;
  font-weight: 500;
  cursor: pointer;
  margin-left: 10px;
  transition: background-color 0.2s;
}

.send-button:hover:not(:disabled) {
  background-color: #05a652;
}

.send-button-disabled {
  background-color: #cccccc !important;
  cursor: not-allowed;
}

/* 正在输入动画 */
.typing-indicator {
  align-self: flex-start;
}

.typing-indicator .message-content {
  background-color: #f0f0f0;
  color: #666;
  font-style: italic;
  display: flex;
  align-items: center;
}

.dot {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #999;
  margin: 0 2px;
  animation: typing 1.4s infinite both;
}

.dot:nth-child(2) {
  animation-delay: 0.2s;
}

.dot:nth-child(3) {
  animation-delay: 0.4s;
}

@keyframes typing {
  0% { transform: translateY(0); opacity: 0.4; }
  50% { transform: translateY(-5px); opacity: 1; }
  100% { transform: translateY(0); opacity: 0.4; }
}

/* 滚动条美化 */
.chat-messages::-webkit-scrollbar {
  width: 6px;
}

.chat-messages::-webkit-scrollbar-thumb {
  background-color: #ccc;
  border-radius: 3px;
}

.chat-messages::-webkit-scrollbar-track {
  background-color: #f1f1f1;
}
</style>