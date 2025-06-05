<template>
  <div class="scenario-container">
    <h2 class="scenario-title">Szenario 4: Abschließender Test</h2>

    <!-- Simulierte Webseite -->
    <div class="website-preview">
      <div class="webpage-frame">
        <p class="scenario-description">
          In diesem Test kombinierst du alle gelernten Aspekte: Struktur, Alternativtexte, ARIA und Kontrast.
        </p>
        <!-- DEINE VERÄNDERUNGEN VON HIER -->
        <h3>Start</h3>
        <h5>Falscher Sprung</h5>

        <img src="../assets/cat_caviar.jpg" />

        <p id="contrast-text" style="color: lightgray; background: white">
          Dieser Text ist schwer lesbar.
        </p>

        <button id="action-button"></button>

        <input type="text" id="email" placeholder="E-Mail" />
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
  let score = 100;

  // Überschriftenhierarchie prüfen
  const headings = document.querySelectorAll('h1, h2, h3, h4, h5, h6');
  let lastLevel = 0;
  let validOrder = true;
  headings.forEach(h => {
    const level = parseInt(h.tagName[1]);
    if (lastLevel !== 0 && level - lastLevel > 1) validOrder = false;
    lastLevel = level;
  });

  if (!validOrder) {
    score -= 20;
    messages.value.push(
      `Die Überschriften folgen keiner logischen Reihenfolge (z. B. h3 direkt vor h5). ` +
      `Strukturiere deine Inhalte hierarchisch mit aufsteigenden Ebenen wie h2 > h3 > h4.`
    );
  }

  // Alt-Text prüfen
  const img = document.querySelector('img');
  if (!img || !img.alt || img.alt.trim().length < 3) {
    score -= 20;
    messages.value.push(
      `Das Bild hat keinen aussagekräftigen alt-Text. ` +
      `Füge einen beschreibenden alt-Text hinzu, der den Inhalt des Bildes für Screenreader vermittelt.`
    );
  }

  // Kontrast prüfen
  const el = document.getElementById('contrast-text');
  const style = getComputedStyle(el);
  const fg = style.color.match(/\d+/g).map(Number);
  const bg = style.backgroundColor.match(/\d+/g).map(Number);
  const ratio = contrast(fg, bg);

  if (ratio < 4.5) {
    score -= 20;
    messages.value.push(
      `Der Text "Dieser Text ist schwer lesbar" hat zu wenig Kontrast (aktuell ${ratio.toFixed(2)}:1). ` +
      `Verwende z. B. eine dunklere Schriftfarbe (#333) oder passe den Hintergrund an, um die Lesbarkeit zu verbessern.`
    );
  }

  // Button mit Text oder aria-label prüfen
  const button = document.getElementById('action-button');
  const hasBtnText = button?.innerText.trim().length > 0;
  const hasBtnAria = button?.getAttribute('aria-label');
  if (!hasBtnText && !hasBtnAria) {
    score -= 20;
    messages.value.push(
      `Der Button hat weder sichtbaren Text noch ein aria-label. ` +
      `Füge entweder beschriftenden Text im Button ein oder verwende das Attribut aria-label, um die Funktion zugänglich zu machen.`
    );
  }

  // Input mit Label oder aria-label prüfen
  const input = document.querySelector('input');
  const inputId = input?.id;
  const label = inputId ? document.querySelector(`label[for="${inputId}"]`) : null;
  const inputAria = input?.getAttribute('aria-label');
  if (!label && !inputAria) {
    score -= 20;
    messages.value.push(
      `Das Eingabefeld für die E-Mail ist nicht beschriftet. ` +
      `Verknüpfe es entweder mit einem <label for="email"> oder verwende aria-label="E-Mail".`
    );
  }

  props.setScore(Math.max(score, 0));
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
