<script lang="ts">
/**
 * @Author jihogrammer@gmail.com
 */
import { defineComponent, reactive, ref } from "@nuxtjs/composition-api";

export default defineComponent({
  name: "Home",
  setup() {
    let socket = ref<WebSocket>();
    const input = ref("");
    const data = reactive({
      message: "",
      logs: [] as Array<{ e: string; c: string; }>,
      status: false,
    });

    const wss = "wss://demo.piesocket.com/v3/channel_1?api_key=oCdCMcMPQpbvNjUIzqtvF1d2X2okWpDQj4AwARJuAgtjhzKxVEjQU6IdCjwm&notify_self";

    function connect() {
      console.warn("Request Connect!");
      socket.value = new WebSocket(wss);
      console.warn({ ...socket });
      socket.value.onopen = () => {
        data.status = true;
        data.logs.push({ e: "connected!", c: wss });
        if (socket.value)
          socket.value.onmessage = (response: { data: any; }) => {
            data.logs.push({ e: "received", c: response.data });
          };
      };
      console.warn({ ...socket });
    }

    function disconnect() {
      console.warn("Request Disconnect!");
      socket.value?.close();
      data.status = false;
      data.logs = [];
    }

    function sendMessage() {
      console.warn("Send message");
      socket.value?.send(input.value);
      data.logs.push({ e: "send", c: input.value });
      input.value = "";
    }

    return {
      input,
      data,
      connect,
      disconnect,
      sendMessage,
    };
  },
});
</script>

<template>
  <main>
    <header>
      <h1>Vue-Nuxt-WebSocket-Test</h1>
    </header>
    <div>
      <button @click="disconnect" v-if="data.status" class="disconnect">
        disconnect
      </button>
      <button @click="connect" v-else class="connect">
        connect
      </button>
      <form @submit.prevent="sendMessage" class="request">
        <input type="text" v-model="input" class="message">
      </form>
      <ul v-if="data.status" class="logs">
        <li
          class="log"
          v-for="(log, i) in data.logs"
          :key="i"
        >
          {{ log.e }} :: {{ log.c }}
        </li>
      </ul>
    </div>
  </main>
</template>

<style>

</style>
