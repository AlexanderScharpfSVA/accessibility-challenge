<!-- KEINE VERÄNDERUNGEN HIER-->
<template>
  <div class="app-container">
    <header class="header">
      <h1 class="title">Barrierefreiheit Challenge</h1>
    </header>

    <nav class="nav-bar">
      <div class="scenario-buttons">
        <button
          v-for="(scenario, i) in scenarios"
          :key="i"
          @click="current = i"
          :class="['nav-button', { active: current === i }]"
        >
          {{ scenario.title }}
          <span class="nav-score">
            <template v-if="scenarioScores[i] === 100">✅</template>
            <template v-else-if="scenarioScores[i] !== null">{{ scenarioScores[i] }}%</template>
            <template v-else>❌</template>
          </span>
        </button>
      </div>
    </nav>

    <main class="main-content">
       <div
        v-if="allCompleted"
        class="completion-message"
      >
        🎉 Glückwunsch! Du hast alle Szenarien erfolgreich abgeschlossen! 🎉
      </div>
      <component
        :is="currentScenario.component"
        :score="scenarioScores[current]"
        :setScore="(value) => scenarioScores[current] = value"
      />
    </main>

    <footer class="footer">
      Barrierefreiheit Challenge
    </footer>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import Scenario1 from './Scenario1.vue';
import Scenario2 from './Scenario2.vue';
import Scenario3 from './Scenario3.vue';
import Scenario4 from './Scenario4.vue';

const scenarios = [
  { component: Scenario1, title: "Szenario 1" },
  { component: Scenario2, title: "Szenario 2" },
  { component: Scenario3, title: "Szenario 3" },
  { component: Scenario4, title: "Szenario 4" }
];

const current = ref(0);
const currentScenario = computed(() => scenarios[current.value]);
const scenarioScores = ref([null, null, null, null]);

const allCompleted = computed(() =>
  scenarioScores.value.every(score => score === 100)
);
</script>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100vw;
  margin: 0;
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  color: #333;
  overflow: hidden;
}

.header {
  background-color: #0081c4;
  padding: 16px;
  color: white;
  text-align: center;
}

.title {
  margin: 0;
  font-size: 1.75em;
}

.nav-bar {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 12px;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.scenario-buttons {
  display: flex;
  gap: 8px;
}

.nav-button {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-width: 100px;
  padding: 8px 12px;
  background-color: #e0e0e0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s ease;
  font-weight: normal;
}

.nav-button:hover {
  background-color: #d5d5d5;
}

.nav-button.active {
  background-color: #0081c4;
  color: white;
  font-weight: bold;
}

.nav-score {
  font-size: 0.8em;
  margin-top: 4px;
  color: #333;
}

.nav-button.active .nav-score {
  color: #ffffff;
}

.main-content {
  flex: 1;
  overflow-y: auto;
  padding: 24px;
  box-sizing: border-box;
  position: relative;
}

.completion-message {
  margin-top: 24px;
  padding: 16px;
  background-color: #e8f9e8;
  border: 2px solid #45b645;
  color: #2d7a2d;
  font-size: 1.2em;
  text-align: center;
  border-radius: 6px;
}

.footer {
  text-align: center;
  padding: 12px;
  background-color: #fff;
  color: #666;
  font-size: 0.85em;
  border-top: 1px solid #ddd;
}
</style>
