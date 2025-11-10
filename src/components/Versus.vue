<script setup>
import { ref, nextTick } from "vue";

const inputValue = ref("");
const strings = ref([]);
const currentIndex = ref(0);
const containerRef = ref(null);

const addString = () => {
  if (inputValue.value.trim() !== "") {
    strings.value.push(inputValue.value.trim());
    inputValue.value = "";
  }
};

const scrollToActive = async () => {
  await nextTick();
  const container = containerRef.value;
  const activeEl = container?.querySelector(".active");
  if (container && activeEl) {
    activeEl.scrollIntoView({
      behavior: "smooth",
      block: "center",
    });
  }
};

const nextQuestion = async () => {
  if (currentIndex.value < strings.value.length - 1) {
    currentIndex.value++;
    scrollToActive();
  }
};

const prevQuestion = async () => {
  if (currentIndex.value > 0) {
    currentIndex.value--;
    scrollToActive();
  }
};

const deleteQuestion = (index) => {
  strings.value.splice(index, 1);
  if (currentIndex.value >= strings.value.length) {
    currentIndex.value = strings.value.length - 1;
  }
  scrollToActive();
};
</script>

<template>
  <div class="container">
    <div
      style="display: flex; flex-direction: column; gap: 15px; position: absolute; left: 20px; top: 50%;"
    >
      <input v-model="inputValue" type="text" placeholder="Écris une question..." />
      <button @click="addString">Valider</button>
    </div>

    <div class="containerQuestions">
      <div class="containerQuestions__overflow" ref="containerRef">
        <div
          v-for="(str, index) in strings"
          :key="index"
          class="questionWrapper"
        >
          <p
            :class="{
              active: index === currentIndex,
              blurred: index !== currentIndex
            }"
          >
            {{ str }}
          </p>

          <button class="deleteButton" @click="deleteQuestion(index)">
            ✕
          </button>
        </div>
      </div>
    </div>

    <div class="navigationButtons">
      <button @click="prevQuestion" :disabled="currentIndex === 0">
        Précédent
      </button>
      <button
        @click="nextQuestion"
        :disabled="currentIndex === strings.length - 1"
      >
        Suivant
      </button>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.questionWrapper {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: transform 0.3s ease;
}

.deleteButton {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) scale(0.7);
  background: rgba(255, 60, 60, 0.9);
  color: white;
  border: none;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  cursor: pointer;
  opacity: 0;
  font-size: 1.2em;
  font-weight: bold;
  line-height: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 6px rgba(255, 0, 0, 0.3);
  transition: all 0.3s ease;
}

.questionWrapper:hover .deleteButton {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1);
}

.deleteButton:hover {
  background: rgba(255, 80, 80, 1);
  box-shadow: 0 0 10px rgba(255, 80, 80, 0.6);
}

p {
  margin: 0;
  font-size: 1.1em;
  transition: all 0.4s ease;
  color: white;
  text-align: center;
  padding: 10px 20px;
  position: relative;
  border-radius: 6px;
  background: rgba(0, 0, 0, 0.192);
}

.active {
  filter: none;
  opacity: 1;
  transform: scale(1);
}
.blurred {
  filter: blur(4px);
  opacity: 0.5;
  transform: scale(0.95);
}

.containerQuestions {
  position: relative;
  padding: 40px 20px;
  border-radius: 8px;
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  max-width: 400px;
  height: 500px;
}

.containerQuestions__overflow {
  display: flex;
  flex-direction: column;
  gap: 25px;
  overflow-y: auto;
  scrollbar-width: none;
  -ms-overflow-style: none;
}
.containerQuestions__overflow::-webkit-scrollbar {
  display: none;
}

.containerQuestions::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 8px;
  padding: 4px;
  background: linear-gradient(45deg, orange, purple);
  -webkit-mask:
    linear-gradient(#fff 0 0) content-box,
    linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  z-index: -1;
}

.navigationButtons {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 15px;
}

input {
  padding: 6px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 6px 12px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: 0.3s;
}

button:hover:not(:disabled) {
  background-color: #369f73;
}

button:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}
</style>
