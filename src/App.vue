<template>
  <form @submit.prevent="updateData">
    <input
      type="text"
      :value="content"
      placeholder="Type here"
      @input="(e) => (content = e.target.value)"
    />
    <button>Submit</button>
    <button @click.prevent="resetGrid">Refresh</button>
  </form>
  <div class="wrapper">
    <div v-for="(row, i) in gridStats" :key="i">
      <button v-for="(cell, j) in row" :key="j" @click="deployCats(i, j)">
        {{ cell ? cell : "-" }}
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref, computed } from "vue";
const content = ref("");
const rowNum = ref(10);
const initGrid = (rows) => {
  const cols = Math.floor(rows * 1.5);
  return [...Array(rows)].map(() => [...Array(cols)].map(() => false));
};
const gridStats = ref(initGrid(rowNum.value));
const resetGrid = () => {
  gridStats.value = initGrid(rowNum.value);
  console.log(`rowNum: ${rowNum.value}`);
};
const updateData = () => {
  rowNum.value = Number(content.value);
  gridStats.value = initGrid(rowNum.value);
  content.value = "";
  console.log(`rowNum: ${rowNum.value}`);
};
const deployCats = (row, col) => {
  gridStats.value[row][col] = !gridStats.value[row][col];
  if (gridStats.value[row][col]) {
    gridStats.value[row][col] = String.fromCharCode(
      Math.floor(Math.random() * 26 + 65),
    );
  }
  console.log(`row: ${row}, col: ${col}`);
};
</script>
<style scoped>
.wrapper button {
  width: 50px;
  height: 50px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  background-color: #fff;
  border-radius: 3px;
  border: 1px solid #c4c4c4;
  background-color: transparent;
  font-size: 2rem;
  color: #333;
  background-image: linear-gradient(
    to bottom,
    transparent,
    transparent 50%,
    rgba(0, 0, 0, 0.04)
  );
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.05),
    inset 0 1px 0 0 rgba(255, 255, 255, 0.45),
    inset 0 -1px 0 0 rgba(255, 255, 255, 0.15),
    0 1px 0 0 rgba(255, 255, 255, 0.15);
  text-shadow: 0 1px rgba(255, 255, 255, 0.4);
}
button:hover {
  background-color: #eaeaea;
}
@media (max-width: 1024px) {
  .wrapper button {
    width: 35px;
    height: 35px;
    font-size: 1.2rem;
  }
}
</style>
