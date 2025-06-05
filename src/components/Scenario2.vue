<template>
  <div class="scenario-container">
    <h2 class="scenario-title">Szenario 2: ARIA Labels & Alt-Texte</h2>

    <!-- Simulierte Webseite -->
    <div class="website-preview">
      <div class="webpage-frame">
        <p class="scenario-description">
          Stelle sicher, dass alle interaktiven Elemente die benötigten Attribute haben, z.&nbsp;B. durch Alt-Texte oder
          ARIA-Labels.
        </p>
        <!-- DEINE VERÄNDERUNGEN VON HIER -->
        <img src="../assets/cat_caviar.jpg" />

        <button></button>

        <form>
          <input type="text" id="email" placeholder="E-Mail" />
        </form>

        <textarea id="feedback" placeholder="Dein Feedback..."></textarea>

        <select id="topic">
          <option value="">Thema wählen</option>
          <option value="cats">Katzen</option>
          <option value="dogs">Hunde</option>
        </select>

        <div role="button" class="icon-button">
          <svg xmlns="http://www.w3.org/2000/svg" height="24" width="24" viewBox="0 0 24 24">
            <path d="M3 12h18M12 3v18" stroke="black" stroke-width="2" fill="none"/>
          </svg>
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
  let result = 0;

  // img mit alt text
  const img = document.querySelector('img');
  if (img?.alt?.trim().length >= 3) {
    result += 15;
  } else {
    messages.value.push(
      'Das Bild hat keinen sinnvollen alt-Text. Füge ein alt-Attribut hinzu, das den Inhalt des Bildes kurz und prägnant beschreibt, ' +
      'z. B. alt="Katze mit Kaviar auf einem Tisch".'
    );
  }

  // Button mit text oder aria-label
  const buttons = document.querySelectorAll('button');
  let buttonsValid = true;
  buttons.forEach((btn) => {
    const hasText = btn.innerText.trim().length > 0;
    const hasAria = btn.getAttribute('aria-label');
    if (!hasText && !hasAria) {
      messages.value.push(
        'Ein Button hat weder sichtbaren Text noch ein aria-label. Füge entweder einen aussagekräftigen Text in den Button ein (z. B. "Absenden") ' +
        'oder ergänze aria-label="Absenden", damit Screenreader die Funktion des Buttons erkennen können.'
      );
      buttonsValid = false;
    }
  });
  if (buttonsValid) result += 15;

  // 3. input mit label oder aria-label
  const inputs = document.querySelectorAll('input');
  let inputsValid = true;
  inputs.forEach((input) => {
    const id = input.id;
    const label = document.querySelector(`label[for="${id}"]`);
    const aria = input.getAttribute('aria-label');
    if (!label && !aria) {
      messages.value.push(
        `Das Eingabefeld mit Platzhalter "${input.placeholder}" ist nicht korrekt beschriftet. ` +
        `Füge entweder ein <label for="${id}">E-Mail</label> hinzu oder ergänze das Attribut aria-label="E-Mail".`
      );
      inputsValid = false;
    }
  });
  if (inputsValid) result += 15;

  // Textareas mit label or aria-label
  const textareas = document.querySelectorAll('textarea');
  let textareasValid = true;
  textareas.forEach((ta) => {
    const id = ta.id;
    const label = document.querySelector(`label[for="${id}"]`);
    const aria = ta.getAttribute('aria-label');
    if (!label && !aria) {
      messages.value.push(
        'Ein Textbereich fehlt eine zugängliche Beschriftung. ' +
        `Füge entweder ein <label for="${id}">Feedback</label> hinzu oder ergänze aria-label="Dein Feedback".`
      );
      textareasValid = false;
    }
  });
  if (textareasValid && textareas.length > 0) result += 10;

  // 5. Select mit label or aria-label
  const selects = document.querySelectorAll('select');
  let selectsValid = true;
  selects.forEach((sel) => {
    const id = sel.id;
    const label = document.querySelector(`label[for="${id}"]`);
    const aria = sel.getAttribute('aria-label');
    if (!label && !aria) {
      messages.value.push(
        'Ein Dropdown-Menü ist nicht beschriftet. Füge entweder ein <label for="topic">Thema</label> hinzu ' +
        'oder ergänze aria-label="Thema wählen", damit der Zweck klar wird.'
      );
      selectsValid = false;
    }
  });
  if (selectsValid && selects.length > 0) result += 10;

  // 6. Interaktive icons mit Rolle
  const interactiveIcons = document.querySelectorAll('[role="button"], [role="link"]');
  let iconsValid = true;
  interactiveIcons.forEach((icon) => {
    const aria = icon.getAttribute('aria-label');
    const labelledBy = icon.getAttribute('aria-labelledby');
    const hasText = icon.textContent.trim().length > 0;
    if (!aria && !labelledBy && !hasText) {
      messages.value.push(
        'Ein interaktives Symbol mit role="button" oder role="link" hat keine zugängliche Beschriftung. ' +
        'Füge aria-label="Menü öffnen" oder eine sichtbare Textalternative hinzu, damit assistive Technologien die Funktion verstehen.'
      );
      iconsValid = false;
    }
  });
  if (iconsValid && interactiveIcons.length > 0) result += 10;

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

.icon-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border: 1px solid #ccc;
  cursor: pointer;
  border-radius: 4px;
}
</style>
