<template>
  <div ref="chatContainer" class="">
    <!-- <Greeting v-if="!memoryList.length" /> -->
    <!-- chat box -->
    <div v-for="item, index in memoryList" :key="index" class="my-6">
      <!-- chat date -->
      <!-- <Separator v-if="index % 8 === 0" :label="'New Message'" class="mb-6" /> -->
      <!-- message box -->
      <ChatBoard :data="item" />
    </div>
  </div>
</template>

<script setup>
import ChatBoard from '@/components/ChatBoard.vue';
import Greeting from '@/components/Greeting.vue';
import { useMemoryStore } from '@/store/memory';
import { UseSettingStore } from '@/store/setting';

//meta
definePageMeta({
  middleware: "auth"
})

//state
const memoryStore = useMemoryStore()
const settingStore = UseSettingStore()
const chatContainer = ref(null)

//computed
const memoryList = computed(() => memoryStore?.memoryList || [])

//life cycle
onMounted(() => {
  nextTick(() => {
    if (chatContainer.value) {
      chatContainer.value.lastElementChild?.scrollIntoView({ behavior: 'smooth' })
    }
  })
})

onUpdated(() => {
  nextTick(() => {
    if (chatContainer.value) {
      chatContainer.value.lastElementChild?.scrollIntoView({ behavior: 'smooth' })
    }
  });
});

//watcher
watch(
  () => memoryStore.memoryList.length,
  () => {
    nextTick(() => {
      chatContainer.value?.lastElementChild?.scrollIntoView({ behavior: "smooth" });
    });
  }
);

watch([() => settingStore.isEnableAutoClear, () => memoryStore.memoryList], ([isEnable, memoryList]) => {
  if (isEnable && memoryList.length > 10) {
    memoryStore.clearMemory()
  }
}, { immediate: true, deep: true })
</script>
