<script setup>
import { io } from 'socket.io-client';
import { onBeforeMount, ref } from 'vue';

const socket = io('http://localhost:4001')
const messages = ref([]);
const messageText = ref('');
const roomId = '94389408-816f-4911-981c-43d2dffa3e79'
const username = '잘난감자'
const userId = '3d634f2a-b3cc-469a-8ce5-4de8cd7fef56'
const avatar = 'https://avatars0.githubusercontent.com/u/9064066?v=4&s=460'


onBeforeMount(() => {
  socket.emit('findAllMessages', { room: roomId, msid: null }, (response) => {
    messages.value = response;
  })

  socket.on('message', (message) => {
    messages.value.push(message);
  })
})

const sendMessage = () => {
  const msg = {
    room: roomId,
    text: messageText.value,
    type: "TEXT",
    user: {
      id: userId,
      username: username,
      avatar: avatar,
    },
  };
  console.log(`msg`, msg);
  socket.emit('createMessage', msg, response => {
    messageText.value = ''
  })
}

const emitTyping = () => {
  console.log('...');
}

</script>

<template>
  <main>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="80" height="80" />
    <div class="chat">
      <div class="chat-container">
        <div class="messages-container">
          <div v-for="message in messages">
            [{{ message.user.username }}]: {{ message.text }}
          </div>
        </div>
      </div>
    </div>
    <div class="message-input">
      <form @submit.prevent="sendMessage">
        <label>Message:</label>
        <input v-model="messageText" @input="emitTyping" />
        <button type="submit">Send</button>
      </form>
    </div>
  </main>
</template>

<style>
@import './assets/base.css'
</style>
