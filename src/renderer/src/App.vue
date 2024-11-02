<script setup lang="ts">
import TabBar from './components/TabBar/TabBar.vue'
import ChatItem from './components/ChatItem/ChatItem.vue'
import { reactive, ref } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const body = ref()

interface chatListItem {
  msg: string
  me: boolean
}

const text = ref('')
const oldList = reactive([])

const chatList: Array<chatListItem> = reactive([
  {
    me: false,
    msg: t('hello') + (localStorage.getItem('key') ? '' : `(${t('plzSetApi')})`)
  }
])
const chat = (): void => {
  chatList.push({
    me: true,
    msg: text.value
  })

  const oldMeText = text.value

  setTimeout(() => {
    body.value.lastElementChild.scrollIntoView()
    text.value = t('getting')
  }, 100)

  const apiLink = localStorage.getItem('key')
  const model = localStorage.getItem('model')
  const systemPro = localStorage.getItem('sysPro')

  fetch(`${apiLink}/v1/chat/completions`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model,
      messages: [
        { role: 'system', content: systemPro ? systemPro : '' },
        ...oldList,
        { role: 'user', content: text.value }
      ],
      temperature: 0.7,
      top_p: 0.8,
      repetition_penalty: 1.05,
      max_tokens: 512
    })
  })
    .then((response) => {
      oldList.push({ role: 'user', content: oldMeText })
      text.value = ''
      return response.json()
    })
    .then((data) => {
      if (data.hasOwnProperty!('error')) {
        text.value = '获取内容失败'
        setTimeout(() => {
          text.value = ''
        }, 1000)
        return
      }
      chatList.push({
        me: false,
        msg: data.choices[0].message.content
      })
      oldList.push({ role: 'assistant', content: data.choices[0].message.content })
    })
    .then(() => {
      body.value.lastElementChild.scrollIntoView()
    })
    .catch((error) => {
      text.value = '获取内容失败'
      setTimeout(() => {
        text.value = ''
      }, 1000)
      console.error(error)
    })
}
</script>

<template>
  <div class="flex flex-col h-screen">
    <tab-bar />
    <div ref="body" class="overflow-scroll flex-1 scroll-smooth pb-1">
      <chat-item v-for="(item, index) in chatList" :key="index" :msg="item.msg" :is-me="item.me" />
    </div>
    <div
      class="w-screen flex items-center p-2 bg-white/50 dark:bg-gray-500/50 border-t-[1px] border-solid border-black/10 dark:border-gray-400/30"
    >
      <input
        v-model="text"
        class="flex-1 p-2 rounded-lg dark:bg-gray-500/50 dark:text-white"
        @keydown.enter="chat"
      />
      <div
        class="flex items-center justify-center p-2 bg-cyan-500/50 ml-2 rounded-lg active:bg-cyan-500"
        @click="chat"
      >
        <span class="material-icons dark:text-white"> send </span>
      </div>
    </div>
  </div>
</template>
