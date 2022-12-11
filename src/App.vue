<script setup>
import {computed, ref} from "vue";
import GridItem from "./components/GridItem.vue";

const row = ref(10)
const column = ref(10)
const bombNumber = ref(10)
const isGameOver = ref(false)
const grid = ref([])
const gridItems = ref(null)

const total = computed(() => {
  return row.value * column.value
})

function doStartGame(event) {
  const arr = []
  for (let i = 0; i < total.value; i++) {
    arr.push(0)
  }

  let bomb = bombNumber.value
  while (bomb) {
    const index = Math.random() * arr.length >> 0
    if (arr[index]) {
      continue
    }
    arr[index] = 1
    bomb--;
  }

  grid.value = arr.map((item, index) => {
    const x = index % column.value
    const y = index / column.value >> 0
    let count = 0
    for (let i = Math.max(y - 1, 0); i < Math.min(y + 2, row.value); i++) {
      for (let j = Math.max(x - 1, 0); j < Math.min(x + 2, column.value); j++) {
        if (arr[i * column.value + j] && !(i === y && j === x)) {
          count++
        }
      }
    }

    return {
      isBomb: !!item,
      count
    }
  })

  if (isGameOver.value) {
    isGameOver.value = false
  }

  if (event) {
    for (const gridItem of gridItems.value) {
      gridItem.reset()
    }
  }
}

function stopGame() {
  isGameOver.value = true
}

function onItemOpen(item, index) {
  if (grid.value[index].isBomb) {
    return stopGame();
  }
  openGridItem(item, index)
}

function openGridItem(item, index) {
  if (item.count) {
    return
  }
  const x = index % column.value;
  const y = index / column.value >> 0;

  for (let i = Math.max(0, y - 1); i < Math.min(row.value, y + 2); i++) {
    for (let j = Math.max(0, x - 1); j < Math.min(column.value, x + 2); j++) {
      if (i === y && j === x) {
        continue;
      }
      const gridItem = gridItems.value[i * row.value + j];
      gridItem.open()
    }
  }
}

function onItemFlag(item, index) {}

doStartGame()
</script>

<template>
  <header>
    <button type="button" @click="doStartGame">
      <span>🎮</span>
    </button>
  </header>
  <div id="game-grid" :class="{'game-over': isGameOver}">
    <GridItem
        ref="gridItems"
        v-for="(item, index) in grid"
        :count="item.count"
        :is-bomb="item.isBomb"
        @open="onItemOpen(item, index)"
        @flag="onItemFlag(item, index)"
    />
  </div>
</template>

<style scoped>
header {
  margin: 1rem;
  text-align: center;
}

header > button > span {
  display: inline-block;
  margin-bottom: 0.2rem;
}

#game-grid {
  display: grid;
  grid-template-rows: repeat(10, 2rem);
  grid-template-columns: repeat(10, 2rem);
  justify-content: center;
}

.game-over {
  pointer-events: none;
}
</style>
