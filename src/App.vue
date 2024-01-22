<template>
  <div>
    <div v-if="gameOver" class="text-center mt-8">
      <h2 class="text-2xl font-bold text-red-500">Game Over!</h2>
      <button @click="restartGame" class="mt-4 bg-blue-500 text-white px-4 py-2 rounded">
        Restart Game
      </button>
    </div>

    <div v-else>
      <h1 class="text-3xl text-center">🐍 Snake 🐍</h1>
      <p class="text-center">Move with W A S D</p>
      <div class="flex items-center justify-center h-full">
        <div class="grid grid-cols-15 gap-1 mt-4">
          <div v-for="(_, rowIndex) in gridSize" :key="rowIndex" class="flex">
            <div
              v-for="(_, colIndex) in gridSize"
              :key="colIndex"
              class="w-8 h-8 border border-gray-300 flex items-center justify-center"
            >
              <div
                v-if="snake.some((segment) => segment.x === colIndex && segment.y === rowIndex)"
                class="w-6 h-6 bg-green-500 rounded-full"
              ></div>
              <div
                v-else-if="food.x === colIndex && food.y === rowIndex"
                class="w-6 h-6 bg-red-500 rounded-full"
              ></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
const getRandomCoordinate = () => Math.floor(Math.random() * 15);

const gridSize = 15;
const snake = ref([
  { x: 3, y: 0 },
  { x: 2, y: 0 },
  { x: 1, y: 0 }
]);
const food = ref({ x: getRandomCoordinate(), y: getRandomCoordinate() });
const direction = ref('right');
const gameOver = ref(false);

function moveSnake(): void {
  if (gameOver.value) return;

  const head = { ...snake.value[0] };

  switch (direction.value) {
    case 'up':
      head.y = (head.y - 1 + gridSize) % gridSize;
      break;
    case 'down':
      head.y = (head.y + 1) % gridSize;
      break;
    case 'left':
      head.x = (head.x - 1 + gridSize) % gridSize;
      break;
    case 'right':
      head.x = (head.x + 1) % gridSize;
      break;
  }

  // Check if the snake hits its own tail
  if (snake.value.some((segment) => segment.x === head.x && segment.y === head.y)) {
    gameOver.value = true;
    return;
  }

  snake.value.unshift(head);

  // Check if snake eats food
  if (head.x === food.value.x && head.y === food.value.y) {
    food.value = { x: getRandomCoordinate(), y: getRandomCoordinate() };
  } else {
    snake.value.pop();
  }
}

function handleKeyPress(event: KeyboardEvent): void {
  if (gameOver.value) return;

  switch (event.key) {
    case 'w':
      direction.value = 'up';
      break;
    case 's':
      direction.value = 'down';
      break;
    case 'a':
      direction.value = 'left';
      break;
    case 'd':
      direction.value = 'right';
      break;
  }
}

function restartGame(): void {
  snake.value = [
    { x: 3, y: 0 },
    { x: 2, y: 0 },
    { x: 1, y: 0 }
  ];
  food.value = { x: getRandomCoordinate(), y: getRandomCoordinate() };
  direction.value = 'right';
  gameOver.value = false;
}

onMounted(() => {
  setInterval(moveSnake, 100);
  window.addEventListener('keydown', handleKeyPress);
});
</script>
