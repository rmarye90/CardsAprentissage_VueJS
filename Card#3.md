# Template Syntax dans Vue.js [3/40]

## 📝 Description
La syntaxe de template Vue.js est une extension de HTML qui permet d'exprimer dynamiquement la relation entre le DOM et les données de l'instance Vue. Elle comprend :

- **Interpolations**: `{{ }}` pour afficher des données
- **Directives**: Attributs spéciaux préfixés par `v-`
- **Expressions JavaScript**: Expressions simples à l'intérieur des interpolations/directives
- **Liaison de données**: One-way (`v-bind` ou `:`) et Two-way (`v-model`)

## 💡 Recommandations Vue.js
- Garder les expressions de template simples
- Utiliser des computed properties pour la logique complexe
- Éviter les modifications d'état dans les expressions
- Utiliser les shorthands (`:` pour `v-bind`, `@` pour `v-on`)

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface User {
  firstName: string
  lastName: string
}

const user = ref<User>({
  firstName: 'John',
  lastName: 'Doe'
})

const message = ref('Hello')
const isActive = ref(true)

const fullName = computed(() => {
  return `${user.value.firstName} ${user.value.lastName}`
})

const styles = computed(() => ({
  'text-red-500': isActive.value,
  'text-gray-500': !isActive.value
}))
</script>

<template>
  <div class="p-4">
    <!-- Interpolation basique -->
    <p>{{ message }}</p>

    <!-- Expression JavaScript -->
    <p>{{ message.split('').reverse().join('') }}</p>

    <!-- Binding d'attributs -->
    <div :class="styles">
      {{ fullName }}
    </div>

    <!-- Binding conditionnel -->
    <div :class="[isActive ? 'active' : '']">
      Statut: {{ isActive ? 'Actif' : 'Inactif' }}
    </div>

    <!-- Event handling -->
    <button 
      @click="isActive = !isActive"
      class="bg-blue-500 text-white px-4 py-2 rounded"
    >
      Toggle Active
    </button>
  </div>
</template>
```

## ❓ QCM

1. Quelle syntaxe utilise-t-on pour l'interpolation de texte ?
   - [ ] {value}
   - [ ] {{ value }}
   - [ ] ${value}
   - [ ] {{{ value }}}

2. Comment fait-on un binding d'attribut (v-bind) en version courte ?
   - [ ] :attribute="value"
   - [ ] @attribute="value"
   - [ ] #attribute="value"
   - [ ] &attribute="value"

3. Quelle expression est valide dans un template ?
   - [ ] {{ user.name.toUpperCase() }}
   - [ ] {{ if (user.name) { return 'Hi' } }}
   - [ ] {{ user.save() }}
   - [ ] {{ const name = user.name }}

4. Comment écouter un événement click ?
   - [ ] :click="handler"
   - [ ] @click="handler"
   - [ ] v-click="handler"
   - [ ] on-click="handler"

5. Pour une condition dans le template :
   - [ ] v-if="condition"
   - [ ] ?if="condition"
   - [ ] if="condition"
   - [ ] #if="condition"

6. Le two-way binding se fait avec :
   - [ ] v-bind
   - [ ] v-model
   - [ ] v-on
   - [ ] v-sync

7. Pour un binding de classe dynamique :
   - [ ] :class="{ active: isActive }"
   - [ ] class="{{ active: isActive }}"
   - [ ] :class="if (isActive) 'active'"
   - [ ] class="isActive ? 'active' : ''"

8. Les directives commencent toujours par :
   - [ ] v-
   - [ ] @
   - [ ] :
   - [ ] #

9. Dans une interpolation, on peut :
   - [ ] Déclarer des variables
   - [ ] Utiliser des opérateurs ternaires
   - [ ] Écrire des boucles for
   - [ ] Définir des fonctions

10. Pour faire un binding de style :
    - [ ] :style="{ color: activeColor }"
    - [ ] style="{{ color: activeColor }}"
    - [ ] :css="{ color: activeColor }"
    - [ ] v-style="{ color: activeColor }"

<h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. B | 2. A  | 3. A | 4. B | 5. A |
| 6. B | 7. A | 8. A | 9. B | 10. A |
</details>

## ✏️ Exercice Pratique

Créez un composant de carte utilisateur qui :
- Affiche les informations d'un utilisateur
- Change de style selon le statut (actif/inactif)
- Permet de modifier le statut en cliquant
- Utilise des classes conditionnelles
- Formate le nom en majuscules
- Affiche l'âge avec un suffixe "ans"

Structure de base :

```vue
<script setup lang="ts">
interface User {
  firstName: string
  lastName: string
  age: number
  active: boolean
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
import { ref, computed } from 'vue'

interface User {
  firstName: string
  lastName: string
  age: number
  active: boolean
}

const user = ref<User>({
  firstName: 'John',
  lastName: 'Doe',
  age: 25,
  active: true
})

const fullName = computed(() => {
  return `${user.value.firstName} ${user.value.lastName}`.toUpperCase()
})

const cardClasses = computed(() => ({
  'bg-green-100': user.value.active,
  'bg-gray-100': !user.value.active,
  'border rounded p-4': true
}))

const toggleStatus = () => {
  user.value.active = !user.value.active
}
</script>

<template>
  <div 
    :class="cardClasses"
    @click="toggleStatus"
    class="max-w-sm mx-auto mt-4 cursor-pointer"
  >
    <h2 class="text-xl font-bold mb-2">{{ fullName }}</h2>
    
    <p class="text-gray-600">
      {{ user.age }} ans
    </p>
    
    <div 
      class="mt-2 text-sm"
      :class="user.active ? 'text-green-600' : 'text-red-600'"
    >
      Statut: {{ user.active ? 'Actif' : 'Inactif' }}
    </div>
  </div>
</template>
```
</details>

## 💡 Tips

- Utilisez des computed properties pour la logique de template complexe
- Préférez `:class` avec un objet pour les classes conditionnelles
- Évitez les expressions complexes dans le template
- Utilisez les shorthands (`@` et `:`) pour plus de lisibilité
- N'oubliez pas que les expressions de template sont évaluées dans leur propre scope

Voulez-vous passer à la carte suivante sur les Props & Emits ?