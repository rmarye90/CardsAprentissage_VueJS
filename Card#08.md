# Event Handling dans Vue.js [8/40]

## 🎯 Objectif
Comprendre la gestion des événements dans Vue.js, qui permet d'interagir avec les actions utilisateur et de déclencher des comportements spécifiques.

## 📝 Description
Le système de gestion d'événements dans Vue.js comprend :
- La directive `v-on` (ou `@` en raccourci)
- Les modificateurs d'événements
- La transmission de paramètres
- Les événements natifs du DOM
- Les événements personnalisés

## 💡 Recommandations Vue.js
- Utiliser la syntaxe raccourcie `@`
- Nommer les handlers de manière claire (handle..., on...)
- Utiliser les modificateurs d'événements appropriés
- Éviter l'utilisation de fonctions fléchées inline complexes
- Typer correctement les événements en TypeScript

## 📌 Exemple

```vue
<script setup lang="ts">
import { ref } from 'vue'

// Types
interface MousePosition {
  x: number
  y: number
}

// État
const count = ref(0)
const mousePosition = ref<MousePosition>({ x: 0, y: 0 })
const inputValue = ref('')
const isTyping = ref(false)
const lastKeyPressed = ref('')

// Handlers
const handleClick = (event: MouseEvent) => {
  count.value++
  mousePosition.value = {
    x: event.clientX,
    y: event.clientY
  }
}

const handleInput = (event: Event) => {
  const target = event.target as HTMLInputElement
  inputValue.value = target.value
}

const handleKeyDown = (event: KeyboardEvent) => {
  lastKeyPressed.value = event.key
  isTyping.value = true
}

const handleKeyUp = () => {
  isTyping.value = false
}

// Avec paramètres personnalisés
const handleActionWithParams = (action: string, id: number, event: Event) => {
  console.log(`Action ${action} sur l'id ${id}`, event)
}
</script>

<template>
  <div class="p-6 max-w-md mx-auto space-y-6">
    <!-- Click basique -->
    <div class="space-y-2">
      <button
        @click="handleClick"
        class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
      >
        Compteur: {{ count }}
      </button>
      
      <p class="text-sm text-gray-600">
        Dernière position: ({{ mousePosition.x }}, {{ mousePosition.y }})
      </p>
    </div>

    <!-- Input avec modificateurs -->
    <div class="space-y-2">
      <input
        v-model="inputValue"
        @keydown="handleKeyDown"
        @keyup="handleKeyUp"
        @input.trim="handleInput"
        class="w-full border p-2 rounded"
        placeholder="Tapez quelque chose..."
      >
      
      <div class="text-sm">
        <p>Valeur: {{ inputValue }}</p>
        <p>Dernière touche: {{ lastKeyPressed }}</p>
        <p>{{ isTyping ? 'En train de taper...' : 'En attente' }}</p>
      </div>
    </div>

    <!-- Événements avec paramètres -->
    <div class="space-y-2">
      <button
        v-for="id in 3"
        :key="id"
        @click="(event) => handleActionWithParams('click', id, event)"
        class="mr-2 px-3 py-1 bg-gray-200 rounded hover:bg-gray-300"
      >
        Action {{ id }}
      </button>
    </div>

    <!-- Stop propagation et prevent default -->
    <div 
      @click="() => console.log('Parent')"
      class="p-4 bg-gray-100 rounded"
    >
      <button
        @click.stop="() => console.log('Child')"
        class="bg-green-500 text-white px-4 py-2 rounded"
      >
        Stop Propagation
      </button>

      <a 
        href="https://vuejs.org"
        @click.prevent="() => console.log('Prevented')"
        class="ml-2 text-blue-500 underline"
      >
        Prevent Default
      </a>
    </div>
  </div>
</template>
```

## ❓ QCM

1. Quelle est la syntaxe raccourcie pour v-on ?
   - [ ] @
   - [ ] :
   - [ ] #
   - [ ] $

2. Comment empêcher la propagation d'un événement ?
   - [ ] @click.prevent
   - [ ] @click.stop
   - [ ] @click.cancel
   - [ ] @click.halt

3. Pour empêcher le comportement par défaut :
   - [ ] @click.stop
   - [ ] @click.default
   - [ ] @click.prevent
   - [ ] @click.halt

4. Pour n'écouter qu'une seule fois :
   - [ ] @click.single
   - [ ] @click.once
   - [ ] @click.one
   - [ ] @click.first

5. Pour les touches du clavier :
   - [ ] @key.enter
   - [ ] @keyboard.enter
   - [ ] @press.enter
   - [ ] @keyup.enter

6. Comment passer des arguments à un handler ?
   - [ ] @click="handler(arg, $event)"
   - [ ] @click="handler(arg)"
   - [ ] @click="{arg}"
   - [ ] @click.args="arg"

7. Pour le clic droit :
   - [ ] @click.right
   - [ ] @rightclick
   - [ ] @click.secondary
   - [ ] @click.context

8. Les modificateurs de touches incluent :
   - [ ] .ctrl
   - [ ] .alt
   - [ ] .shift
   - [ ] .meta

9. Dans un handler d'événement, `this` fait référence à :
   - [ ] le composant
   - [ ] l'élément DOM
   - [ ] undefined (avec setup)
   - [ ] l'événement

10. Pour un événement de souris :
    - [ ] MouseEvent
    - [ ] Event
    - [ ] DOMEvent
    - [ ] PointerEvent

    <h2>Reponses QCM</h2>
<details>
<summary>voire la solution</summary>
| 1. A | 2. B  | 3. C | 4. B | 5. C |
| 6. A | 7. A | 8. A, B, C, D | 9. C | 10. A |
</details>

## ✏️ Exercice Pratique

Créez un composant de dessin simple qui :
- Permet de dessiner en maintenant le clic
- Change de couleur avec les touches (r, g, b)
- Efface avec le clic droit
- Affiche les coordonnées de la souris
- Empêche la sélection de texte pendant le dessin
- Permet d'ajuster la taille du pinceau avec la molette

<details>
<summary>Voir la solution</summary>

```vue
<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

// Types
interface Point {
  x: number
  y: number
  color: string
  size: number
}

// État
const isDrawing = ref(false)
const canvas = ref<HTMLCanvasElement | null>(null)
const ctx = ref<CanvasRenderingContext2D | null>(null)
const currentPosition = ref({ x: 0, y: 0 })
const currentColor = ref('#000000')
const brushSize = ref(5)
const points = ref<Point[]>([])

// Configuration du canvas
onMounted(() => {
  if (!canvas.value) return
  
  ctx.value = canvas.value.getContext('2d')
  if (!ctx.value) return

  // Ajuster la taille du canvas
  resizeCanvas()
  window.addEventListener('resize', resizeCanvas)

  // Configuration initiale du contexte
  ctx.value.lineCap = 'round'
  ctx.value.lineJoin = 'round'
})

onUnmounted(() => {
  window.removeEventListener('resize', resizeCanvas)
})

// Fonctions utilitaires
const resizeCanvas = () => {
  if (!canvas.value) return
  canvas.value.width = canvas.value.offsetWidth
  canvas.value.height = canvas.value.offsetHeight
  redrawCanvas()
}

const redrawCanvas = () => {
  if (!ctx.value || !canvas.value) return

  ctx.value.clearRect(0, 0, canvas.value.width, canvas.value.height)
  
  points.value.forEach((point, index) => {
    const previousPoint = points.value[index - 1]
    
    if (previousPoint) {
      ctx.value!.beginPath()
      ctx.value!.strokeStyle = point.color
      ctx.value!.lineWidth = point.size
      ctx.value!.moveTo(previousPoint.x, previousPoint.y)
      ctx.value!.lineTo(point.x, point.y)
      ctx.value!.stroke()
    }
  })
}

// Handlers des événements
const startDrawing = (event: MouseEvent) => {
  if (event.button !== 0) return // Seulement clic gauche
  
  isDrawing.value = true
  updatePosition(event)
}

const stopDrawing = () => {
  isDrawing.value = false
}

const draw = (event: MouseEvent) => {
  if (!isDrawing.value) return
  
  updatePosition(event)
  
  points.value.push({
    x: currentPosition.value.x,
    y: currentPosition.value.y,
    color: currentColor.value,
    size: brushSize.value
  })

  redrawCanvas()
}

const updatePosition = (event: MouseEvent) => {
  if (!canvas.value) return
  
  const rect = canvas.value.getBoundingClientRect()
  currentPosition.value = {
    x: event.clientX - rect.left,
    y: event.clientY - rect.top
  }
}

const handleKeyPress = (event: KeyboardEvent) => {
  switch(event.key.toLowerCase()) {
    case 'r':
      currentColor.value = '#FF0000'
      break
    case 'g':
      currentColor.value = '#00FF00'
      break
    case 'b':
      currentColor.value = '#0000FF'
      break
    case 'c':
      clearCanvas()
      break
  }
}

const handleWheel = (event: WheelEvent) => {
  event.preventDefault()
  brushSize.value = Math.max(1, Math.min(50, brushSize.value - event.deltaY / 100))
}

const clearCanvas = () => {
  points.value = []
  redrawCanvas()
}

const handleContextMenu = (event: MouseEvent) => {
  event.preventDefault()
  const radius = 20
  points.value = points.value.filter(point => {
    const distance = Math.sqrt(
      Math.pow(point.x - currentPosition.value.x, 2) +
      Math.pow(point.y - currentPosition.value.y, 2)
    )
    return distance > radius
  })
  redrawCanvas()
}
</script>

<template>
  <div class="p-4">
    <div class="mb-4 flex items-center justify-between">
      <div>
        <span class="font-bold">Position:</span>
        ({{ currentPosition.x }}, {{ currentPosition.y }})
      </div>
      
      <div class="space-x-2">
        <span class="font-bold">Taille:</span>
        {{ brushSize }}px
      </div>
      
      <div class="flex items-center space-x-2">
        <span class="font-bold">Couleur:</span>
        <div
          class="w-6 h-6 border rounded"
          :style="{ backgroundColor: currentColor }"
        ></div>
      </div>
      
      <button
        @click="clearCanvas"
        class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600"
      >
        Effacer tout
      </button>
    </div>

    <canvas
      ref="canvas"
      @mousedown="startDrawing"
      @mousemove="draw"
      @mouseup="stopDrawing"
      @mouseleave="stopDrawing"
      @contextmenu="handleContextMenu"
      @wheel="handleWheel"
      tabindex="0"
      @keydown="handleKeyPress"
      class="w-full h-[500px] border rounded bg-white cursor-crosshair"
    ></canvas>

    <div class="mt-4 text-sm text-gray-600">
      <p>Instructions:</p>
      <ul class="list-disc list-inside">
        <li>Clic gauche pour dessiner</li>
        <li>Clic droit pour effacer</li>
        <li>Molette pour ajuster la taille</li>
        <li>Touches R, G, B pour changer de couleur</li>
        <li>Touche C pour tout effacer</li>
      </ul>
    </div>
  </div>
</template>
```
</details>

## 💡 Tips pour l'Event Handling

1. **Typage des événements**
```ts
// Pour les événements DOM natifs
function handleClick(event: MouseEvent) {
  console.log(event.clientX, event.clientY)
}

// Pour les événements personnalisés
function handleCustom(event: CustomEvent<{ id: number }>) {
  console.log(event.detail.id)
}
```

2. **Chaînage des modificateurs**
```vue
<button @click.stop.prevent="handler">
  Arrêt propagation et prevent default
</button>
```

3. **Raccourcis clavier**
```ts
<input @keyup.enter.ctrl="submit">
```

4. **Accès à l'événement dans une méthode inline**
```ts
<button @click="(event) => handler(id, event)">
```

5. **Modificateurs de souris**
```vue
<div @click.left="leftClick"
     @click.right="rightClick"
     @click.middle="middleClick">
```
