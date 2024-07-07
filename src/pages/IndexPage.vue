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
        <div v-if="streamText != '__streaming__'" class="bot-message">
          <p>{{ streamText }}</p>
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
import { Notify } from 'quasar'

const streamText = ref('__streaming__')
const userInput = ref('')
const messages = ref([])


async function sendMessage() {
  if (userInput.value.trim() !== '') {
    messages.value.push({ text: userInput.value, isUser: true })
    let userMessage = userInput.value
    userInput.value = ''

    const botMessage = { text: '', isUser: false }
    messages.value.push(botMessage)

    try {
      const url = 'http://localhost:8080/chat/71187ba6-41c5-4254-b7ca-0947964c7afc';
      const options = {
        method: 'POST',
        headers: { 'content-type': 'application/json' },
        body: JSON.stringify({
          "prompt": userMessage
        })
      };

      console.log(options)
      const response = await fetch(url, options);

      if (!response.body) {
        throw new Error('ReadableStream not supported by the browser.');
      }
      if (response.status != 200) {
        const data = await response.json();

        Notify.create({
          message: data.error,
          color: "red-6",
          position: "top-right"
        })
      }
      const reader = response.body.getReader();
      const decoder = new TextDecoder('utf-8');
      let done = false;

      streamText.value = ""
      while (!done) {
        const { value, done: readerDone } = await reader.read();
        if (readerDone) break;

        const chunk = decoder.decode(value, { stream: true });
        try {
          const json = JSON.parse(chunk);

          if (json.response) {
            streamText.value += json.response;
            try {

              botMessage.value.text += json.response;
            } catch (error) {
              console.log(error)
            }
          }
          done = json.done;
          if (done) {

          }
        } catch (error) {
          console.error('Error parsing JSON chunk:', error);
        }
      }
    } catch (error) {
      console.error('Error:', error);
    }
  }
  messages.value.push({ text: streamText.value, isUser: false })
  console.log(messages)
  streamText.value = "__streaming__"

  messages.value = messages.value.filter(message => message.text !== "");

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
  border-bottom: 2px solid #1f1f1f;
  background-color: #121212;
}

.user-message,
.bot-message {
  width: fit-content;
  display: block;
  padding: 10px;
  margin-bottom: 2%;
  border-radius: 10px;
  word-wrap: break-word;
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
