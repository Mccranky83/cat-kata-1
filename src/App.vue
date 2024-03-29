<template>
  <form @submit.prevent="updateData">
    <span class="h1">Width: {{ content }}</span>
    <span class="h1" v-show="content">
      <br />
      Height: {{ Math.floor(content * 1.5) }}
    </span>
    <br />
    <input
      type="text"
      :value="content"
      placeholder="Type here"
      @input="(e) => (content = e.target.value)"
    />
    <button>Submit</button>
    <button @click.prevent="resetGrid">Reset</button>
    <button @click.prevent="refreshGrid">Refresh</button>
  </form>
  <div class="wrapper">
    <div v-for="(row, i) in gridStats" :key="i">
      <button
        v-for="(cell, j) in row"
        :key="j"
        :class="[cell ? 'deployed' : '']"
        @click="deployCats(i, j)"
      >
        {{ cell ? cell : "-" }}
      </button>
    </div>
  </div>
  <div>
    <p v-show="output" class="h1">Mini Distance: {{ output }}</p>
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
let coords = [];
const output = ref();
const resetGrid = () => {
  gridStats.value = initGrid(rowNum.value);
  coords = [];
  output.value = undefined;
};
const refreshGrid = () => {
  gridStats.value.forEach((t, i) => {
    t.forEach((e, j) => {
      gridStats.value[i][j] = !gridStats.value[i][j];
      deployCats(i, j);
    });
  });
};
const updateData = () => {
  if (Number(content.value) > 100) {
    alert("Number is way too large!");
    content.value = "";
    return 0;
  }
  rowNum.value = Number(content.value);
  gridStats.value = initGrid(rowNum.value);
  content.value = "";
  coords = [];
  output.value = undefined;
};
const deployCats = (row, col) => {
  gridStats.value[row][col] = !gridStats.value[row][col];
  coords = coords.filter(
    (t) => JSON.stringify(t) !== JSON.stringify([row, col]),
  );
  if (gridStats.value[row][col]) {
    gridStats.value[row][col] = String.fromCharCode(
      Math.floor(Math.random() * 26 + 65),
    );
    coords.push([row, col]);
  }
  coords.length && calculateDistance();
};
const calculateDistance = () => {
  let distances = coords
    .map((coord, i) =>
      coords
        .filter((_, otherIndex) => i != otherIndex)
        .map((otherCoord) => [
          Math.abs(coord[0] - otherCoord[0]),
          Math.abs(coord[1] - otherCoord[1]),
        ]),
    )
    .flat()
    .filter(
      (coord, i, self) =>
        i == self.findIndex((t) => t[0] == coord[0] && t[1] == coord[1]), // Remove duplicate values
    )
    .map((t) => Math.hypot(...t).toFixed(3));
  output.value = distances.length
    ? distances.reduce((acc, cur) => Math.min(acc, cur))
    : undefined;
};
</script>
<style scoped>
.h1 {
  font-size: 1.8rem;
}
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
button.deployed {
  background-color: #e67e80;
}
@media (max-width: 1024px) {
  .wrapper button {
    width: 35px;
    height: 35px;
    font-size: 1.2rem;
  }
}
</style>
