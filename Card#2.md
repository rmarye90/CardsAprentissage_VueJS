# Réactivité dans Vue.js

## Description
La réactivité est le cœur de Vue.js. Elle permet de synchroniser automatiquement l'état (data) avec l'interface utilisateur (UI).

### Deux façons principales de créer de la réactivité :

1. `ref()`
   - Pour les valeurs primitives (string, number, boolean)
   - Nécessite l'utilisation de `.value` dans le script
   - Automatiquement "unwrapped" dans les templates

2. `reactive()`
   - Pour les objets et tableaux
   - Accès direct aux propriétés
   - Pas de `.value` nécessaire

## Recommandations officielles

- Préférer `ref()` pour la majorité des cas
- Utiliser `reactive()` uniquement pour des objets complexes
- Ne pas mélanger `ref` et `reactive`
- Toujours déstructurer les refs dans le setup avec `.value`

## Exemple de code

```vue
<script setup lang="ts">
import { ref, reactive, computed } from 'vue'

// Utilisation de ref pour les valeurs primitives
const count = ref<number>(0)
const name = ref<string>('John')

// Utilisation de reactive pour les objets
const user = reactive({
  firstName: 'John',
  lastName: 'Doe',
  age: 25
})

// Computed property basée sur des valeurs réactives
const fullName = computed(() => {
  return user.firstName + ' ' + user.lastName
})

// Méthodes manipulant les valeurs réactives
const incrementCount = () => {
  count.value++ // Notez l'utilisation de .value pour ref
}

const updateUser = () => {
  user.age++ // Pas besoin de .value pour reactive
}
</script>

<template>
  <div class="p-4">
    <div class="mb-4">
      <p>Count: {{ count }}</p>
      <button 
        @click="incrementCount"
        class="bg-blue-500 text-white px-4 py-2 rounded"
      >
        Increment
      </button>
    </div>

    <div class="mb-4">
      <p>Full Name: {{ fullName }}</p>
      <p>Age: {{ user.age }}</p>
      <button 
        @click="updateUser"
        class="bg-green-500 text-white px-4 py-2 rounded"
      >
        Age +1
      </button>
    </div>
  </div>
</template>
```

## QCM

1. Quelle fonction utilise-t-on pour rendre réactive une valeur primitive ?
   - [ ] reactive()
   - [x] ref()
   - [ ] computed()
   - [ ] watch()

2. Comment accède-t-on à la valeur d'une ref dans le script ?
   - [ ] count()
   - [x] count.value
   - [ ] getValue(count)
   - [ ] count.get()

[Les autres questions du QCM...]

## Exercice pratique

Créez un composant "UserProfile" qui :
- Gère un objet user avec name, email et age
- Affiche ces informations
- Permet de les modifier via des inputs
- Calcule si l'utilisateur est majeur (computed)
- Change le style en fonction de l'âge

```vue
<script setup lang="ts">
// À vous de jouer !
</script>

<template>
  <div>
    <!-- Votre code ici -->
  </div>
</template>
```

## Solution

<details>
<summary>Cliquez pour voir la solution</summary>

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

- Utilisez toujours ref() pour les primitives
- N'oubliez jamais .value lors de la modification d'une ref dans le script
- Préférez computed aux méthodes pour les valeurs dérivées
- Évitez de modifier directement les propriétés d'un objet reactive
- Utilisez TypeScript pour une meilleure sécurité de type