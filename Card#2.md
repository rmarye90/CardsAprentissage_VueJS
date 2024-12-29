# Réactivité dans Vue.js [2/40]

## 📝 Description
La réactivité dans Vue.js permet de créer des données qui, lorsqu'elles sont modifiées, mettent automatiquement à jour l'interface utilisateur. Vue 3 offre deux APIs principales pour la réactivité:

- **ref()**: Pour les valeurs primitives (string, number, boolean)
- **reactive()**: Pour les objets et tableaux

## 💡 Recommandations Vue.js
- Utiliser `ref()` par défaut pour plus de clarté
- Éviter de mélanger `ref` et `reactive`
- Ne pas destructurer les objets `reactive`
- Utiliser `computed()` pour les valeurs dérivées
- Préférer la Composition API avec `<script setup>`

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref, reactive, computed } from 'vue'

// Utilisation de ref
const count = ref<number>(0)
const message = ref<string>('Hello')

// Utilisation de reactive
const user = reactive({
  name: 'John',
  age: 25
})

// Valeur calculée
const doubleCount = computed(() => count.value * 2)

// Modification des valeurs
const increment = () => {
  count.value++
  user.age++
}
</script>

<template>
  <div class="p-4">
    <p>Count: {{ count }}</p>
    <p>Double: {{ doubleCount }}</p>
    <p>Message: {{ message }}</p>
    <p>User: {{ user.name }} ({{ user.age }})</p>
    
    <button 
      @click="increment"
      class="mt-4 bg-blue-500 text-white px-4 py-2 rounded"
    >
      Increment
    </button>
  </div>
</template>
```

## ❓ QCM

1. Quelle API utiliser pour une valeur primitive ?
   - [ ] reactive()
   - [x] ref()
   - [ ] computed()
   - [ ] watch()

2. Comment accéder à la valeur d'une ref dans le script ?
   - [ ] count
   - [x] count.value
   - [ ] count()
   - [ ] count.get()

3. Les objets reactive peuvent-ils être destructurés ?
   - [ ] Oui, sans perte de réactivité
   - [x] Non, cela perd la réactivité
   - [ ] Seulement avec toRefs()
   - [ ] Uniquement pour les propriétés de premier niveau

4. Un computed est :
   - [ ] Mutable
   - [x] Une valeur en lecture seule basée sur d'autres données réactives
   - [ ] Une fonction normale
   - [ ] Un watcher

5. Quelle est la bonne façon de modifier une ref ?
   - [ ] count++
   - [x] count.value++
   - [ ] count.set(count.value + 1)
   - [ ] count(count() + 1)

6. Comment créer une ref typée en TypeScript ?
   - [x] const count = ref<number>(0)
   - [ ] const count: number = ref(0)
   - [ ] const count = ref(0 as number)
   - [ ] const count = new Ref<number>(0)

7. Quelle méthode utiliser pour convertir un objet reactive en refs ?
   - [ ] toRef()
   - [x] toRefs()
   - [ ] toReactive()
   - [ ] toValue()

8. Dans le template, pour une ref :
   - [ ] Il faut utiliser .value
   - [x] La valeur est automatiquement "unwrapped"
   - [ ] Il faut utiliser une méthode get()
   - [ ] Il faut utiliser une computed

9. reactive() peut être utilisé avec :
   - [ ] string
   - [ ] number
   - [x] object
   - [ ] boolean

10. Un computed :
    - [ ] Peut être modifié directement
    - [x] Se met à jour automatiquement quand ses dépendances changent
    - [ ] Doit être appelé comme une fonction
    - [ ] Est identique à une méthode

## ✏️ Exercice Pratique

Créez un gestionnaire de tâches simple avec :
- Une liste de tâches (reactive)
- Un compteur de tâches totales et complétées (computed)
- La possibilité d'ajouter/supprimer/compléter des tâches

Structure de base :

```vue
<script setup lang="ts">
import { ref, reactive, computed } from 'vue'

interface Task {
  id: number
  title: string
  completed: boolean
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

<details>
<summary>Voir la solution</summary>

```vue
<script setup lang="ts">
import { ref, reactive, computed } from 'vue'

interface Task {
  id: number
  title: string
  completed: boolean
}

const newTaskTitle = ref('')
const tasks = reactive<Task[]>([])
let nextId = 0

const totalTasks = computed(() => tasks.length)
const completedTasks = computed(() => tasks.filter(task => task.completed).length)

const addTask = () => {
  if (newTaskTitle.value.trim()) {
    tasks.push({
      id: nextId++,
      title: newTaskTitle.value,
      completed: false
    })
    newTaskTitle.value = ''
  }
}

const removeTask = (id: number) => {
  const index = tasks.findIndex(task => task.id === id)
  if (index !== -1) {
    tasks.splice(index, 1)
  }
}

const toggleTask = (task: Task) => {
  task.completed = !task.completed
}
</script>

<template>
  <div class="p-4 max-w-md mx-auto">
    <h1 class="text-2xl font-bold mb-4">Task Manager</h1>
    
    <div class="flex gap-2 mb-4">
      <input 
        v-model="newTaskTitle"
        @keyup.enter="addTask"
        type="text"
        class="flex-1 border p-2 rounded"
        placeholder="New task..."
      />
      <button 
        @click="addTask"
        class="bg-blue-500 text-white px-4 py-2 rounded"
      >
        Add
      </button>
    </div>

    <div class="mb-4">
      <p>Total tasks: {{ totalTasks }}</p>
      <p>Completed: {{ completedTasks }}</p>
    </div>

    <ul class="space-y-2">
      <li 
        v-for="task in tasks" 
        :key="task.id"
        class="flex items-center gap-2 p-2 border rounded"
      >
        <input 
          type="checkbox"
          :checked="task.completed"
          @change="toggleTask(task)"
        />
        <span :class="{ 'line-through': task.completed }">
          {{ task.title }}
        </span>
        <button 
          @click="removeTask(task.id)"
          class="ml-auto text-red-500"
        >
          Delete
        </button>
      </li>
    </ul>
  </div>
</template>
```
</details>

## 💡 Tips & Bonnes Pratiques

- Utilisez toujours `ref()` pour les primitives
- N'oubliez jamais `.value` lors de la modification d'une ref dans le script
- Préférez `computed` aux méthodes pour les valeurs dérivées
- Évitez de modifier directement les propriétés d'un objet reactive
- Utilisez TypeScript pour une meilleure sécurité de type