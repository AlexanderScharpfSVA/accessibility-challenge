<template>
  <div class="scenario-container">
    <h2 class="scenario-title">Szenario 1: HTML-Struktur</h2>

    <!-- Simulierte Webseite-->
    <div class="website-preview">
      <div id="example-root" class="webpage-frame">
        <p class="scenario-description">
          Verbessere die semantische HTML-Struktur dieser Webseite, indem du passende Elemente wie
          &ltheader&gt, &ltmain&gt, &ltsection&gt, &ltnav&gt oder &ltfooter&gt verwendest. Achte außerdem auf die
          korrekte Verwendung von Überschriften und Landmarken.
          Die rot umrandeten Bereiche markieren Stellen, an denen semantische HTML-Elemente fehlen oder verbessert
          werden sollten.
        </p>
        <!-- DEINE VERÄNDERUNGEN VON HIER -->
        <div class="non-semantic">
          <h3>Meine Webseite</h3>
          <p>Willkommen auf meiner Seite</p>

          <div class="non-semantic">
            <strong style="display: block; margin-bottom: 4px;">(Navigation?)</strong>
            <ul>
              <li>Start</li>
              <li>Über uns</li>
              <li>Kontakt</li>
            </ul>
          </div>

          <div class="non-semantic">
            <h5>Neuigkeiten</h5>
            <p>Dies ist die neueste Aktualisierung.</p>
          </div>

          <div class="non-semantic">
            <h2>Kontakt</h2>
            <p>Schreiben Sie uns: beispiel@example.com</p>
          </div>
        </div>
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

function checkFix() {
  messages.value = [];
  let scoreVal = 0;

  const header = document.querySelector('header');
  const main = document.querySelector('main');
  const footer = document.querySelector('footer');
  const nav = document.querySelector('nav');

  if (header) {
    scoreVal += 10;
  } else {
    messages.value.push(
      'Der obere Seitenbereich sollte mit einem <header>-Element ausgezeichnet werden, um den Anfang der Seite semantisch zu kennzeichnen. ' +
      'Beispiel: <header><h1>Meine Webseite</h1></header>'
    );
  }

  if (main) {
    scoreVal += 10;
  } else {
    messages.value.push(
      'Der Hauptinhalt der Seite gehört in ein <main>-Element, um Screenreadern und Browsern die Struktur klar zu machen. ' +
      'Beispiel: <main><section>...</section></main>'
    );
  }

  if (footer) {
    scoreVal += 10;
  } else {
    messages.value.push(
      'Am Seitenende fehlt ein <footer>-Element. Es sollte genutzt werden, um Kontaktinformationen, Copyright oder zusätzliche Navigation zu platzieren.'
    );
  }

  if (nav) {
    scoreVal += 10;
  } else {
    messages.value.push(
      'Die Navigationslinks sollten in einem <nav>-Element gruppiert werden, damit assistive Technologien sie als Navigation erkennen. ' +
      'Beispiel: <nav><ul><li>Start</li>...</ul></nav>'
    );
  }

  // Überschriften-Hierarchie
  const headings = Array.from(document.querySelectorAll('h1, h2, h3, h4, h5, h6'));
  let lastLevel = 0;
  let validHeadingOrder = true;
  headings.forEach(h => {
    const level = parseInt(h.tagName[1]);
    if (lastLevel && level - lastLevel > 1) validHeadingOrder = false;
    lastLevel = level;
  });

  if (validHeadingOrder && headings.length > 0) {
    scoreVal += 20;
  } else {
    messages.value.push(
      'Die Überschriften sollten in sinnvoller Reihenfolge verwendet werden (z. B. h1 > h2 > h3). ' +
      'Vermeide Sprünge wie h3 direkt vor h5, da das die Dokumentstruktur für Screenreader unklar macht.'
    );
  }

  // h1 Check
  const h1s = document.querySelectorAll('h1');
  if (h1s.length >= 1) {
    scoreVal += 10;
  } else {
    messages.value.push(
      'Ein <h1>-Element für den Haupttitel der Seite fehlt. Es dient als wichtigste Überschrift und sollte nur einmal vorkommen.'
    );
  }


  // Section oder Article
  const hasSection = !!document.querySelector('section');
  const hasArticle = !!document.querySelector('article');
  if (hasSection || hasArticle) {
    scoreVal += 10;
  } else {
    messages.value.push(
      'Zur Strukturierung des Inhalts fehlen semantische Bereiche wie <section> oder <article>. ' +
      'Verwende sie, um logisch zusammengehörende Inhalte abzugrenzen.'
    );
  }

  // Übermäßige <div>-Verwendung
  const nonSemanticDivs = document.querySelectorAll('#example-root > div');
  if (nonSemanticDivs.length > 0) {
    messages.value.push(
      'Es wurden generische <div>-Container für Inhaltsbereiche verwendet. Ersetze sie durch semantische Elemente wie <header>, <section>, <nav> oder <footer>, ' +
      'um die Struktur der Seite verständlicher zu machen.'
    );
  } else {
    scoreVal += 20;
  }

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

.non-semantic {
  outline: 2px dashed red;
  background-color: #fdf3f3;
  padding: 8px;
  margin-bottom: 12px;
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