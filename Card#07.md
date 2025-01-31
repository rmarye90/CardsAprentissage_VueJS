# Directives de Base dans Vue.js [7/40]

## 🎯 Objectif
Comprendre les directives de base de Vue.js (v-if, v-show, v-for) qui permettent de contrôler le rendu et la structure du DOM.

## 📝 Description
Les directives de base sont :
- **v-if/v-else/v-else-if** : Rendu conditionnel (ajoute/supprime les éléments du DOM)
- **v-show** : Visibilité conditionnelle (toggle display: none)
- **v-for** : Rendu de listes et itération sur des objets

## 💡 Recommandations Vue.js
- Utiliser v-show pour des toggles fréquents
- Utiliser v-if pour des conditions qui changent rarement
- Toujours utiliser :key avec v-for
- Éviter v-if avec v-for sur le même élément
- Préférer computed pour le filtrage de listes

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref } from 'vue'

interface User {
  id: number
  name: string
  isActive: boolean
  role: 'admin' | 'user'
}

const users = ref<User[]>([
  { id: 1, name: 'Alice', isActive: true, role: 'admin' },
  { id: 2, name: 'Bob', isActive: false, role: 'user' },
  { id: 3, name: 'Charlie', isActive: true, role: 'user' }
])

const showInactive = ref(false)
const selectedRole = ref<'all' | 'admin' | 'user'>('all')

const filteredUsers = computed(() => {
  return users.value.filter(user => {
    if (!showInactive.value && !user.isActive) return false
    if (selectedRole.value !== 'all' && user.role !== selectedRole.value) return false
    return true
  })
})
</script>

<template>
  <div class="p-4">
    <!-- Contrôles -->
    <div class="mb-4 space-x-4">
      <label class="inline-flex items-center">
        <input 
          v-model="showInactive" 
          type="checkbox"
          class="mr-2"
        >
        Afficher les utilisateurs inactifs
      </label>

      <select 
        v-model="selectedRole"
        class="border rounded p-1"
      >
        <option value="all">Tous les rôles</option>
        <option value="admin">Admin</option>
        <option value="user">Utilisateur</option>
      </select>
    </div>

    <!-- Liste avec v-if et v-for -->
    <div v-if="filteredUsers.length" class="space-y-2">
      <div
        v-for="user in filteredUsers"
        :key="user.id"
        class="p-3 border rounded"
        :class="{
          'bg-gray-100': !user.isActive,
          'border-green-500': user.role === 'admin'
        }"
      >
        <div class="flex items-center justify-between">
          <span>{{ user.name }}</span>
          
          <div class="space-x-2">
            <span 
              v-if="user.role === 'admin'"
              class="px-2 py-1 text-xs bg-green-100 text-green-800 rounded"
            >
              Admin
            </span>

            <span 
              v-show="!user.isActive"
              class="px-2 py-1 text-xs bg-red-100 text-red-800 rounded"
            >
              Inactif
            </span>
          </div>
        </div>
      </div>
    </div>

    <!-- Message si aucun résultat -->
    <div 
      v-else
      class="text-center p-4 text-gray-500"
    >
      Aucun utilisateur ne correspond aux critères
    </div>
  </div>
</template>
```

## ❓ QCM

1. Quelle est la différence principale entre v-if et v-show ?
   - [ ] v-if supprime/ajoute l'élément du DOM, v-show utilise display: none
   - [ ] v-if est plus rapide que v-show
   - [ ] v-show permet plus de conditions
   - [ ] v-if permet l'animation

2. Avec v-for, la directive :key est :
   - [ ] Obligatoire
   - [ ] Optionnelle
   - [ ] Déconseillée
   - [ ] Automatique

3. On peut utiliser v-for sur :
   - [ ] Un tableau
   - [ ] Un objet
   - [ ] Un nombre
   - [ ] Une chaîne

4. v-else doit :
   - [ ] Suivre immédiatement un v-if
   - [ ] Être sur n'importe quel élément
   - [ ] Avoir une condition
   - [ ] Être dans une boucle

5. Pour un rendu conditionnel qui change souvent, il est préférable d'utiliser :
   - [ ] v-if
   - [ ] v-show
   - [ ] v-else
   - [ ] v-for

6. La syntaxe correcte pour v-for est :
   - [ ] v-for="item in items"
   - [ ] v-for="items"
   - [ ] v-for="item of items"
   - [ ] v-for="(item, items)"

7. Dans une boucle v-for, pour accéder à l'index :
   - [ ] v-for="(item, index) in items"
   - [ ] v-for="item.index in items"
   - [ ] v-for="item[index] in items"
   - [ ] v-for="index(item) in items"

8. v-if avec v-for sur le même élément :
   - [ ] Est recommandé
   - [ ] Est déconseillé
   - [ ] Est plus performant
   - [ ] N'a pas d'impact

9. Pour faire une boucle sur un objet :
   - [ ] v-for="(value, key) in object"
   - [ ] v-for="object"
   - [ ] v-for="key in object"
   - [ ] v-for="value of object"

10. Pour filtrer une liste, il est préférable d'utiliser :
    - [ ] Une computed property
    - [ ] v-if dans v-for
    - [ ] Un watcher
    - [ ] Une méthode

<h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. B | 2. A  | 3. A, B, C, D | 4. A | 5. B |
| 6. A | 7. A | 8. B | 9. A | 10. A |
</details>

## ✏️ Exercice Pratique

Créez une interface de gestion de tâches avec :
- Liste de tâches avec différentes catégories
- Filtrage par catégorie
- Affichage conditionnel du nombre de tâches
- Toggle pour montrer/cacher les tâches terminées
- Message si aucune tâche
- Compteur par catégorie

<details>
<summary>Voir la solution</summary>

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface Task {
  id: number
  title: string
  completed: boolean
  category: 'work' | 'personal' | 'shopping' | 'health'
}

// État initial
const tasks = ref<Task[]>([
  { id: 1, title: 'Finir le projet Vue', completed: false, category: 'work' },
  { id: 2, title: 'Faire les courses', completed: true, category: 'shopping' },
  { id: 3, title: 'Rendez-vous médecin', completed: false, category: 'health' },
  { id: 4, title: 'Appeler maman', completed: false, category: 'personal' },
  { id: 5, title: 'Sport', completed: true, category: 'health' },
])

// Filtres
const selectedCategory = ref<'all' | Task['category']>('all')
const showCompleted = ref(true)

// Computed Properties
const filteredTasks = computed(() => {
  return tasks.value.filter(task => {
    const categoryMatch = selectedCategory.value === 'all' || 
                         task.category === selectedCategory.value
    const completedMatch = showCompleted.value || !task.completed
    return categoryMatch && completedMatch
  })
})

const categoryCount = computed(() => {
  const counts = {
    work: 0,
    personal: 0,
    shopping: 0,
    health: 0,
    total: tasks.value.length,
    completed: tasks.value.filter(t => t.completed).length
  }

  tasks.value.forEach(task => {
    counts[task.category]++
  })

  return counts
})

const categoryColors = {
  work: 'blue',
  personal: 'purple',
  shopping: 'green',
  health: 'red'
}

const toggleTask = (taskId: number) => {
  const task = tasks.value.find(t => t.id === taskId)
  if (task) {
    task.completed = !task.completed
  }
}
</script>

<template>
  <div class="max-w-4xl mx-auto p-6">
    <header class="mb-8">
      <h1 class="text-3xl font-bold mb-4">Gestionnaire de Tâches</h1>
      
      <!-- Filtres -->
      <div class="flex items-center space-x-4 mb-4">
        <select 
          v-model="selectedCategory"
          class="border rounded-lg px-3 py-2"
        >
          <option value="all">Toutes les catégories</option>
          <option value="work">Travail</option>
          <option value="personal">Personnel</option>
          <option value="shopping">Courses</option>
          <option value="health">Santé</option>
        </select>

        <label class="flex items-center">
          <input
            v-model="showCompleted"
            type="checkbox"
            class="mr-2"
          >
          Afficher les tâches terminées
        </label>
      </div>

      <!-- Compteurs -->
      <div class="grid grid-cols-6 gap-4 mb-6">
        <div
          v-for="(count, category) in categoryCount"
          :key="category"
          class="bg-gray-100 rounded-lg p-3 text-center"
          v-show="category !== 'completed'"
        >
          <div class="text-xl font-bold">{{ count }}</div>
          <div class="text-sm text-gray-600 capitalize">{{ category }}</div>
        </div>
      </div>
    </header>

    <!-- Liste des tâches -->
    <div class="space-y-2">
      <template v-if="filteredTasks.length">
        <div
          v-for="task in filteredTasks"
          :key="task.id"
          class="flex items-center p-4 border rounded-lg hover:bg-gray-50"
          :class="{
            'opacity-75': task.completed
          }"
        >
          <input
            type="checkbox"
            :checked="task.completed"
            @change="toggleTask(task.id)"
            class="mr-4"
          >
          
          <span 
            class="flex-grow"
            :class="{ 'line-through': task.completed }"
          >
            {{ task.title }}
          </span>

          <span
            class="px-3 py-1 text-sm rounded-full"
            :class="`bg-${categoryColors[task.category]}-100 text-${categoryColors[task.category]}-800`"
          >
            {{ task.category }}
          </span>
        </div>
      </template>

      <div 
        v-else
        class="text-center py-8 text-gray-500"
      >
        <p v-if="selectedCategory !== 'all'">
          Aucune tâche dans cette catégorie
        </p>
        <p v-else>
          Aucune tâche à afficher
        </p>
      </div>
    </div>

    <!-- Résumé -->
    <footer class="mt-6 pt-4 border-t">
      <div class="text-sm text-gray-600">
        {{ categoryCount.completed }} tâches terminées sur {{ categoryCount.total }}
      </div>
    </footer>
  </div>
</template>
```
</details>

## 💡 Tips pour les Directives de Base

### 1. Optimisation des performances avec v-show vs v-if
```ts
<!-- Pour les toggles fréquents -->
<div v-show="isVisible">Contenu fréquemment togglé</div>

<!-- Pour les conditions rarement changées -->
<div v-if="isLoggedIn">Contenu rarement changé</div>
```

### 2. Éviter v-if avec v-for
```ts
<!-- ❌ À éviter -->
<div v-for="item in items" v-if="item.isVisible">

<!-- ✅ À la place, utiliser computed -->
<div v-for="item in visibleItems">
```

### 3. Gestion des listes vides
```vue
<template>
  <div v-if="items.length">
    <div v-for="item in items">...</div>
  </div>
  <div v-else>Aucun élément à afficher</div>
</template>
```

### 4. Key avec v-for
```ts
<!-- ❌ À éviter -->
<div v-for="item in items" :key="index">

<!-- ✅ Utiliser un ID unique -->
<div v-for="item in items" :key="item.id">
```

### 5. Destructuring dans v-for
```vue
<div v-for="{ name, id, status } in items" :key="id">
  {{ name }} - {{ status }}
</div>
```
