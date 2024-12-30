# Props & Emits [4/40]

## 🎯 Objectif
Comprendre la communication entre composants parents et enfants à travers les props (données descendantes) et les événements émis (données ascendantes).

## 📝 Description
- Les **Props** sont des attributs personnalisés que vous pouvez enregistrer sur un composant
- Les **Emits** sont des événements personnalisés émis par un composant enfant vers son parent
- Ensemble, ils forment un système de communication bidirectionnelle entre composants

## 💡 Recommandations Vue.js
- Toujours définir les types des props en TypeScript
- Utiliser les validateurs de props quand nécessaire
- Nommer les événements émis en kebab-case
- Ne jamais modifier directement une prop dans l'enfant
- Documenter les props et les events attendus

## 📌 Exemple

```vue
<!-- ChildComponent.vue -->
<script setup lang="ts">
// Définition des props avec TypeScript
interface Props {
  title: string
  count?: number
  user: {
    name: string
    age: number
  }
}

// Définition des props et des emits
const props = defineProps<Props>()
const emit = defineEmits<{
  (e: 'update', value: number): void
  (e: 'delete'): void
}>()

const incrementAndUpdate = () => {
  const newCount = (props.count || 0) + 1
  emit('update', newCount)
}
</script>

<template>
  <div class="p-4 border rounded">
    <h2 class="text-xl font-bold">{{ title }}</h2>
    <p>User: {{ user.name }} ({{ user.age }})</p>
    <p>Count: {{ count }}</p>
    
    <div class="mt-4 space-x-2">
      <button 
        @click="incrementAndUpdate"
        class="bg-blue-500 text-white px-4 py-2 rounded"
      >
        Increment
      </button>
      
      <button 
        @click="emit('delete')"
        class="bg-red-500 text-white px-4 py-2 rounded"
      >
        Delete
      </button>
    </div>
  </div>
</template>

<!-- ParentComponent.vue -->
<script setup lang="ts">
import { ref } from 'vue'
import ChildComponent from './ChildComponent.vue'

const count = ref(0)
const user = ref({
  name: 'John',
  age: 25
})

const handleUpdate = (newValue: number) => {
  count.value = newValue
}

const handleDelete = () => {
  console.log('Delete requested')
}
</script>

<template>
  <ChildComponent
    title="Demo Component"
    :count="count"
    :user="user"
    @update="handleUpdate"
    @delete="handleDelete"
  />
</template>
```

## ❓ QCM

1. Les props sont :
   - [ ] Des données passées du parent à l'enfant
   - [ ] Des données passées de l'enfant au parent
   - [ ] Des données locales
   - [ ] Des événements

2. Comment déclarer des props en TypeScript ?
   - [ ] defineProps<Props>()
   - [ ] props: Props
   - [ ] @Props()
   - [ ] new Props()

3. Comment émettre un événement ?
   - [ ] emit('eventName')
   - [ ] $emit('eventName')
   - [ ] this.emit('eventName')
   - [ ] emits.eventName()

4. Les props sont-elles mutables dans l'enfant ?
   - [ ] Oui
   - [ ] Non
   - [ ] Seulement si elles sont optionnelles
   - [ ] Seulement avec v-model

5. Comment écouter un événement personnalisé ?
   - [ ] @event-name="handler"
   - [ ] v-on:event-name="handler"
   - [ ] :event-name="handler"
   - [ ] #event-name="handler"

6. Les props peuvent être :
   - [ ] Des objets
   - [ ] Des primitives
   - [ ] Des fonctions
   - [ ] Des tableaux

7. Pour une prop optionnelle :
   - [ ] On ajoute ? dans le type
   - [ ] On utilise le mot-clé optional
   - [ ] On met undefined comme type
   - [ ] On ne peut pas

8. Les emits doivent être :
   - [ ] Déclarés avant utilisation
   - [ ] Toujours synchrones
   - [ ] Toujours asynchrones
   - [ ] Mutables

9. Pour un v-model personnalisé :
   - [ ] On utilise modelValue comme prop
   - [ ] On utilise value comme prop
   - [ ] On utilise model comme prop
   - [ ] On utilise data comme prop

10. La validation des props :
    - [ ] Peut être faite avec des types TypeScript
    - [ ] Peut inclure des validateurs personnalisés
    - [ ] Peut définir des valeurs par défaut
    - [ ] Est recommandée en production

<h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. A | 2. A  | 3. A | 4. B | 5. A |
| 6. A,B,C,D | 7. A | 8. A | 9. A | 10. A,B,C,D |
</details>

## ✏️ Exercice Pratique

Créez un composant de formulaire de commentaire qui :
- Accepte un auteur et un texte comme props
- Émet les événements de sauvegarde et d'annulation
- Valide que le texte fait au moins 5 caractères
- Fournit une prévisualisation
- Utilise TypeScript pour les types

Structure de base :

```vue
<script setup lang="ts">
interface CommentFormProps {
  // À vous de définir les props
}

// À vous de jouer !
</script>

<template>
  <div>
    <!-- Votre code ici -->
  </div>
</template>
```

## 🔍 Solution

```vue
<!-- CommentForm.vue -->
<script setup lang="ts">
interface CommentFormProps {
  author: string
  initialText?: string
  minLength?: number
}

interface Comment {
  author: string
  text: string
  createdAt: Date
}

const props = defineProps<CommentFormProps>()
const emit = defineEmits<{
  (e: 'save', comment: Comment): void
  (e: 'cancel'): void
  (e: 'error', message: string): void
}>()

const text = ref(props.initialText || '')
const minLength = computed(() => props.minLength || 5)

const isValid = computed(() => {
  return text.value.length >= minLength.value
})

const errorMessage = computed(() => {
  if (!text.value) return 'Le commentaire ne peut pas être vide'
  if (!isValid.value) return `Le commentaire doit faire au moins ${minLength.value} caractères`
  return ''
})

const handleSubmit = () => {
  if (!isValid.value) {
    emit('error', errorMessage.value)
    return
  }

  const comment: Comment = {
    author: props.author,
    text: text.value,
    createdAt: new Date()
  }

  emit('save', comment)
  text.value = ''
}
</script>

<template>
  <form 
    @submit.prevent="handleSubmit"
    class="max-w-md mx-auto p-4 border rounded shadow"
  >
    <div class="mb-4">
      <label class="block text-gray-700 mb-2">
        Commenter en tant que: {{ author }}
      </label>
      
      <textarea
        v-model="text"
        rows="4"
        class="w-full p-2 border rounded focus:ring-2 focus:ring-blue-500"
        :class="{
          'border-red-500': errorMessage && text.length > 0,
          'border-green-500': isValid && text.length > 0
        }"
        placeholder="Votre commentaire..."
      ></textarea>
      
      <p 
        v-if="errorMessage && text.length > 0" 
        class="mt-1 text-sm text-red-500"
      >
        {{ errorMessage }}
      </p>
    </div>

    <div class="mb-4">
      <h3 class="font-bold mb-2">Prévisualisation:</h3>
      <div class="p-2 bg-gray-100 rounded">
        <p class="text-sm italic">{{ text || 'Aucun texte saisi' }}</p>
      </div>
    </div>

    <div class="flex justify-end space-x-2">
      <button
        type="button"
        @click="emit('cancel')"
        class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded"
      >
        Annuler
      </button>
      
      <button
        type="submit"
        :disabled="!isValid"
        class="px-4 py-2 bg-blue-500 text-white rounded disabled:opacity-50"
      >
        Envoyer
      </button>
    </div>
  </form>
</template>
```

## 💡 Tips pour les Props & Emits

### 1. Conventions de nommage
```ts
// Props en camelCase dans le script
const props = defineProps<{
  userName: string
}>()

// Mais kebab-case dans le template
<MyComponent user-name="John" />
```

### 2. Validation avancée des props
```ts
const props = withDefaults(defineProps<{
  count?: number
  callback?: () => void
}>(), {
  count: 0,
  callback: () => console.log('default')
})
```

### 3. Type guard pour les props optionnelles
```ts
const hasData = computed(() => {
  return props.data !== undefined && props.data !== null
})
```

### 4. Typage strict des événements
```ts
const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void
  (e: 'change', value: string, oldValue: string): void
}>()
```

### 5. Pattern v-model personnalisé
```ts
// Dans l'enfant
const props = defineProps<{
  modelValue: string
}>()
const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void
}>()
```

### 6. Éviter la mutation directe des props
```ts
// ❌ Mauvais
props.value = newValue

// ✅ Bon
emit('update:value', newValue)
```

### 7. Documentation des props
```ts
/**
 * @prop {string} title - Le titre du composant
 * @prop {number} [count=0] - Le compteur initial
 * @emits {number} update - Émis quand le compteur change
 */
```

### 8. Valeurs par défaut avec TypeScript
```ts
interface Props {
  message: string
  count?: number
  items?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  count: 0,
  items: () => [] // Utiliser une fonction pour les objets/tableaux
})
```

### 9. Props en lecture seule
```ts
// Créer une copie locale des props si besoin de modification
const localValue = ref(props.value)

watch(() => props.value, (newValue) => {
  localValue.value = newValue
})
```

### 10. Validation complexe
```ts
const valid = computed(() => {
  if (props.min !== undefined && props.value < props.min) {
    return false
  }
  if (props.max !== undefined && props.value > props.max) {
    return false
  }
  return true
})
```
