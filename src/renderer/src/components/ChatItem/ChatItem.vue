<script setup lang="ts">
import Markdown from 'vue3-markdown-it'

withDefaults(
  defineProps<{
    isMe?: boolean
    isSys?: boolean
    msg: string
  }>(),
  {
    isMe: true
  }
)
</script>

<template>
  <div v-if="isSys" class="flex w-[calc(100%-56x)] pl-2 py-1">
    <div class="w-10 mr-2">
      <img class="rounded-full" src="../../assets/system.jpg" alt="chatGPT" />
    </div>
    <div
      class="bg-white rounded-xl rounded-tl-md p-3 overflow-auto h-auto dark:bg-gray-500/50 w-auto max-w-[calc(100vw-112px)] shadow-md shadow-black/10"
    >
      <span class="w-10 whitespace-normal break-words text-sm">{{ msg }}</span>
    </div>
  </div>
  <div v-else-if="isMe && !isSys" class="flex w-[calc(100vw-56px)] pr-2 py-1 justify-end ml-[56px]">
    <div
      class="text-start bg-cyan-500 rounded-xl rounded-tr-md p-3 overflow-auto h-auto dark:bg-cyan-900 dark:text-white/70 w-auto max-w-[calc(100vw-112px)] shadow-md shadow-cyan-700/10"
    >
      <span class="w-10 whitespace-normal break-words text-white text-sm">{{ msg }}</span>
    </div>
    <div class="w-10 ml-2 rounded-lg">
      <img
        class="rounded-full dark:bg-gary-500/50 bg-white/50"
        src="../../assets/user.jpg"
        alt="me"
      />
    </div>
  </div>
  <div v-else class="flex w-[calc(100%-56x)] pl-2 py-1">
    <div class="w-10 mr-2">
      <img class="rounded-full" src="../../assets/logo.png" alt="chatGPT" />
    </div>
    <div
      class="bg-white rounded-xl rounded-tl-md p-3 overflow-auto h-auto dark:bg-gray-500/50 w-auto max-w-[calc(100vw-112px)] shadow-md shadow-black/10"
    >
      <Markdown :source="msg" class="dark:text-white/70 select-text text-sm" />
    </div>
  </div>
</template>
