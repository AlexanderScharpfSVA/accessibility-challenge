<template>
  <div class="scenario-container">
    <h2 class="scenario-title">Szenario 2: ARIA Labels & Alt-Texte</h2>

    <!-- Simulierte Webseite -->
    <div class="website-preview">
      <div class="webpage-frame">
        <!-- <img src="/logo.png" alt="" /> -->

        <button>Absenden</button>

        <form>
          <input type="text" id="email" placeholder="E-Mail" />
        </form>
      </div>
    </div>

    <button @click="checkFix" class="check-button">Ergebnisse prüfen</button>
    <p v-if="score !== null" class="score-text">Dein Score: {{ score }}%</p>
    <ul v-if="messages.length" class="feedback-list">
      <li v-for="(msg, i) in messages" :key="i">{{ msg }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
  score: Number,
  setScore: Function
});

const messages = ref([]);

function checkFix() {
  messages.value = [];
  let result = 0;

  // Alt-Text überprüfen
  const img = document.querySelector('img');
  const altValid = img?.alt && img.alt.trim().length >= 3;

  if (altValid) {
    result += 30;
  } else {
    messages.value.push('Füge einen beschreibenden alt-Text zum Bild hinzu.');
  }

  // Aria-Label oder sichtbarer Text auf Button
  const button = document.querySelector('button');
  const hasAria = button?.getAttribute('aria-label');
  const hasText = button?.innerText.trim().length > 0;
  if (hasAria || hasText) {
    result += 30;
  } else {
    messages.value.push('Füge dem Button entweder sichtbaren Text oder ein aria-label hinzu.');
  }

  // Input mit Label
  const input = document.querySelector('input');
  const label = document.querySelector(`label[for="${input?.id}"]`);
  const ariaLabel = input?.getAttribute('aria-label');
  const accessibleInput = label || ariaLabel;

  if (accessibleInput) {
    result += 40;
  } else {
    messages.value.push('Verknüpfe das Eingabefeld mit einem <label> oder verwende aria-label.');
  }

  props.setScore(result);
}
</script>

<style scoped>
.scenario-container {
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.scenario-title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #0081c4;
  margin-bottom: 20px;
}

.website-preview {
  display: flex;
  justify-content: center;
  width: 100%;
  margin-bottom: 16px;
}

.webpage-frame {
  width: 800px;
  padding: 24px;
  background-color: white;
  border: 1px solid #ccc;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.check-button {
  margin-top: 8px;
  padding: 8px 16px;
  background-color: #0081c4;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.check-button:hover {
  background-color: #006ba2;
}

.score-text {
  margin-top: 12px;
  font-weight: bold;
}

.feedback-list {
  margin-top: 10px;
  padding-left: 20px;
  color: #333;
  max-width: 800px;
}
</style>
