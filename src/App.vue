<template>
  <div class="wrapper">

    <div class="login" v-if="showLogin">
      <div class="form">
        <h2>My chat</h2>
          <input class="login_input" type="text" v-model="user" @keypress.enter="join" placeholder="Input your name">
          <button class="btn btn--success btn--full" @click="join">Join</button>
      </div>
    </div>

    <div v-else class="chat">
        <ul class="chat_list">
          <li class="chat_list_item" v-for="item in chat" :key="item.message"><strong>{{item.username}}: </strong>{{item.message}}</li>
        </ul>
        <input class="chat_input" type="text" v-model="message" @keypress.enter="sendMessage" placeholder="Type a message">
    </div>
  </div>
  
</template>

<script>

import io from 'socket.io-client'
const socket = io.connect('http://localhost:3010')
export default {
  data () {
    return {
      user: null,
      message: null,
      chat: [],
      showLogin: true
    }
  },
  mounted () {
    socket.on('set initial messages', data => {
      this.chat = data
    })
    socket.on('new message', data => {
      this.chat = data
      if (!this.showLogin) {
        this.$nextTick(() => {
          this.setScrollToEnd()
        })
      }
    })
  },
  methods: {
    join () {
      socket.emit('join user', this.user)
      this.user = ''
      this.showLogin = false
      this.$nextTick(() => {
        this.setScrollToEnd()
      })
    },
    sendMessage () {
      socket.emit('send message', this.message)
      this.message = ''
    },
    setScrollToEnd () {
      this.$refs.body.scrollTop = this.$refs.body.scrollHeight
    }
  }
}
</script>
