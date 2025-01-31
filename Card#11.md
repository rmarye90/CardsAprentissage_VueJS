# Cycle de Vie des Composants Vue.js [11/40]

## 🎯 Objectif
Comprendre les différentes étapes du cycle de vie d'un composant Vue.js et savoir quand utiliser les hooks correspondants.

## 📝 Description
Le cycle de vie d'un composant représente les différentes étapes par lesquelles passe un composant, de sa création à sa destruction. Chaque étape dispose d'un "hook" (fonction) qui permet d'exécuter du code à un moment précis.

### Hooks principaux :
- **onMounted**: Appelé après que le composant est monté dans le DOM
- **onUpdated**: Appelé après que le composant est mis à jour
- **onUnmounted**: Appelé avant que le composant soit retiré du DOM
- **onBeforeMount**: Appelé juste avant le montage
- **onBeforeUpdate**: Appelé avant une mise à jour
- **onBeforeUnmount**: Appelé avant le démontage

## 💡 Recommandations Vue.js
- Utiliser `onMounted` pour les initialisations (appels API, événements...)
- Utiliser `onUnmounted` pour le nettoyage (désabonnements...)
- Éviter la logique asynchrone dans `onBeforeMount`
- Ne pas accéder au DOM dans `setup()` ou `onBeforeMount`

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref, onMounted, onUnmounted, onUpdated } from 'vue'

const data = ref<string[]>([])
const timer = ref<number>()

// Au montage du composant
onMounted(() => {
  console.log('Composant monté')
  // Initialisation d'un timer
  timer.value = setInterval(() => {
    data.value.push(new Date().toLocaleTimeString())
  }, 1000)
})

// À chaque mise à jour
onUpdated(() => {
  console.log('Composant mis à jour')
})

// Au démontage
onUnmounted(() => {
  console.log('Composant démonté')
  // Nettoyage du timer
  if (timer.value) {
    clearInterval(timer.value)
  }
})
</script>

<template>
  <div class="p-4">
    <h2 class="text-xl mb-4">Horloge</h2>
    <ul class="space-y-2">
      <li v-for="time in data" :key="time">
        {{ time }}
      </li>
    </ul>
  </div>
</template>
```

## ❓ QCM

1. Quel hook est appelé après que le composant est monté dans le DOM ?
   - [ ] onBeforeMount
   - [ ] onMounted
   - [ ] onUpdated
   - [ ] onCreated

2. Où doit-on faire les appels API initiaux ?
   - [ ] onBeforeMount
   - [ ] onMounted
   - [ ] setup
   - [ ] onUpdated

3. Quel hook est utilisé pour le nettoyage des ressources ?
   - [ ] onDestroy
   - [ ] onCleanup
   - [ ] onUnmounted
   - [ ] onBeforeUnmount

4. Dans quel ordre sont appelés les hooks au montage ?
   - [ ] setup → onMounted → onBeforeMount
   - [ ] onBeforeMount → onMounted → setup
   - [ ] setup → onBeforeMount → onMounted
   - [ ] onMounted → setup → onBeforeMount

5. Quand est appelé onUpdated ?
   - [ ] À chaque modification du state
   - [ ] Après chaque rendu du Virtual DOM
   - [ ] Une seule fois au montage
   - [ ] Avant chaque modification du DOM

6. Le hook onUnmounted est appelé :
   - [ ] Avant le retrait du DOM
   - [ ] Après le retrait du DOM
   - [ ] Pendant le retrait du DOM
   - [ ] Au démarrage de l'application

7. Peut-on accéder au DOM dans setup() ?
   - [ ] Oui, toujours
   - [ ] Non, jamais
   - [ ] Oui, avec nextTick
   - [ ] Seulement avec async/await

8. Le hook onBeforeUpdate est appelé :
   - [ ] Avant chaque mise à jour du DOM virtuel
   - [ ] Après chaque mise à jour du DOM
   - [ ] Une seule fois au montage
   - [ ] Avant le démontage

9. Dans quel hook doit-on s'abonner à des événements externes ?
   - [ ] setup
   - [ ] onBeforeMount
   - [ ] onMounted
   - [ ] onUpdated

10. Comment nettoyer un intervalle dans un composant ?
    - [ ] Automatiquement
    - [ ] Dans onBeforeUnmount
    - [ ] Dans onUnmounted
    - [ ] Ce n'est pas nécessaire

<h2>Réponses QCM</h2>
<details>
<summary>Voir la solution</summary>
| 1. B | 2. B | 3. C | 4. C | 5. B |
| 6. A | 7. B | 8. A | 9. C | 10. C |
</details>

## 💡 TIPS & BONNES PRATIQUES

**Organisation des hooks**
```ts
# ✅ Préférer
import { onMounted, onUnmounted, onUpdated } from 'vue'

// Regrouper les hooks liés
onMounted(() => {
  initializeData()
  setupEventListeners()
})

onUnmounted(() => {
  cleanupEventListeners()
  clearTimers()
})

# ❌ Éviter
// Hooks éparpillés dans le code
setupEventListeners()
onMounted(() => {})
initializeData()
onUnmounted(() => {})
```

**Gestion des effets asynchrones**
```ts
# ✅ Préférer
onMounted(async () => {
  try {
    const data = await fetchData()
    processData(data)
  } catch (error) {
    handleError(error)
  }
})

# ❌ Éviter
// Ne pas laisser les erreurs non gérées
onMounted(async () => {
  const data = await fetchData()
  processData(data)
})
```

**Nettoyage des ressources**
```ts
# ✅ Préférer
const subscription = ref()

onMounted(() => {
  subscription.value = someService.subscribe()
})

onUnmounted(() => {
  subscription.value?.unsubscribe()
})

# ❌ Éviter
// Oublier de nettoyer les ressources
onMounted(() => {
  someService.subscribe()
})
```

**Éviter les effets de bord dans setup()**
```ts
# ✅ Préférer
const data = ref(null)

onMounted(() => {
  data.value = loadInitialData()
})

# ❌ Éviter
// Effets de bord dans setup
const data = ref(loadInitialData()) // Peut causer des problèmes
```

**nextTick pour les manipulations DOM**
```ts
# ✅ Préférer
import { nextTick } from 'vue'

const updateDOM = async () => {
  await nextTick()
  // Maintenant le DOM est à jour
  doSomethingWithDOM()
}

# ❌ Éviter
// Manipuler le DOM immédiatement après une mutation
const updateDOM = () => {
  state.value = newValue
  doSomethingWithDOM() // Peut ne pas refléter les changements
}
```

**Gestion des watchers dans le cycle de vie**
```ts
# ✅ Préférer
const stopWatch = watch(source, callback)
onUnmounted(() => {
  stopWatch() // Nettoyer le watcher
})

# ❌ Éviter
// Watcher sans nettoyage
watch(source, callback)
```

Je vais ajouter ces sections à la carte précédente, après les TIPS & BONNES PRATIQUES et avant l'exercice pratique.

## 🎯 Cas d'utilisation courants

### Gestion des thèmes
```vue
<!-- ThemeProvider.vue -->
<script setup lang="ts">
import { provide, ref, watch } from 'vue'
import type { InjectionKey } from 'vue'

interface ThemeContext {
  isDark: boolean
  primaryColor: string
  setPrimaryColor: (color: string) => void
  toggleTheme: () => void
}

export const themeKey = Symbol() as InjectionKey<ThemeContext>

const isDark = ref(false)
const primaryColor = ref('#3b82f6') // blue-500 par défaut
const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)')

const toggleTheme = () => {
  isDark.value = !isDark.value
  updateThemeClass()
}

const setPrimaryColor = (color: string) => {
  primaryColor.value = color
  document.documentElement.style.setProperty('--primary-color', color)
}

const updateThemeClass = () => {
  document.documentElement.classList.toggle('dark', isDark.value)
}

// Écoute des préférences système
systemPrefersDark.addEventListener('change', (e) => {
  isDark.value = e.matches
})

// Initialisation
isDark.value = systemPrefersDark.matches
updateThemeClass()

provide(themeKey, {
  isDark,
  primaryColor,
  setPrimaryColor,
  toggleTheme
})
</script>

<template>
  <slot></slot>
</template>

<style>
:root {
  --primary-color: #3b82f6;
}

.dark {
  --bg-color: #1a1a1a;
  --text-color: #ffffff;
}

:root:not(.dark) {
  --bg-color: #ffffff;
  --text-color: #1a1a1a;
}
</style>
```

### Système d'authentification
```vue
<!-- AuthProvider.vue -->
<script setup lang="ts">
import { provide, ref } from 'vue'
import type { InjectionKey } from 'vue'

interface User {
  id: string
  name: string
  email: string
}

interface AuthContext {
  user: User | null
  isAuthenticated: boolean
  login: (email: string, password: string) => Promise<void>
  logout: () => Promise<void>
}

export const authKey = Symbol() as InjectionKey<AuthContext>

const user = ref<User | null>(null)
const isAuthenticated = ref(false)

const login = async (email: string, password: string) => {
  // Simulation d'un appel API
  const response = await fetch('/api/login', {
    method: 'POST',
    body: JSON.stringify({ email, password })
  })
  const userData = await response.json()
  user.value = userData
  isAuthenticated.value = true
}

const logout = async () => {
  await fetch('/api/logout')
  user.value = null
  isAuthenticated.value = false
}

provide(authKey, {
  user,
  isAuthenticated,
  login,
  logout
})
</script>

<template>
  <slot></slot>
</template>
```

### Exemple concret complet : Système de thème avec préférences utilisateur

```ts
<!-- App.vue -->
<script setup lang="ts">
import { ThemeProvider } from './providers/ThemeProvider.vue'
import { SettingsPanel } from './components/SettingsPanel.vue'
</script>

<template>
  <ThemeProvider>
    <div class="min-h-screen transition-colors duration-200" 
         :class="{ 'dark': isDark }">
      <SettingsPanel />
      <router-view />
    </div>
  </ThemeProvider>
</template>

<!-- providers/types.ts -->
import type { InjectionKey } from 'vue'

export interface ThemeContext {
  isDark: boolean
  primaryColor: string
  fontSize: number
  setPrimaryColor: (color: string) => void
  setFontSize: (size: number) => void
  toggleTheme: () => void
}

export const themeKey = Symbol() as InjectionKey<ThemeContext>

<!-- providers/ThemeProvider.vue -->
<script setup lang="ts">
import { ref, provide, watch } from 'vue'
import { themeKey } from './types'

const STORAGE_KEY = 'user-theme-preferences'

// État
const isDark = ref(false)
const primaryColor = ref('#3b82f6')
const fontSize = ref(16)

// Chargement des préférences
const loadPreferences = () => {
  const saved = localStorage.getItem(STORAGE_KEY)
  if (saved) {
    const { dark, color, size } = JSON.parse(saved)
    isDark.value = dark
    primaryColor.value = color
    fontSize.value = size
  }
}

// Sauvegarde des préférences
const savePreferences = () => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify({
    dark: isDark.value,
    color: primaryColor.value,
    size: fontSize.value
  }))
}

// Actions
const toggleTheme = () => {
  isDark.value = !isDark.value
}

const setPrimaryColor = (color: string) => {
  primaryColor.value = color
}

const setFontSize = (size: number) => {
  fontSize.value = size
}

// Observers
watch([isDark, primaryColor, fontSize], () => {
  savePreferences()
  updateCSSVariables()
}, { deep: true })

// Mise à jour des variables CSS
const updateCSSVariables = () => {
  const root = document.documentElement
  root.style.setProperty('--primary-color', primaryColor.value)
  root.style.setProperty('--font-size-base', `${fontSize.value}px`)
}

// Initialisation
loadPreferences()
updateCSSVariables()

// Provide du contexte
provide(themeKey, {
  isDark,
  primaryColor,
  fontSize,
  setPrimaryColor,
  setFontSize,
  toggleTheme
})
</script>

<template>
  <slot></slot>
</template>

<!-- components/SettingsPanel.vue -->
<script setup lang="ts">
import { inject } from 'vue'
import { themeKey } from '../providers/types'

const theme = inject(themeKey)
if (!theme) throw new Error('ThemeProvider not found')
</script>

<template>
  <div class="fixed bottom-4 right-4 p-4 bg-white dark:bg-gray-800 rounded-lg shadow-lg">
    <div class="space-y-4">
      <div>
        <label class="block text-sm font-medium mb-1">
          Theme
        </label>
        <button 
          @click="theme.toggleTheme"
          class="px-4 py-2 bg-primary-500 text-white rounded hover:bg-primary-600"
        >
          {{ theme.isDark ? 'Switch to Light' : 'Switch to Dark' }}
        </button>
      </div>

      <div>
        <label class="block text-sm font-medium mb-1">
          Primary Color
        </label>
        <input 
          type="color" 
          :value="theme.primaryColor"
          @input="e => theme.setPrimaryColor((e.target as HTMLInputElement).value)"
          class="w-full"
        >
      </div>

      <div>
        <label class="block text-sm font-medium mb-1">
          Font Size
        </label>
        <input 
          type="range" 
          :value="theme.fontSize"
          @input="e => theme.setFontSize(Number((e.target as HTMLInputElement).value))"
          min="12"
          max="24"
          class="w-full"
        >
      </div>
    </div>
  </div>
</template>
```

Cet exemple complet montre comment implémenter un système de thème complet avec :
- Persistance des préférences
- Gestion du thème sombre/clair
- Personnalisation de la couleur principale
- Ajustement de la taille de police
- Mise à jour en temps réel
- Type-safety avec TypeScript


## ✏️ Exercice Pratique

Créez un composant Chronomètre avec :
- Démarrage/Arrêt
- Affichage minutes:secondes
- Réinitialisation
- Nettoyage correct des ressources

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

## 🔍 Solution

<details>
<summary>Voir la solution</summary>

```vue
<script setup lang="ts">
import { ref, onUnmounted } from 'vue'

const time = ref(0)
const isRunning = ref(false)
const timer = ref<number>()

const formatTime = (time: number): string => {
  const minutes = Math.floor(time / 60)
  const seconds = time % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
}

const start = () => {
  if (!isRunning.value) {
    isRunning.value = true
    timer.value = setInterval(() => {
      time.value++
    }, 1000)
  }
}

const stop = () => {
  isRunning.value = false
  if (timer.value) {
    clearInterval(timer.value)
  }
}

const reset = () => {
  stop()
  time.value = 0
}

onUnmounted(() => {
  if (timer.value) {
    clearInterval(timer.value)
  }
})
</script>

<template>
  <div class="flex flex-col items-center p-4">
    <h2 class="text-2xl mb-4">Chronomètre</h2>
    <div class="text-4xl font-mono mb-4">
      {{ formatTime(time) }}
    </div>
    <div class="flex gap-2">
      <button 
        @click="isRunning ? stop() : start()"
        class="px-4 py-2 rounded"
        :class="isRunning ? 'bg-red-500' : 'bg-green-500'"
      >
        {{ isRunning ? 'Stop' : 'Start' }}
      </button>
      <button 
        @click="reset"
        class="bg-gray-500 px-4 py-2 rounded"
      >
        Reset
      </button>
    </div>
  </div>
</template>
```
</details>
