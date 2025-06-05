<template>
  <div class="scenario-container">
    <h2 class="scenario-title">Szenario 1: HTML-Struktur</h2>

    <!-- Simuliertes Webseitendesign -->
    <div class="website-preview">
      <div id="example-root" class="webpage-frame">
        <!-- Schlechte Struktur zum Verbessern -->
        <div>
          <h3>Meine Webseite</h3>
          <p>Willkommen auf meiner Seite</p>

          <div>
            <h5>Neuigkeiten</h5>
            <p>Dies ist die neueste Aktualisierung.</p>
          </div>

          <div>
            <h2>Kontakt</h2>
            <p>Schreiben Sie uns: beispiel@example.com</p>
          </div>
        </div>
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
  let scoreVal = 0;

  const header = document.querySelector('header');
  const main = document.querySelector('main');
  const footer = document.querySelector('footer');

  if (header) scoreVal += 20;
  else messages.value.push('Nutze ein <header>-Element für den oberen Seitenbereich.');

  if (main) scoreVal += 20;
  else messages.value.push('Der Hauptinhalt sollte im <main>-Tag stehen.');

  if (footer) scoreVal += 10;
  else messages.value.push('Verwende ein <footer>-Element für den unteren Seitenbereich.');

  const headings = Array.from(document.querySelectorAll('h1, h2, h3, h4, h5, h6'));
  let lastLevel = 0;
  let validHeadingOrder = true;
  headings.forEach(h => {
    const level = parseInt(h.tagName[1]);
    if (level - lastLevel > 1) validHeadingOrder = false;
    lastLevel = level;
  });

  if (validHeadingOrder && headings.length > 0) {
    scoreVal += 30;
  } else {
    messages.value.push('Achte auf eine logische Reihenfolge der Überschriften (z. B. h1 > h2 > h3).');
  }

  const hasSection = !!document.querySelector('section');
  const hasArticle = !!document.querySelector('article');

  if (hasSection || hasArticle) scoreVal += 20;
  else messages.value.push('Strukturiere Inhalte mit semantischen Tags wie <section> oder <article>.');

  props.setScore(scoreVal);
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
