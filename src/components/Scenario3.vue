<template>
  <div class="scenario-container">
    <h2 class="scenario-title">Szenario 3: Farbkontrast & Lesbarkeit</h2>

    <!-- Simulierte Webseite -->
    <div class="website-preview">
      <div class="webpage-frame">
        <p class="scenario-description">
          Sorge für ausreichend Farbkontrast, vermeide kleine Schriftgrößen und stelle sicher, dass Informationen nicht nur über Farben vermittelt werden.
        </p>
        <!-- DEINE VERÄNDERUNGEN VON HIER -->
        <p id="contrast-text" style="color: lightgray; background: white">
          Dieser Text hat zu wenig Kontrast.
        </p>

        <p id="color-only-text" style="color: red">Fehler</p>

        <p id="small-text" style="font-size: 10px">Sehr kleiner Text</p>
        <!-- BIS HIER -->
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
  let score = 0;

  // Farbkontrast
  const el = document.getElementById('contrast-text');
  const style = getComputedStyle(el);
  const fg = style.color.match(/\d+/g).map(Number);
  const bg = style.backgroundColor.match(/\d+/g).map(Number);
  const ratio = contrast(fg, bg);

  if (ratio < 4.5) {
    messages.value.push(
      `Der Text "Dieser Text hat zu wenig Kontrast" erfüllt nicht das Mindestkontrastverhältnis von 4.5:1 (aktuell ${ratio.toFixed(2)}:1). ` +
      `Verwende z. B. eine dunklere Schriftfarbe wie #333 oder einen dunkleren Hintergrund, um die Lesbarkeit zu verbessern.`
    );
  } else {
    score += 40;
  }

  // Nur Farbe zur Bedeutung
  const colorOnly = document.getElementById('color-only-text');
  if (colorOnly?.innerText.trim().toLowerCase() === 'fehler') {
    messages.value.push(
      `Die Meldung "Fehler" nutzt nur die Farbe Rot, um die Bedeutung zu vermitteln. ` +
      `Füge ergänzend ein erklärendes Icon oder Text wie "Fehler: Ungültige Eingabe" hinzu, damit die Information auch bei Farbsehschwäche verständlich ist.`
    );
  } else {
    score += 30;
  }

  // Kleine Schriftgröße
  const smallText = document.getElementById('small-text');
  const size = parseFloat(getComputedStyle(smallText).fontSize);
  if (size < 12) {
    messages.value.push(
      `Der Text "Sehr kleiner Text" verwendet eine zu kleine Schriftgröße (${size}px). ` +
      `Verwende mindestens 12–14px oder besser skalierbare Einheiten wie \`rem\`, um die Lesbarkeit auf allen Geräten sicherzustellen.`
    );
  } else {
    score += 30;
  }

  props.setScore(score);
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
