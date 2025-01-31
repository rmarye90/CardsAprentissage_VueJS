# v-model et Forms dans Vue.js [9/40]

## 🎯 Objectif
Comprendre la directive v-model et la gestion des formulaires dans Vue.js, permettant la liaison bidirectionnelle des données avec les éléments de formulaire.

## 📝 Description
v-model crée automatiquement une liaison bidirectionnelle entre :
- Les champs de formulaire et les données du composant
- Combine la liaison de valeur (:value) et l'écoute d'événement (@input)
- Fonctionne avec différents types d'inputs (text, checkbox, radio, select...)

## 💡 Recommandations Vue.js
- Utiliser v-model pour les formulaires plutôt que :value + @input
- Préférer les computed properties pour les transformations complexes
- Utiliser les modificateurs appropriés (.lazy, .number, .trim)
- Typer correctement les formulaires avec TypeScript
- Éviter de mélanger v-model et :value sur le même élément

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface FormData {
  username: string
  email: string
  age: number
  interests: string[]
  newsletter: boolean
  preferredContact: 'email' | 'phone'
  bio: string
}

// État initial du formulaire
const formData = ref<FormData>({
  username: '',
  email: '',
  age: 18,
  interests: [],
  newsletter: false,
  preferredContact: 'email',
  bio: ''
})

// Validation computed
const validation = computed(() => {
  return {
    username: formData.value.username.length >= 3,
    email: /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(formData.value.email),
    age: formData.value.age >= 18 && formData.value.age <= 120,
    bio: formData.value.bio.length <= 500
  }
})

const isFormValid = computed(() => 
  Object.values(validation.value).every(Boolean)
)

// Soumission du formulaire
const handleSubmit = () => {
  if (!isFormValid.value) return
  
  console.log('Données du formulaire:', formData.value)
  // Envoi à l'API...
}

// Réinitialisation
const resetForm = () => {
  formData.value = {
    username: '',
    email: '',
    age: 18,
    interests: [],
    newsletter: false,
    preferredContact: 'email',
    bio: ''
  }
}
</script>

<template>
  <form 
    @submit.prevent="handleSubmit" 
    class="max-w-2xl mx-auto p-6 space-y-6"
  >
    <!-- Input texte simple -->
    <div class="space-y-2">
      <label class="block">
        Nom d'utilisateur:
        <input
          v-model.trim="formData.username"
          type="text"
          class="mt-1 block w-full rounded border p-2"
          :class="{ 'border-red-500': !validation.username }"
        >
      </label>
      <p 
        v-show="!validation.username" 
        class="text-red-500 text-sm"
      >
        Le nom doit faire au moins 3 caractères
      </p>
    </div>

    <!-- Email -->
    <div class="space-y-2">
      <label class="block">
        Email:
        <input
          v-model="formData.email"
          type="email"
          class="mt-1 block w-full rounded border p-2"
          :class="{ 'border-red-500': !validation.email }"
        >
      </label>
      <p 
        v-show="!validation.email" 
        class="text-red-500 text-sm"
      >
        Email invalide
      </p>
    </div>

    <!-- Input numérique -->
    <div class="space-y-2">
      <label class="block">
        Âge:
        <input
          v-model.number="formData.age"
          type="number"
          class="mt-1 block w-full rounded border p-2"
          :class="{ 'border-red-500': !validation.age }"
        >
      </label>
      <p 
        v-show="!validation.age" 
        class="text-red-500 text-sm"
      >
        L'âge doit être entre 18 et 120 ans
      </p>
    </div>

    <!-- Checkboxes multiples -->
    <div class="space-y-2">
      <label class="block">Centres d'intérêt:</label>
      <div class="space-y-1">
        <label class="flex items-center">
          <input
            type="checkbox"
            v-model="formData.interests"
            value="sports"
            class="mr-2"
          >
          Sports
        </label>
        <label class="flex items-center">
          <input
            type="checkbox"
            v-model="formData.interests"
            value="music"
            class="mr-2"
          >
          Musique
        </label>
        <label class="flex items-center">
          <input
            type="checkbox"
            v-model="formData.interests"
            value="tech"
            class="mr-2"
          >
          Technologie
        </label>
      </div>
    </div>

    <!-- Radio buttons -->
    <div class="space-y-2">
      <label class="block">Contact préféré:</label>
      <div class="space-y-1">
        <label class="flex items-center">
          <input
            type="radio"
            v-model="formData.preferredContact"
            value="email"
            class="mr-2"
          >
          Email
        </label>
        <label class="flex items-center">
          <input
            type="radio"
            v-model="formData.preferredContact"
            value="phone"
            class="mr-2"
          >
          Téléphone
        </label>
      </div>
    </div>

    <!-- Checkbox unique -->
    <div>
      <label class="flex items-center">
        <input
          type="checkbox"
          v-model="formData.newsletter"
          class="mr-2"
        >
        S'inscrire à la newsletter
      </label>
    </div>

    <!-- Textarea -->
    <div class="space-y-2">
      <label class="block">
        Bio:
        <textarea
          v-model.trim="formData.bio"
          class="mt-1 block w-full rounded border p-2"
          rows="4"
          :class="{ 'border-red-500': !validation.bio }"
        ></textarea>
      </label>
      <p class="text-sm text-gray-600">
        {{ formData.bio.length }}/500 caractères
      </p>
      <p 
        v-show="!validation.bio" 
        class="text-red-500 text-sm"
      >
        La bio ne peut pas dépasser 500 caractères
      </p>
    </div>

    <!-- Boutons -->
    <div class="flex justify-end space-x-4">
      <button
        type="button"
        @click="resetForm"
        class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded"
      >
        Réinitialiser
      </button>
      
      <button
        type="submit"
        :disabled="!isFormValid"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 disabled:opacity-50"
      >
        Envoyer
      </button>
    </div>
  </form>
</template>
```

## 🔍 QCM

1. v-model combine :
   - [ ] :model et @change
   - [ ] :bind et @update
   - [ ] :value et @input
   - [ ] :data et @sync

2. Pour un champ numérique, on utilise :
   - [ ] v-model.number
   - [ ] v-model.numeric
   - [ ] v-model.int
   - [ ] v-model.float

3. Pour éviter les espaces inutiles :
   - [ ] v-model.clean
   - [ ] v-model.strip
   - [ ] v-model.space
   - [ ] v-model.trim

4. Pour une mise à jour différée :
   - [ ] v-model.delayed
   - [ ] v-model.lazy
   - [ ] v-model.async
   - [ ] v-model.defer

5. Pour les checkboxes multiples :
   - [ ] Un tableau dans v-model
   - [ ] Une chaîne dans v-model
   - [ ] Un objet dans v-model
   - [ ] Un nombre dans v-model

6. Pour un select multiple :
   - [ ] v-model retourne une chaîne
   - [ ] v-model retourne un objet
   - [ ] v-model retourne un tableau
   - [ ] v-model retourne un boolean

7. Pour un composant personnalisé :
   - [ ] modelValue comme prop
   - [ ] value comme prop
   - [ ] model comme prop
   - [ ] input comme prop

8. Les modificateurs disponibles sont :
   - [ ] .lazy
   - [ ] .sync
   - [ ] .number
   - [ ] .trim

9. Pour une validation en temps réel :
   - [ ] Methods
   - [ ] Watch
   - [ ] v-model.validate
   - [ ] Computed properties

10. Dans un formulaire, prevent default est géré par :
    - [ ] @submit.prevent
    - [ ] v-model.prevent
    - [ ] @form.prevent
    - [ ] v-prevent

    <h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. C | 2. A  | 3. D | 4. B | 5. A |
| 6. C | 7. A | 8. A, C, D | 9. D | 10. A |
</details>

## 💡 Tips pour v-model et Forms

### 1. Validation en temps réel
```ts
const validation = computed(() => ({
  email: /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value),
  password: password.value.length >= 8
}))
```

### 2. Reset de formulaire
```ts
const initialState = {
  email: '',
  password: ''
}

const formData = ref({ ...initialState })
const resetForm = () => {
  formData.value = { ...initialState }
}
```

### 3. v-model personnalisé sur composant
```vue
<script setup lang="ts">
defineProps<{
  modelValue: string
}>()

defineEmits<{
  (e: 'update:modelValue', value: string): void
}>()
</script>
```

### 4. Gestion des fichiers
```vue
<template>
  <input 
    type="file" 
    @change="handleFileUpload"
    accept=".jpg,.png"
  >
</template>

<script setup lang="ts">
const handleFileUpload = (event: Event) => {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (file) {
    // Traitement du fichier
  }
}
</script>
```

### 5. Débounce sur input
```ts
import { debounce } from 'lodash-es'

const debouncedUpdate = debounce((value) => {
  // Update logic here
}, 300)

const handleInput = (event: Event) => {
  const value = (event.target as HTMLInputElement).value
  debouncedUpdate(value)
}
```
