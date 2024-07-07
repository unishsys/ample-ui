<template>
  <q-page class="flex flex-center">
    <q-card class="chat-card">
      <q-card-section>
        <div class="chat-header">
          <h4>Interface</h4>
        </div>
      </q-card-section>

      <q-card-section class="chat-section">
        <div v-for="(message, index) in messages" :key="index"
          :class="{ 'user-message': message.isUser, 'bot-message': !message.isUser }">
          <p>{{ message.text }}</p>
        </div>
      </q-card-section>

      <q-card-section class="input-section">
        <q-input v-model="userInput" placeholder="Type your message..." @keyup.enter="sendMessage"></q-input>
        <q-btn label="Send" @click="sendMessage"></q-btn>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref } from 'vue'

const userInput = ref('')
const messages = ref([{ text: "foo", isUser: true }, { text: "bar", isUser: false }])

function sendMessage() {
  if (userInput.value.trim() !== '') {
    messages.value.push({ text: userInput.value, isUser: true })
    userInput.value = ''

    // Simulate a response from the bot
    setTimeout(() => {
      messages.value.push({ text: 'This is a simulated response from ChatGPT', isUser: false })
    }, 1000)
  }
}
</script>

<style scoped>
.chat-card {
  width: 100%;
  max-width: 600px;
  margin: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  background-color: #121212;
}

.chat-header {
  text-align: center;
  font-size: 1.2em;
  font-weight: bold;
  color: #bb86fc;
  border-bottom: 2px solid #bb86fc;
}

.chat-section {
  display: flex;
  flex-direction: column;
  max-height: 400px;
  overflow-y: auto;
  padding: 10px;
  border-bottom: 2px solid #1f1f1f;
  background-color: #121212;
}

.user-message,
.bot-message {
  width: fit-content;
  display: block;
  padding: 10px;
  border-radius: 10px;
  word-wrap: break-word;
  text-align: center;
  align-self: flex-start;
  align-content: center;
}

.user-message {
  background-color: #1e88e5;
  color: #ffffff;
  align-self: flex-end;
  /* Align to the right for user messages */
}

.bot-message {
  background-color: #2e7d32;
  color: #ffffff;
  align-self: flex-start;
  /* Align to the left for bot messages */
}

.input-section {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 5px 10px;
  /* Adjusted padding */
  background-color: #1f1f1f;
  border-top: 2px solid #1f1f1f;
}

.q-input {
  width: calc(100% - 60px);
  margin-right: 10px;
  background-color: #1f1f1f;
  color: #ffffff;
}

.q-btn {
  width: 50px;
  background-color: #bb86fc;
  color: #121212;
}

.input-section {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
  background-color: #1f1f1f;
  border-top: 2px solid #1f1f1f;
}
</style>
