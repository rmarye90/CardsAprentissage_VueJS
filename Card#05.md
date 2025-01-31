# Data Binding dans Vue.js [5/40]

## 🎯 Objectif
Comprendre les différentes méthodes de liaison de données (data binding) dans Vue.js, permettant de synchroniser les données entre le template et la logique.

## 📝 Description
- **One-way binding** (liaison unidirectionnelle) : Des données vers le template
- **Two-way binding** (liaison bidirectionnelle) : Synchronisation dans les deux sens
- **Event binding** : Liaison des événements
- **Attribute binding** : Liaison des attributs HTML

## 💡 Recommandations Vue.js
- Utiliser v-model pour la liaison bidirectionnelle
- Préférer les shorthands (`:` et `@`)
- Éviter d'utiliser v-model avec des props
- Utiliser computed pour les transformations de données

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface FormData {
  username: string
  email: string
  active: boolean
}

const formData = ref<FormData>({
  username: '',
  email: '',
  active: false
})

const isValid = computed(() => {
  return formData.value.username.length > 3 && 
         formData.value.email.includes('@')
})

const buttonClasses = computed(() => ({
  'bg-blue-500': isValid.value,
  'bg-gray-400': !isValid.value,
  'text-white px-4 py-2 rounded': true
}))
</script>

<template>
  <div class="p-4 max-w-md mx-auto">
    <!-- Two-way binding avec v-model -->
    <div class="mb-4">
      <label class="block mb-2">Username:</label>
      <input
        v-model="formData.username"
        type="text"
        class="w-full border p-2 rounded"
      />
    </div>

    <div class="mb-4">
      <label class="block mb-2">Email:</label>
      <input
        v-model="formData.email"
        type="email"
        class="w-full border p-2 rounded"
      />
    </div>

    <!-- Binding d'attribut et de classe -->
    <div class="mb-4">
      <label class="flex items-center">
        <input
          v-model="formData.active"
          type="checkbox"
          class="mr-2"
        />
        Active
      </label>
    </div>

    <!-- Binding conditionnel -->
    <button
      :disabled="!isValid"
      :class="buttonClasses"
      @click="submit"
    >
      Submit
    </button>

    <!-- One-way binding avec interpolation -->
    <pre class="mt-4 p-2 bg-gray-100 rounded">
      {{ formData }}
    </pre>
  </div>
</template>
```

## ❓ QCM

1. Quelle directive utilise-t-on pour le two-way binding ?
   - [ ] v-model
   - [ ] v-bind
   - [ ] v-sync
   - [ ] v-two-way

2. Comment fait-on du binding d'attribut ?
   - [ ] :attribute ou v-bind:attribute
   - [ ] @attribute
   - [ ] #attribute
   - [ ] *attribute

3. Pour du binding de classe conditionnel :
   - [ ] :class="{ active: isActive }"
   - [ ] class="{ active: isActive }"
   - [ ] :class="isActive ? 'active'"
   - [ ] v-class="active: isActive"

4. Comment lier un événement ?
   - [ ] @click="handler"
   - [ ] :click="handler"
   - [ ] on:click="handler"
   - [ ] bind:click="handler"

5. v-model est équivalent à :
   - [ ] :value + @input
   - [ ] :data + @change
   - [ ] :model + @update
   - [ ] :bind + @sync

6. Pour du binding de style :
   - [ ] :style="{ color: color }"
   - [ ] style="{{ color }}"
   - [ ] :css="{ color }"
   - [ ] v-style="color"

7. Quelle syntaxe est correcte ?
   - [ ] v-model.trim="value"
   - [ ] v-bind.trim="value"
   - [ ] v-model:trim="value"
   - [ ] @model.trim="value"

8. Pour un v-model personnalisé :
   - [ ] modelValue + update:modelValue
   - [ ] value + input
   - [ ] model + change
   - [ ] data + sync

9. Les modificateurs v-model incluent :
   - [ ] .lazy
   - [ ] .number
   - [ ] .trim
   - [ ] .sync

10. Pour du binding HTML brut :
    - [ ] v-html
    - [ ] v-text
    - [ ] v-raw
    - [ ] v-unsafe

<h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. A | 2. A  | 3. A | 4. B | 5. A |
| 6. A | 7. A | 8. A | 9. A, B, C | 10. A |
</details>


## ✏️ Exercice Pratique

Créez un composant de formulaire de réservation avec :
- Champs nom/prénom (text)
- Date de réservation (date)
- Nombre de personnes (number)
- Options supplémentaires (checkbox)
- Validation en temps réel
- Affichage d'un résumé en temps réel

Structure de base :

```vue
<script setup lang="ts">
// importer ce qui est nécessaire
// et créer l'interface et l'état initial

</script>

<template>
  <div class="max-w-md mx-auto p-6">
    <h2 class="text-2xl font-bold mb-6">Formulaire de Réservation</h2>

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

interface Booking {
  firstName: string
  lastName: string
  date: string
  people: number
  options: {
    breakfast: boolean
    parking: boolean
    latecheckout: boolean
  }
}

// État initial
const booking = ref<Booking>({
  firstName: '',
  lastName: '',
  date: '',
  people: 1,
  options: {
    breakfast: false,
    parking: false,
    latecheckout: false
  }
})

// Prix de base par personne
const BASE_PRICE = 100

// Validation
const validations = computed(() => ({
  firstName: booking.value.firstName.length >= 2,
  lastName: booking.value.lastName.length >= 2,
  date: Boolean(booking.value.date),
  people: booking.value.people >= 1 && booking.value.people <= 10
}))

const isValid = computed(() => 
  Object.values(validations.value).every(Boolean)
)

// Calcul du prix total
const totalPrice = computed(() => {
  let total = BASE_PRICE * booking.value.people
  
  if (booking.value.options.breakfast) total += 15 * booking.value.people
  if (booking.value.options.parking) total += 10
  if (booking.value.options.latecheckout) total += 30
  
  return total
})

// Formatage de la date
const formattedDate = computed(() => {
  if (!booking.value.date) return ''
  return new Date(booking.value.date).toLocaleDateString()
})

const handleSubmit = () => {
  if (!isValid.value) return
  
  alert('Réservation envoyée !')
  // Ici vous pourriez envoyer les données à votre API
  console.log(booking.value)
}
</script>

<template>
  <div class="max-w-2xl mx-auto p-6">
    <h2 class="text-2xl font-bold mb-6">Formulaire de Réservation</h2>

    <form @submit.prevent="handleSubmit" class="space-y-6">
      <!-- Informations personnelles -->
      <div class="grid grid-cols-2 gap-4">
        <div>
          <label class="block text-sm font-medium text-gray-700">Prénom</label>
          <input
            v-model="booking.firstName"
            type="text"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
            :class="{ 'border-red-500': !validations.firstName && booking.firstName }"
          />
          <p v-if="!validations.firstName && booking.firstName" class="mt-1 text-sm text-red-600">
            Le prénom doit faire au moins 2 caractères
          </p>
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700">Nom</label>
          <input
            v-model="booking.lastName"
            type="text"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
            :class="{ 'border-red-500': !validations.lastName && booking.lastName }"
          />
          <p v-if="!validations.lastName && booking.lastName" class="mt-1 text-sm text-red-600">
            Le nom doit faire au moins 2 caractères
          </p>
        </div>
      </div>

      <!-- Date et Nombre de personnes -->
      <div class="grid grid-cols-2 gap-4">
        <div>
          <label class="block text-sm font-medium text-gray-700">Date d'arrivée</label>
          <input
            v-model="booking.date"
            type="date"
            :min="new Date().toISOString().split('T')[0]"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700">Nombre de personnes</label>
          <input
            v-model.number="booking.people"
            type="number"
            min="1"
            max="10"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          />
        </div>
      </div>

      <!-- Options -->
      <div class="space-y-2">
        <h3 class="text-lg font-medium text-gray-700">Options</h3>
        
        <div class="flex items-center">
          <input
            v-model="booking.options.breakfast"
            type="checkbox"
            class="h-4 w-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500"
          />
          <label class="ml-2 text-sm text-gray-700">Petit déjeuner (+15€/pers)</label>
        </div>

        <div class="flex items-center">
          <input
            v-model="booking.options.parking"
            type="checkbox"
            class="h-4 w-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500"
          />
          <label class="ml-2 text-sm text-gray-700">Parking (+10€)</label>
        </div>

        <div class="flex items-center">
          <input
            v-model="booking.options.latecheckout"
            type="checkbox"
            class="h-4 w-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500"
          />
          <label class="ml-2 text-sm text-gray-700">Départ tardif (+30€)</label>
        </div>
      </div>

      <!-- Résumé -->
      <div class="bg-gray-50 p-4 rounded-lg">
        <h3 class="text-lg font-medium text-gray-700 mb-2">Résumé de la réservation</h3>
        <div class="space-y-1 text-sm">
          <p>
            <span class="font-medium">Client:</span> 
            {{ booking.firstName }} {{ booking.lastName }}
          </p>
          <p>
            <span class="font-medium">Date:</span> 
            {{ formattedDate }}
          </p>
          <p>
            <span class="font-medium">Personnes:</span> 
            {{ booking.people }}
          </p>
          <p>
            <span class="font-medium">Options:</span>
            <span v-if="!Object.values(booking.options).some(Boolean)" class="italic">
              Aucune option sélectionnée
            </span>
            <span v-else>
              {{ booking.options.breakfast ? 'Petit déjeuner, ' : '' }}
              {{ booking.options.parking ? 'Parking, ' : '' }}
              {{ booking.options.latecheckout ? 'Départ tardif' : '' }}
            </span>
          </p>
          <p class="text-lg font-bold mt-2">
            Total: {{ totalPrice }}€
          </p>
        </div>
      </div>

      <!-- Submit -->
      <div class="flex justify-end">
        <button
          type="submit"
          :disabled="!isValid"
          class="px-4 py-2 rounded-md text-white font-medium focus:outline-none focus:ring-2 focus:ring-offset-2"
          :class="{
            'bg-blue-600 hover:bg-blue-700': isValid,
            'bg-gray-400 cursor-not-allowed': !isValid
          }"
        >
          Réserver
        </button>
      </div>
    </form>
  </div>
</template>
```
</details>

## 💡 Tips pour le Data Binding

### 1. Modificateurs v-model utiles

```ts
<!-- Attendre que l'utilisateur finisse de taper -->
<input v-model.lazy="searchQuery">

<!-- Convertir en nombre -->
<input v-model.number="age">

<!-- Supprimer les espaces -->
<input v-model.trim="username">
```

### 2. Binding dynamique d'attributs multiples
```ts
<div v-bind="objectOfAttrs">
<!-- Équivalent à -->
<div 
  :class="objectOfAttrs.class"
  :id="objectOfAttrs.id"
  :style="objectOfAttrs.style"
>
```

### 3. Binding de style avec unités
```ts
<div :style="{
  width: width + 'px',
  height: `${height}px`,
  margin: 0
}">
```

### 4. Classes conditionnelles multiples
```ts
<div :class="[
  'base-class',
  { 'active': isActive },
  condition ? 'yes' : 'no'
]">
```

### 5. Validation en temps réel
```ts
const errors = computed(() => ({
  username: username.value.length < 3 ? 'Trop court' : '',
  email: !email.value.includes('@') ? 'Email invalide' : ''
}))
```
