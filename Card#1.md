# 🎴 Carte #1 : La Réactivité dans Vue.js

## 📚 Explication détaillée
La réactivité est le cœur de Vue.js. C'est ce qui permet à votre interface utilisateur de se mettre à jour automatiquement quand vos données changent. Dans Vue 3, la réactivité est construite autour de deux concepts principaux :

1. `ref()` : Pour les valeurs primitives (string, number, boolean)
- Crée un objet réactif avec une propriété `.value`
- Nécessaire car JavaScript ne peut pas détecter les changements sur les valeurs primitives directement

2. `reactive()` : Pour les objets et tableaux
- Crée une version réactive profonde de l'objet
- Suit tous les changements dans l'objet, même les objets imbriqués

## 💡 Recommandations Vue.js
- Utiliser `ref()` pour les valeurs simples
- Utiliser `reactive()` pour les objets complexes
- Éviter de déstructurer les objets réactifs car cela brise la réactivité
- Toujours accéder à `.value` pour les refs dans le script, mais pas nécessaire dans le template

## 🔍 Exemple concret
```vue
<script setup lang="ts">
import { ref, reactive } from 'vue'

// Utilisation de ref pour les valeurs simples
const count = ref<number>(0)
const message = ref<string>('Bonjour')

// Utilisation de reactive pour les objets
const user = reactive({
  name: 'John',
  age: 25,
  preferences: {
    theme: 'dark',
    notifications: true
  }
})

// Fonction pour modifier les valeurs
const incrementCount = () => {
  count.value++ // Noter l'utilisation de .value
  user.age++ // Pas besoin de .value pour reactive
}
</script>

<template>
  <div class="p-4">
    <h1 class="text-2xl mb-4">{{ message }}</h1>
    <p class="mb-2">Compteur: {{ count }}</p>
    <p class="mb-2">Utilisateur: {{ user.name }}, {{ user.age }} ans</p>
    <button 
      @click="incrementCount"
      class="bg-blue-500 text-white px-4 py-2 rounded"
    >
      Incrémenter
    </button>
  </div>
</template>
```

## ❓ QCM
1. Quelle fonction utilise-t-on pour rendre réactive une valeur primitive dans Vue 3?
   - A) reactive()
   - B) ref()
   - C) computed()
   - D) watch()
   - Réponse: B

2. Pourquoi doit-on utiliser .value avec ref()?
   - A) C'est une préférence de style
   - B) Pour des raisons de performance
   - C) Pour permettre à Vue de détecter les changements sur les valeurs primitives
   - D) Ce n'est pas obligatoire
   - Réponse: C

3. Dans le template, faut-il utiliser .value pour accéder à une ref?
   - A) Oui, toujours
   - B) Non, jamais
   - C) Seulement pour les nombres
   - D) Seulement dans les événements
   - Réponse: B

4. Que se passe-t-il si on déstructure un objet reactive?
   - A) Rien de spécial
   - B) Une erreur est lancée
   - C) La réactivité est perdue
   - D) Les valeurs sont dupliquées
   - Réponse: C

5. Quelle est la différence principale entre ref et reactive?
   - A) ref est plus rapide
   - B) reactive ne fonctionne qu'avec les tableaux
   - C) ref est pour les valeurs primitives, reactive pour les objets
   - D) Il n'y a pas de différence
   - Réponse: C

## 💻 Exercice pratique
Créez une petite application de gestion de tâches qui démontre la réactivité :
- Une liste de tâches (array of objects)
- Un compteur de tâches terminées
- La possibilité d'ajouter/supprimer/marquer comme terminée une tâche
- Un filtre pour afficher toutes les tâches/terminées/en cours
