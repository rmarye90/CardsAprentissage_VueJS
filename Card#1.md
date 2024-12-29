# Composants Vue.js [1/40]

## 📝 Description
Un composant Vue.js est un bloc de code réutilisable qui encapsule du HTML, du CSS et du JavaScript. C'est l'équivalent d'une brique de Lego qui peut être réutilisée à travers votre application. 

### Structure d'un composant :
- **Template**: La partie HTML du composant
- **Script**: La logique en JavaScript/TypeScript
- **Style**: Le style CSS/Tailwind
- **State**: Les données propres au composant
- **Methods**: Les fonctions du composant

## 💡 Recommandations Vue.js
- Utiliser la syntaxe Single File Component (.vue)
- Nommer les composants en PascalCase (UserProfile)
- Un composant = une responsabilité
- Privilégier `<script setup>` avec TypeScript

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

const increment = () => {
  count.value++
}
</script>

<template>
  <div class="flex flex-col items-center p-4">
    <h2 class="text-xl mb-4">Mon Compteur</h2>
    <p class="mb-2">Compteur: {{ count }}</p>
    <button 
      @click="increment"
      class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
    >
      Incrémenter
    </button>
  </div>
</template>
```

## ❓ QCM

1. Un composant Vue.js est :
   - [ ] Un fichier HTML uniquement
   - [ ] Un bloc de code réutilisable combinant HTML, CSS et JavaScript
   - [ ] Un fichier JavaScript uniquement
   - [ ] Une feuille de style CSS

2. L'extension de fichier pour un composant Vue est :
   - [ ] .js
   - [ ] .vue
   - [ ] .html
   - [ ] .ts

3. Pour déclarer une variable réactive dans Vue 3 :
   - [ ] const count = 0
   - [ ] let count = 0
   - [ ] const count = ref(0)
   - [ ] var count = 0

4. La convention de nommage pour les composants Vue est :
   - [ ] camelCase
   - [ ] snake_case
   - [ ] PascalCase
   - [ ] kebab-case

5. Pour accéder à la valeur d'une ref dans le script :
   - [ ] count
   - [ ] count.value
   - [ ] count()
   - [ ] this.count

6. La balise pour définir le template est :
   - [ ] `<html>`
   - [ ] `<div>`
   - [ ] `<template>`
   - [ ] `<body>`

7. Le script d'un composant moderne Vue 3 se définit avec :
   - [ ] `<script>`
   - [ ] `<script setup>`
   - [ ] `<javascript>`
   - [ ] `<code>`

8. Pour afficher une variable dans le template :
   - [ ] {count}
   - [ ] {{count}}
   - [ ] $(count)
   - [ ] [count]

9. SFC signifie :
   - [ ] Simple File Component
   - [ ] Single File Component
   - [ ] Special File Configuration
   - [ ] System File Control

10. Un événement click dans Vue s'écrit :
    - [ ] onclick=""
    - [ ] @click=""
    - [ ] v-click=""
    - [ ] on-click=""

## ✏️ Reponses QCM
| 1. B | 2. B  | 3. C | 4. C | 5. B |
| 6. C | 7. B | 8. B | 9. B | 10. B |

## 💡 TIPS & BONNES PRATIQUES
Organisation des imports

```ts
// D'abord les imports de Vue
import { ref, computed } from 'vue'

// Puis les composants
import MyComponent from './components/MyComponent.vue'

// Enfin les autres imports (utils, types, etc.)
import type { User } from '@/types'
```

**Nommage des fichiers**

- Components: UserProfile.vue
- Composables: useCounter.ts
- Types: types.ts
- Constantes: constants.ts

**Réactivité**

```ts
# ❌ Éviter
let count = 0

# ✅ Préférer
const count = ref(0)
```

**Typage des Props**

```ts
# ❌ Éviter
const props = defineProps(['title'])

# ✅ Préférer
const props = defineProps<{
  title: string
  count?: number
}>()
```
**Structure de dossiers recommandée**

```ts
src/
├── components/
│   ├── common/          # Composants réutilisables
│   └── features/        # Composants spécifiques
├── composables/         # Logique réutilisable
├── types/              # Types TypeScript
└── utils/              # Fonctions utilitaires
```

**Conventions de nommage des événements**

```ts
# ❌ Éviter
emit('change')

# ✅ Préférer
emit('update:modelValue')
emit('user-selected')
```

**Gestion des refs**

```ts
# ❌ Éviter de mélanger ref et reactive
const state = reactive({
  count: ref(0)  // Non nécessaire
})

# ✅ Préférer
const count = ref(0)
# OU
const state = reactive({
  count: 0
})
```

**Performance**

Évitez v-for avec v-if sur le même élément
Utilisez key avec v-for
Préférez computed à des méthodes pour les données dérivées

**Debug**

```ts
# Utilisez console.log avec des objets nommés
console.log({ count: count.value, props })

# Pour la réactivité
import { watch } from 'vue'
watch(count, (newVal, oldVal) => {
  console.log('Count changed:', { newVal, oldVal })
})
```

**VSCode Snippets utiles**

```json
{
  "Vue Component Setup": {
    "prefix": "vbase-ts",
    "body": [
      "<script setup lang=\"ts\">",
      "",
      "</script>",
      "",
      "<template>",
      "  <div>",
      "    $1",
      "  </div>",
      "</template>"
    ]
  }
}
```

## ✏️ Exercice Pratique

Créez un compteur avec les fonctionnalités suivantes :
- Affichage du compteur
- Bouton d'incrémentation
- Bouton de décrémentation
- Minimum de 0
- Change de couleur au-dessus de 5

Structure de base :

```vue
<script setup lang="ts">
import { ref } from 'vue'

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
import { ref, computed } from 'vue'

const count = ref(0)

const increment = () => {
  count.value++
}

const decrement = () => {
  if (count.value > 0) {
    count.value--
  }
}

const counterColor = computed(() => {
  return count.value > 5 ? 'text-red-500' : 'text-blue-500'
})
</script>

<template>
  <div class="flex flex-col items-center p-4">
    <h2 class="text-xl mb-4">Compteur</h2>
    <p class="mb-2 text-2xl" :class="counterColor">
      {{ count }}
    </p>
    <div class="flex gap-2">
      <button 
        @click="decrement"
        class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600"
      >
        -
      </button>
      <button 
        @click="increment"
        class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600"
      >
        +
      </button>
    </div>
  </div>
</template>
```
</details>
