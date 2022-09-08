<template>
  <v-container>
    <v-navigation-drawer
      permanent
      app
    >
      <v-btn
        variant="flat"
        @click="joinRoom('number1')"
      >Room 1</v-btn>
      <v-btn
        variant="flat"
        @click="joinRoom('number2')"
      >Room 2</v-btn>
    </v-navigation-drawer>

    <v-app-bar app>
      <!-- -->
    </v-app-bar>

    <!-- Sizes your content based upon application components -->
    <v-main>

      <!-- Provides the application the proper gutter -->
      <v-container fluid>
        <v-row
          v-for="(message, idx) in messages"
          v-bind:key="idx"
          style="padding-bottom: 20px"
        >
          <v-chip>
            {{ message }}
          </v-chip>
        </v-row>

        <!-- If using vue-router -->
        <router-view></router-view>
      </v-container>
    </v-main>

    <v-footer app>
      <v-textarea
          @keydown.enter.prevent="sendMessage($event.target.value)"
          prepend-inner-icon="mdi-comment"
          class="mx-2"
          rows="1"
          v-model="message"
      ></v-textarea>
    </v-footer>
  </v-container>
</template>

<script lang='ts'>
import { defineComponent } from 'vue'
import { io, Socket } from 'socket.io-client'

export default defineComponent({
  name: 'HelloWorld',

  mounted() {
    const vm = this
    if (!this.spaceSocket) {
      this.spaceSocket = io('ws://localhost:3000/space/test_namespace')
        .on('message_room', (_room, message) => {
          vm.messages.push(message)
        })
    }
  },
  methods: {
    sendMessage(message: string) {
      if (this.spaceSocket) {
        this.spaceSocket.emit('message_room', this.currentRoom, message)
        this.clearInput()
      }
    },
    clearInput() {
      this.message = ''
    },
    joinRoom(room: string) {
      if (this.spaceSocket) {
        this.spaceSocket.emit('join_room', room)
        this.currentRoom = room
        this.joinedRooms.add(room)
      }
    }
  },
  data () {
    return {
      spaceSocket: null as Socket | null,
      messages: [] as string[],
      joinedRooms: new Set<string>(),
      currentRoom: '',
      message: ''
    }
  },
})
</script>
