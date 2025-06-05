<template>
  <div class="scenario-container">
    <h2 class="scenario-title">Szenario 4: Abschließender Test</h2>

    <!-- Simulierte Webseite -->
    <div class="website-preview">
      <div class="webpage-frame">
        <h3>Start</h3>
        <h5>Falscher Sprung</h5>

        <!-- <img src="/logo.png" alt="" /> -->

        <p style="color: lightgray; background: white">
          Dieser Text ist schwer lesbar.
        </p>
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

function luminance(rgb) {
  const a = rgb.map(v => {
    v /= 255;
    return v <= 0.03928 ? v / 12.92 : Math.pow((v + 0.055) / 1.055, 2.4);
  });
  return 0.2126 * a[0] + 0.7152 * a[1] + 0.0722 * a[2];
}

function contrast(rgb1, rgb2) {
  const lum1 = luminance(rgb1);
  const lum2 = luminance(rgb2);
  return (Math.max(lum1, lum2) + 0.05) / (Math.min(lum1, lum2) + 0.05);
}

function checkFix() {
  messages.value = [];
  let scoreSum = 0;

  // Überschriften-Hierarchie prüfen
  const headings = document.querySelectorAll('h1, h2, h3, h4, h5, h6');
  let lastLevel = 0;
  let validOrder = true;
  headings.forEach(h => {
    const level = parseInt(h.tagName[1]);
    if (level - lastLevel > 1) validOrder = false;
    lastLevel = level;
  });

  if (validOrder) {
    scoreSum += 33;
  } else {
    messages.value.push('Die Überschriften sollten eine logische Reihenfolge haben (z. B. h1 > h2 > h3).');
  }

  // Alt-Text prüfen
  const img = document.querySelector('img');
  if (img && img.alt && img.alt.length >= 3) {
    scoreSum += 33;
  } else {
    messages.value.push('Das Bild sollte einen aussagekräftigen alt-Text haben.');
  }

  // Kontrast prüfen
  const el = document.querySelector('p');
  const style = getComputedStyle(el);
  const fg = style.color.match(/\d+/g).map(Number);
  const bg = style.backgroundColor.match(/\d+/g).map(Number);
  const ratio = contrast(fg, bg);

  if (ratio >= 4.5) {
    scoreSum += 34;
  } else {
    messages.value.push('Der Textkontrast ist zu niedrig. Zielwert: mindestens 4.5:1.');
  }

  props.setScore(scoreSum);
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
