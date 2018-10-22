<template>
  <div class="wrapper">

    <div class="login" v-if="showLogin">
      <div class="form">
        <h2><span>My</span>Chat</h2>
          <label for="login_input" class="sr-only">Your name</label>
          <input id="login_input" class="login_input form-control" type="text" v-model="user" @keypress.enter="join" placeholder="Input your name">
          <button class="btn btn-lg btn-primary btn-block" @click="join">Join</button>
          <p class="text-muted">© 2018</p>
      </div>
    </div>

    <div v-else class="chat">
        <ul class="chat_list">
          <li class="chat_list_item" v-for="item in chat" :key="item.message"><strong>{{item.username}}: </strong>{{item.message}}</li>
        </ul>
        <input class="chat_input form-control" type="text" v-model="message" @keypress.enter="sendMessage" placeholder="Type a message">
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
