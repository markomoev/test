<script setup lang="ts">
import './App.css';
import { ref, onMounted } from 'vue';

const questions = ref([]);
const selectedQuest = ref<null | { 
  question: string; 
  answer: number; 
  options: string[]; 
  selected: number | null 
}>(null);
const feedback = ref('');

async function fetchQuestions() {
  try {
    const response = await fetch('/QA.json');
    if (!response.ok) {
      throw new Error('Failed to fetch data');
    }
    const data = await response.json();
    questions.value = data;
    console.log('Fetched questions:', questions.value); // Debugging
  } catch (error) {
    console.error('Error fetching questions:', error);
  }
}

function randomQuest() {
  console.log('Random quest triggered');
  const randomIndex = Math.floor(Math.random() * questions.value.length);
  selectedQuest.value = questions.value[randomIndex];
  console.log('Selected question:', selectedQuest.value); // Debugging
}

function getButtonColor(index: number): string {
  const colors = ['#e57373', '#64b5f6', '#81c784', '#ffd54f'];
  return colors[index % colors.length];
}

function isCorrect() {
  if (selectedQuest.value && selectedQuest.value.selected === selectedQuest.value.answer) {
    feedback.value = 'Correct! 🎉';
    setTimeout(() => {
      randomQuest();
      feedback.value = '';
    }, 500);
  } else {
    feedback.value = 'Incorrect. ❌';
  }
}

onMounted(() => {
  fetchQuestions();
  console.log('Component mounted, fetching questions...');
});
</script>

<template>
  <button @click="randomQuest">Start the quiz!</button>

  <div v-if="selectedQuest">
    <p class="question">{{ selectedQuest.question }}</p>
    <ul class="answers">
      <li v-for="(option, index) in selectedQuest.options" :key="index" class="answer">
        <button 
          @click="selectedQuest.selected = index"
          :style="{
            backgroundColor: selectedQuest.selected === index ? '#ffffff' : getButtonColor(index),
            color: selectedQuest.selected === index ? '#000000' : '#ffffff',
            border: selectedQuest.selected === index ? '2px solid #388e3c' : '2px solid transparent'}">
          {{ option }}
        </button>
      </li>
    </ul>
    <button @click="isCorrect">Check the answer!</button>
    <p v-if="feedback" class="feedback">{{ feedback }}</p>
  </div>
</template>