<template>
  <v-container>
    <v-navigation-drawer app>
      <!-- -->
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
          @keyup.enter="sendMessage($event.target.value)"
          prepend-inner-icon="mdi-comment"
          class="mx-2"
          label="prepend-inner-icon"
          rows="1"
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
    if (!this.socket) {
      this.socket = io('ws://localhost:3000')
        // .on('getId', id => {
        // })
        .on('sendChat', thing => {
          vm.messages.push(thing[0])
        })
    }
  },
  methods: {
    sendMessage(message: string) {
      this.socket?.emit('chat', [message])
    }
  },
  data () {
    return {
      socket: null as Socket | null,
      messages: [] as string[]
    }
  },
})
</script>
