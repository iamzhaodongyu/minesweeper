<script setup>
import {ref, toRefs} from "vue";

const emits = defineEmits(['flag', 'open', 'openAll'])
const props = defineProps({
  isBomb: Boolean,
  count: Number
})

const {count, isBomb} = toRefs(props)

const isOpen = ref(false)
const isFlag = ref(false)

function onClick() {
  open()
}

function onRightClick(event) {
  event.preventDefault();

  if (isOpen.value) {
    return
  }
  isFlag.value = !isFlag.value
  emits('flag')
}

function open() {
  if (isFlag.value || isOpen.value) {
    return
  }
  isOpen.value = true
  emits('open')
}

function reset() {
  isOpen.value = false
  isFlag.value = false
}

defineExpose({
  open,
  reset
})
</script>

<template>
  <div
      class="grid-item"
      :class="{'opened': isOpen}"
      @click="onClick"
      @contextmenu="onRightClick"
  >
    <template v-if="isFlag">🚩</template>
    <template v-else-if="isOpen">
      <template v-if="isBomb">💥</template>
      <template v-else>{{ count ? count : '' }}</template>
    </template>
  </div>
</template>

<style scoped>
.grid-item {
  cursor: pointer;
  border: 1px solid #eee;
  display: flex;
  justify-content: center;
  align-items: center;
}

.grid-item:not(.opened) {
  background-color: #ddd;
  border-top-color: #eee;
  border-left-color: #eee;
  border-right-color: #ccc;
  border-bottom-color: #ccc;
}
</style>
