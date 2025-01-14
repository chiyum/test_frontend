<template>
  <div>
    <label :for="inputId" class="form-label">{{ label }}</label>
    <input :type="type" v-model="inputValue" class="form-control" :id="inputId" placeholder="">
  </div>
</template>

<script setup lang="ts">
import { defineProps, defineModel, ref } from 'vue'

withDefaults(defineProps<{
  label: string,
  type?: 'text' | 'password' | 'email' | 'number' | 'tel' | 'url' | 'search' | 'date' | 'time' | 'datetime-local' | 'month' | 'week' | 'color' | 'file' | 'range',
}>(),{
  type: 'text'
})

const inputId = ref("id")

const inputValue = defineModel()

const generateRandomId = (length: number = 8) : string => {
  // 定義可用的字元集合，包括大小寫字母與數字
  const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
  let result = ''; // 初始化結果字串
  for (let i = 0; i < length; i++) {
    // 隨機選取一個字元並追加到結果字串
    const randomIndex = Math.floor(Math.random() * characters.length);
    result += characters[randomIndex];
  }
  return result; // 返回生成的字串
}

// 未防止 id 重複，初始化時就產生一個隨機 id
const init = () => {
  inputId.value = generateRandomId()
}

init()

</script>

<style lang="scss" scoped>

</style>
