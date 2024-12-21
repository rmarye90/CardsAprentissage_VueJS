# CARTE 1A - RECTO : LA RÉACTIVITÉ DANS VUE.JS

[En-tête Vue.js]
----------------------------

## LA RÉACTIVITÉ

### DÉFINITION FONDAMENTALE
Un système qui permet à Vue.js de détecter automatiquement les changements de données et de mettre à jour le DOM en conséquence.

### CONCEPTS CLÉS

#### 🔹 ref()
- Crée un conteneur réactif pour valeurs primitives
- Utilise la propriété .value pour accéder/modifier la valeur
- Automatiquement "déballé" dans les templates
```ts
const count = ref<number>(0) 
// Script : count.value++
// Template : {{ count }}
```

#### 🔹 reactive()
- Crée un objet réactif profond
- Suivi des modifications sur toutes les propriétés imbriquées
- Accès direct aux propriétés
```ts
const user = reactive({
  name: 'John',
  settings: { theme: 'dark' }
})
```

### LIMITATIONS
- ref() nécessite .value dans les scripts
- reactive() perd sa réactivité si déstructuré
- Les propriétés ajoutées après création ne sont pas réactives

[Pied de page avec numéro de carte]

# CARTE 1B - VERSO : EXEMPLE & TIPS
[148 x 210 mm]

### EXEMPLE PRATIQUE COMPLET
```vue
<script setup lang="ts">
import { ref, reactive } from 'vue'

// Valeurs primitives avec ref
const count = ref<number>(0)
const message = ref<string>('Hello')

// Objet complexe avec reactive
const user = reactive({
  name: 'John',
  preferences: {
    theme: 'dark',
    notifications: true
  }
})

function updateUser() {
  count.value++ // Noter .value
  user.preferences.theme = 'light' // Pas de .value
}
</script>

<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Theme: {{ user.preferences.theme }}</p>
    <button @click="updateUser">Update</button>
  </div>
</template>
```

### 💡 TIPS ESSENTIELS
- Utilisez ref() pour tout ce qui est primitif
- Préférez reactive() pour les objets complexes
- N'oubliez jamais .value dans les scripts
- Évitez la déstructuration des objets reactifs

# CARTE 1C - QCM
[148 x 210 mm]

## TESTEZ VOS CONNAISSANCES 

1. Dans quel cas utiliser ref() ?
   - A) Pour les objets complexes
   - B) Pour les valeurs primitives ✅
   - C) Pour les composants
   - D) Uniquement pour les nombres

2. Où doit-on utiliser .value ?
   - A) Partout
   - B) Jamais
   - C) Dans le script uniquement ✅
   - D) Dans le template uniquement

3. Que se passe-t-il si on déstructure un objet reactive ?
   - A) Une erreur est lancée
   - B) La réactivité est perdue ✅
   - C) Rien de spécial
   - D) Les valeurs sont dupliquées

4. reactive() peut-il être utilisé pour des valeurs primitives ?
   - A) Oui
   - B) Non ✅
   - C) Seulement pour les nombres
   - D) Seulement avec TypeScript

5. Comment accéder à une ref dans le template ?
   - A) Toujours avec .value
   - B) Directement sans .value ✅
   - C) Avec une fonction
   - D) Ce n'est pas possible

### EXERCICE PRATIQUE
Créez un compteur avec :
- Une valeur numérique
- Un bouton d'incrémentation
- Un bouton de réinitialisation
- Un objet pour stocker l'historique des valeurs

[Pied de page avec correction au verso]
