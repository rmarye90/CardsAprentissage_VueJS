# Modifiers dans Vue.js [10/40]

## 🎯 Objectif
Comprendre les modificateurs (modifiers) dans Vue.js qui permettent de modifier le comportement des directives et des événements.

## 📝 Description
Les modificateurs sont des suffixes spéciaux indiqués par un point qui permettent de :
- Modifier le comportement des événements (event modifiers)
- Modifier la façon dont v-model fonctionne
- Changer le comportement des liaisons de données
- Ajouter des comportements spéciaux aux touches (key modifiers)

## 💡 Recommandations Vue.js
- Utiliser les modificateurs plutôt que de gérer la logique dans les handlers
- Chaîner les modificateurs dans un ordre logique
- Préférer les modificateurs natifs aux solutions manuelles
- Utiliser les alias de touches quand ils existent

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)
const message = ref('')
const isEditing = ref(false)
const formData = ref({
  name: '',
  age: 0,
  email: ''
})

const logMessage = (msg: string) => {
  console.log(msg)
}

const handleSubmit = () => {
  console.log('Form submitted:', formData.value)
}
</script>

<template>
  <div class="p-6 max-w-md mx-auto space-y-6">
    <!-- Event Modifiers -->
    <section class="space-y-4">
      <h2 class="text-xl font-bold">Event Modifiers</h2>

      <!-- Stop Propagation -->
      <div 
        @click="logMessage('Outer')"
        class="p-4 bg-gray-100"
      >
        Outer
        <button
          @click.stop="logMessage('Inner')"
          class="bg-blue-500 text-white px-4 py-2 rounded"
        >
          Stop Propagation
        </button>
      </div>

      <!-- Prevent Default -->
      <a 
        href="https://vuejs.org"
        @click.prevent="logMessage('Prevented')"
        class="text-blue-500 underline"
      >
        Prevent Default
      </a>

      <!-- Once -->
      <button
        @click.once="count++"
        class="bg-green-500 text-white px-4 py-2 rounded"
      >
        Increment Once: {{ count }}
      </button>
    </section>

    <!-- Key Modifiers -->
    <section class="space-y-4">
      <h2 class="text-xl font-bold">Key Modifiers</h2>

      <input
        v-model="message"
        @keyup.enter="logMessage('Enter pressed')"
        @keyup.escape="message = ''"
        placeholder="Press enter or escape"
        class="w-full border p-2 rounded"
      >

      <!-- Multiple Key Modifiers -->
      <input
        @keyup.ctrl.enter="logMessage('Ctrl+Enter')"
        placeholder="Press Ctrl+Enter"
        class="w-full border p-2 rounded"
      >
    </section>

    <!-- v-model Modifiers -->
    <section class="space-y-4">
      <h2 class="text-xl font-bold">v-model Modifiers</h2>

      <!-- Lazy -->
      <input
        v-model.lazy="formData.name"
        placeholder="Updates on change"
        class="w-full border p-2 rounded"
      >

      <!-- Number -->
      <input
        v-model.number="formData.age"
        type="number"
        class="w-full border p-2 rounded"
      >

      <!-- Trim -->
      <input
        v-model.trim="formData.email"
        placeholder="Trims whitespace"
        class="w-full border p-2 rounded"
      >
    </section>

    <!-- Other Modifiers -->
    <section class="space-y-4">
      <h2 class="text-xl font-bold">Other Modifiers</h2>

      <!-- .capture -->
      <div 
        @click.capture="logMessage('Captured!')"
        class="p-4 bg-gray-100"
      >
        Capture Phase
      </div>

      <!-- .self -->
      <div 
        @click.self="logMessage('Self only!')"
        class="p-4 bg-gray-100"
      >
        Self Only
      </div>

      <!-- .passive -->
      <div 
        @scroll.passive="logMessage('Scrolling')"
        class="h-24 overflow-auto bg-gray-100"
      >
        <p v-for="n in 20" :key="n">Scroll content {{ n }}</p>
      </div>
    </section>

    <!-- Form with Multiple Modifiers -->
    <form 
      @submit.prevent="handleSubmit"
      class="space-y-4"
    >
      <div class="space-y-2">
        <h3>Form Data:</h3>
        <pre class="bg-gray-100 p-2 rounded">{{ formData }}</pre>
      </div>

      <button 
        type="submit"
        class="w-full bg-blue-500 text-white px-4 py-2 rounded"
      >
        Submit Form
      </button>
    </form>
  </div>
</template>
```
## ❓ QCM

1. Quel modificateur empêche la propagation d'événements ?
   - [ ] .prevent
   - [ ] .halt
   - [x] .stop
   - [ ] .block

2. Pour empêcher le comportement par défaut :
   - [x] .prevent
   - [ ] .stop
   - [ ] .default
   - [ ] .block

3. Pour ne déclencher un événement qu'une fois :
   - [ ] .single
   - [ ] .one
   - [ ] .first
   - [x] .once

4. Pour les touches du clavier :
   - [ ] .return
   - [x] .enter
   - [ ] .key-enter
   - [ ] .enterKey

5. Pour v-model, quel modificateur convertit en nombre ?
   - [x] .number
   - [ ] .numeric
   - [ ] .int
   - [ ] .float

6. Pour mettre à jour sur change plutôt que sur input :
   - [ ] .change
   - [ ] .update
   - [ ] .blur
   - [x] .lazy

7. Pour supprimer les espaces :
   - [ ] .clean
   - [x] .trim
   - [ ] .strip
   - [ ] .space

8. Pour un clic droit :
   - [ ] .secondary
   - [ ] .context
   - [x] .right
   - [ ] .alt

9. Pour la phase de capture :
   - [ ] .first
   - [ ] .before
   - [ ] .pre
   - [x] .capture

10. Pour cibler uniquement l'élément lui-même :
    - [x] .self
    - [ ] .only
    - [ ] .target
    - [ ] .current

    <h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. C | 2. A  | 3. D | 4. B | 5. A |
| 6. C | 7. B | 8. C | 9. D | 10. A |
</details>

## 💡 Tips pour les Modifiers

### 1. Chaînage des modificateurs
```ts
<!-- L'ordre est important -->
<input @keyup.ctrl.enter="handler">
<form @submit.stop.prevent="handler">
```

### 2. Modificateurs personnalisés
```ts
// Dans main.ts
app.directive('custom', {
  mounted(el, binding) {
    if (binding.modifiers.special) {
      // Logique spéciale
    }
  }
})
```

### 3. Combinaisons clavier communes
```ts
<!-- Alt + C -->
<input @keyup.alt.c="handler">

<!-- Ctrl + Entrée -->
<input @keyup.ctrl.enter="handler">

<!-- Cmd/Ctrl + S -->
<input @keyup.meta.s="handler">
```

### 4. Optimisation des performances
```ts
<!-- .passive améliore les performances du scroll -->
<div @scroll.passive="handler">

<!-- .once réduit les listeners -->
<button @click.once="handler">
```

### 5. Combinaison avec TypeScript
```vue
<script setup lang="ts">
const handler = (event: KeyboardEvent) => {
  if (event.ctrlKey && event.key === 'Enter') {
    // Alternative aux modificateurs
  }
}
</script>
```

## ✏️ Exercice Pratique

Créez un composant d'éditeur de texte basique avec :
- Raccourcis clavier (Ctrl+B pour gras, Ctrl+I pour italique)
- Empêcher le copier-coller
- Validation à la perte de focus (trim + longueur min/max)
- Double-clic pour éditer
- Action d'enregistrement avec confirmation
- Prévention de la fermeture accidentelle avec modifications non sauvegardées

<details>
<summary>Voir la solution</summary>

```vue
<script setup lang="ts">
import { ref, computed, watch, onBeforeUnmount } from 'vue'

interface EditorState {
  text: string
  isEditing: boolean
  isBold: boolean
  isItalic: boolean
  hasUnsavedChanges: boolean
}

// État initial
const initialState = {
  text: '',
  isEditing: false,
  isBold: false,
  isItalic: false,
  hasUnsavedChanges: false
}

// État de l'éditeur
const editorState = ref<EditorState>({ ...initialState })

// Validation
const validation = computed(() => {
  const text = editorState.value.text.trim()
  return {
    tooShort: text.length < 10,
    tooLong: text.length > 1000,
    isValid: text.length >= 10 && text.length <= 1000
  }
})

// Gestion des raccourcis clavier
const handleKeydown = (event: KeyboardEvent) => {
  if (event.ctrlKey || event.metaKey) {
    switch(event.key.toLowerCase()) {
      case 'b':
        event.preventDefault()
        editorState.value.isBold = !editorState.value.isBold
        break
      case 'i':
        event.preventDefault()
        editorState.value.isItalic = !editorState.value.isItalic
        break
      case 's':
        event.preventDefault()
        if (validation.value.isValid) {
          saveContent()
        }
        break
    }
  }
}

// Empêcher le copier-coller
const preventCopyPaste = (event: ClipboardEvent) => {
  event.preventDefault()
  alert('Copier-coller désactivé dans cet éditeur')
}

// Gestion de la sauvegarde
const saveContent = () => {
  if (!validation.value.isValid) return

  if (confirm('Voulez-vous sauvegarder les modifications ?')) {
    console.log('Contenu sauvegardé:', editorState.value.text)
    editorState.value.hasUnsavedChanges = false
  }
}

// Gestion des modifications non sauvegardées
const handleBeforeUnload = (event: BeforeUnloadEvent) => {
  if (editorState.value.hasUnsavedChanges) {
    event.preventDefault()
    event.returnValue = ''
  }
}

// Observateur pour détecter les changements
watch(
  () => editorState.value.text,
  () => {
    editorState.value.hasUnsavedChanges = true
  }
)

// Nettoyage
onBeforeUnmount(() => {
  window.removeEventListener('beforeunload', handleBeforeUnload)
})

// Initialisation
window.addEventListener('beforeunload', handleBeforeUnload)
</script>

<template>
  <div class="max-w-2xl mx-auto p-6">
    <div class="mb-4 flex justify-between items-center">
      <h2 class="text-xl font-bold">Éditeur de texte</h2>
      
      <div class="space-x-2">
        <button
          @click="editorState.isBold = !editorState.isBold"
          class="px-3 py-1 border rounded"
          :class="{ 'bg-gray-200': editorState.isBold }"
          title="Ctrl+B"
        >
          <strong>B</strong>
        </button>
        
        <button
          @click="editorState.isItalic = !editorState.isItalic"
          class="px-3 py-1 border rounded"
          :class="{ 'bg-gray-200': editorState.isItalic }"
          title="Ctrl+I"
        >
          <em>I</em>
        </button>
      </div>
    </div>

    <div
      class="border rounded p-4 min-h-[200px]"
      :class="{
        'font-bold': editorState.isBold,
        'italic': editorState.isItalic
      }"
    >
      <textarea
        v-model.trim="editorState.text"
        @keydown="handleKeydown"
        @copy.prevent="preventCopyPaste"
        @paste.prevent="preventCopyPaste"
        @cut.prevent="preventCopyPaste"
        @blur="editorState.isEditing = false"
        :class="{
          'w-full h-full outline-none resize-none': true,
          'font-bold': editorState.isBold,
          'italic': editorState.isItalic
        }"
        placeholder="Commencez à écrire... (min 10 caractères)"
      ></textarea>
    </div>

    <!-- Validation -->
    <div class="mt-2 text-sm">
      <p 
        v-if="validation.tooShort" 
        class="text-red-500"
      >
        Le texte doit faire au moins 10 caractères
      </p>
      <p 
        v-if="validation.tooLong"
        class="text-red-500"
      >
        Le texte ne peut pas dépasser 1000 caractères
      </p>
      <p class="text-gray-600">
        {{ editorState.text.length }}/1000 caractères
      </p>
    </div>

    <!-- Actions -->
    <div class="mt-4 flex justify-end space-x-4">
      <span 
        v-if="editorState.hasUnsavedChanges"
        class="text-orange-500"
      >
        Modifications non sauvegardées
      </span>
      
      <button
        @click="saveContent"
        :disabled="!validation.isValid || !editorState.hasUnsavedChanges"
        class="px-4 py-2 bg-blue-500 text-white rounded disabled:opacity-50"
      >
        Sauvegarder (Ctrl+S)
      </button>
    </div>

    <!-- Raccourcis -->
    <div class="mt-4 text-sm text-gray-600">
      <p>Raccourcis clavier :</p>
      <ul class="list-disc list-inside">
        <li>Ctrl+B : Gras</li>
        <li>Ctrl+I : Italique</li>
        <li>Ctrl+S : Sauvegarder</li>
      </ul>
    </div>
  </div>
</template>
```
</details>

## 💡 Tips supplémentaires

1. **Groupement des modificateurs**
```ts
<!-- Meilleure lisibilité -->
<button 
  @click.stop.prevent.once="handler"
  class="..."
>
```

2. **Validation d'événements spéciaux**
```ts
const handleKeyCombo = (event: KeyboardEvent) => {
  // Vérifier la plateforme
  const isMac = /macintosh|mac os x/i.test(navigator.userAgent)
  const modifier = isMac ? event.metaKey : event.ctrlKey
  
  if (modifier && event.key === 's') {
    // ...
  }
}
```

3. **Gestion du contexte**
```vue
<div 
  @contextmenu.prevent="handleContext"
  class="..."
>
  <!-- Menu contextuel personnalisé -->
</div>
```

4. **Écoute des modifications**
```ts
const trackChanges = () => {
  // Détecter les modifications
  window.onbeforeunload = (e) => {
    if (hasUnsavedChanges.value) {
      e.preventDefault()
      return e.returnValue = ''
    }
  }
}
```
