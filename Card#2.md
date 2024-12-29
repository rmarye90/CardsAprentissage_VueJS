# Réactivité dans Vue.js [2/40]

## 📝 Description
La réactivité est le cœur de Vue.js. Elle permet de synchroniser automatiquement l'état (data) avec l'interface utilisateur (UI). Vue.js propose deux façons principales de créer de la réactivité :

1. ref()

Pour les valeurs primitives (string, number, boolean...)
Nécessite .value dans le script
Accès direct dans le template

2. reactive()

Pour les objets et tableaux
Accès direct aux propriétés
Pas de .value nécessaire