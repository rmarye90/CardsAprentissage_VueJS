# Computed Properties [6/40]

## 🎯 Objectif
Comprendre les propriétés calculées (computed properties) dans Vue.js, qui permettent de créer des données dérivées à partir d'autres données de manière réactive et performante.

## 📝 Description
Les computed properties sont des propriétés qui :
- Se calculent automatiquement à partir d'autres données
- Sont mises en cache (cached) basé sur leurs dépendances
- Ne se recalculent que lorsque leurs dépendances changent
- Peuvent être utilisées comme des données normales dans le template

## 💡 Recommandations Vue.js
- Utiliser computed plutôt que des méthodes pour des valeurs dérivées
- Garder les computed properties simples et focalisées
- Éviter les effets secondaires dans les computed
- Préférer computed à des watchers quand possible

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface Todo {
  id: number
  text: string
  done: boolean
}

const todos = ref<Todo[]>([
  { id: 1, text: 'Apprendre Vue', done: true },
  { id: 2, text: 'Créer une app', done: false },
  { id: 3, text: 'Coder en TypeScript', done: false }
])

const showCompleted = ref(false)

// Computed simple
const totalTodos = computed(() => todos.value.length)

// Computed avec logique
const completedTodos = computed(() => 
  todos.value.filter(todo => todo.done)
)

// Computed dépendant d'autres valeurs
const filteredTodos = computed(() => 
  showCompleted.value 
    ? completedTodos.value 
    : todos.value.filter(todo => !todo.done)
)

// Computed avec getter et setter
const completionStatus = computed({
  get: () => {
    const completed = completedTodos.value.length
    return completed === todos.value.length ? 'Tout est fait !' :
           completed === 0 ? 'Rien n\'est fait' :
           `${completed}/${todos.value.length} terminées`
  },
  set: (newValue: boolean) => {
    todos.value.forEach(todo => {
      todo.done = newValue
    })
  }
})
</script>

<template>
  <div class="p-4">
    <div class="mb-4 flex justify-between items-center">
      <h2 class="text-xl font-bold">Mes Todos</h2>
      <span class="text-gray-600">{{ completionStatus }}</span>
    </div>

    <div class="mb-4">
      <label class="flex items-center">
        <input 
          v-model="showCompleted" 
          type="checkbox"
          class="mr-2"
        >
        Montrer les tâches terminées
      </label>
    </div>

    <ul class="space-y-2">
      <li 
        v-for="todo in filteredTodos" 
        :key="todo.id"
        class="flex items-center p-2 border rounded"
      >
        <input 
          v-model="todo.done"
          type="checkbox" 
          class="mr-2"
        >
        <span :class="{ 'line-through': todo.done }">
          {{ todo.text }}
        </span>
      </li>
    </ul>

    <div class="mt-4 text-sm text-gray-600">
      Total: {{ totalTodos }} 
      ({{ completedTodos.length }} terminées)
    </div>
  </div>
</template>
```

## ❓ QCM

1. Une computed property se recalcule :
   - [ ] À chaque rendu
   - [ ] Quand ses dépendances changent
   - [ ] Quand on l'appelle
   - [ ] À intervalle régulier

2. Par rapport à une méthode, une computed property :
   - [ ] Est mise en cache
   - [ ] Est plus rapide à exécuter
   - [ ] Peut prendre des paramètres
   - [ ] Est asynchrone

3. Une computed property peut :
   - [ ] Avoir un getter et un setter
   - [ ] Être async/await
   - [ ] Modifier directement ses dépendances
   - [ ] Retourner undefined

4. La mise en cache d'une computed :
   - [ ] Est basée sur ses dépendances réactives
   - [ ] Dure un temps fixe
   - [ ] Est manuelle
   - [ ] N'existe pas

5. Dans une computed, on accède à une ref :
   - [ ] Avec .value
   - [ ] Directement
   - [ ] Avec $refs
   - [ ] Avec this

6. Une computed property doit :
   - [ ] Retourner une valeur
   - [ ] Modifier l'état
   - [ ] Appeler des API
   - [ ] Émettre des événements

7. Les computed properties sont utiles pour :
   - [ ] Filtrer des listes
   - [ ] Formatter des données
   - [ ] Calculer des totaux
   - [ ] Faire des appels API

8. Une computed property avec setter :
   - [ ] Peut être utilisée avec v-model
   - [ ] Est asynchrone
   - [ ] Ne peut pas avoir de getter
   - [ ] Est plus lente

9. Les dépendances d'une computed sont :
   - [ ] Détectées automatiquement
   - [ ] Déclarées manuellement
   - [ ] Configurables
   - [ ] Optionnelles

10. Pour des calculs complexes, il est préférable d'utiliser :
    - [ ] Des méthodes
    - [ ] Des computed properties
    - [ ] Des watchers
    - [ ] Des refs

<h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. B | 2. A  | 3. A | 4. B | 5. A |
| 6. A | 7. A, B, C | 8. A | 9. A | 10. B |
</details>

## ✏️ Exercice Pratique

Créez un composant de panier d'achat avec :
- Liste de produits avec quantité
- Sous-total par produit
- Total du panier
- TVA (20%)
- Total avec TVA
- Réduction si le total dépasse 100€
- Frais de livraison gratuits au-dessus de 50€

<details>
<summary>Voir la solution</summary>

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface Product {
  id: number
  name: string
  price: number
  quantity: number
}

const products = ref<Product[]>([
  { id: 1, name: 'Clavier Mécanique', price: 89.99, quantity: 1 },
  { id: 2, name: 'Souris Gaming', price: 59.99, quantity: 1 },
  { id: 3, name: 'Tapis de souris', price: 19.99, quantity: 2 },
  { id: 4, name: 'Casque Audio', price: 129.99, quantity: 1 }
])

// Sous-total par produit
const productSubtotals = computed(() => {
  return products.value.map(product => ({
    ...product,
    subtotal: +(product.price * product.quantity).toFixed(2)
  }))
})

// Sous-total du panier (avant TVA et réduction)
const cartSubtotal = computed(() => {
  return +productSubtotals.value
    .reduce((sum, product) => sum + product.subtotal, 0)
    .toFixed(2)
})

// TVA (20%)
const vat = computed(() => {
  return +(cartSubtotal.value * 0.20).toFixed(2)
})

// Réduction (10% si total > 100€)
const discount = computed(() => {
  return cartSubtotal.value > 100 
    ? +(cartSubtotal.value * 0.10).toFixed(2) 
    : 0
})

// Frais de livraison
const shippingCost = computed(() => {
  return cartSubtotal.value > 50 ? 0 : 5.99
})

// Total final
const finalTotal = computed(() => {
  return +(cartSubtotal.value + vat.value - discount.value + shippingCost.value).toFixed(2)
})

// Mettre à jour la quantité
const updateQuantity = (productId: number, newQuantity: number) => {
  const product = products.value.find(p => p.id === productId)
  if (product) {
    product.quantity = Math.max(0, newQuantity)
  }
}
</script>

<template>
  <div class="max-w-3xl mx-auto p-6">
    <h2 class="text-2xl font-bold mb-6">Panier d'achat</h2>

    <!-- Liste des produits -->
    <div class="mb-8">
      <div class="bg-gray-100 p-4 rounded-t">
        <div class="grid grid-cols-5 gap-4 font-semibold">
          <div class="col-span-2">Produit</div>
          <div>Prix</div>
          <div>Quantité</div>
          <div>Total</div>
        </div>
      </div>

      <div class="divide-y border-x border-b">
        <div 
          v-for="product in productSubtotals" 
          :key="product.id"
          class="grid grid-cols-5 gap-4 p-4 items-center"
        >
          <div class="col-span-2">{{ product.name }}</div>
          <div>{{ product.price.toFixed(2) }}€</div>
          <div class="flex items-center gap-2">
            <button 
              @click="updateQuantity(product.id, product.quantity - 1)"
              class="w-8 h-8 rounded-full bg-gray-200 hover:bg-gray-300"
            >
              -
            </button>
            <span>{{ product.quantity }}</span>
            <button 
              @click="updateQuantity(product.id, product.quantity + 1)"
              class="w-8 h-8 rounded-full bg-gray-200 hover:bg-gray-300"
            >
              +
            </button>
          </div>
          <div>{{ product.subtotal }}€</div>
        </div>
      </div>
    </div>

    <!-- Résumé -->
    <div class="bg-gray-50 p-6 rounded-lg">
      <h3 class="text-lg font-semibold mb-4">Résumé de la commande</h3>
      
      <div class="space-y-2 text-sm">
        <div class="flex justify-between">
          <span>Sous-total</span>
          <span>{{ cartSubtotal }}€</span>
        </div>

        <div class="flex justify-between">
          <span>TVA (20%)</span>
          <span>{{ vat }}€</span>
        </div>

        <div class="flex justify-between text-green-600" v-if="discount">
          <span>Réduction (10%)</span>
          <span>-{{ discount }}€</span>
        </div>

        <div class="flex justify-between">
          <span>Frais de livraison</span>
          <span>
            <template v-if="shippingCost === 0">
              <span class="text-green-600">Gratuit</span>
            </template>
            <template v-else>
              {{ shippingCost }}€
            </template>
          </span>
        </div>

        <div class="border-t pt-2 mt-4">
          <div class="flex justify-between font-bold text-lg">
            <span>Total</span>
            <span>{{ finalTotal }}€</span>
          </div>
        </div>

        <div class="mt-4 text-sm text-gray-600">
          <p v-if="cartSubtotal <= 100" class="text-orange-600">
            Ajoutez {{ (100 - cartSubtotal).toFixed(2) }}€ d'articles pour bénéficier d'une réduction de 10% !
          </p>
          <p v-if="cartSubtotal <= 50" class="text-orange-600">
            Ajoutez {{ (50 - cartSubtotal).toFixed(2) }}€ d'articles pour la livraison gratuite !
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
```
</details>


## 💡 Tips pour les Computed Properties

### 1. Composition de computed
```ts
const basePrice = computed(() => /* ... */)
const withTax = computed(() => basePrice.value * 1.2)
```

### 2. Debounce d'une computed
```ts
import { debounce } from 'lodash-es'

const debouncedValue = computed(() => {
  return debounce(() => {
    return expensiveComputation()
  }, 300)
})
```

### 3. Memoization avec computed
```ts
const memoizedValue = computed(() => {
  const key = `${dependency1.value}-${dependency2.value}`
  if (!cache.has(key)) {
    cache.set(key, expensiveComputation())
  }
  return cache.get(key)
})
```

### 4. Type sûr avec computed
```ts
const total = computed<number>(() => {
  return items.value.reduce((sum, item) => sum + item.price, 0)
})
```

### 5. Computed avec garde de type
```ts
const validItems = computed(() => {
  return items.value.filter((item): item is ValidItem => {
    return isValidItem(item)
  })
})
```
