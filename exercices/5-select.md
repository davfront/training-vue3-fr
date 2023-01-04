# Exercice 5 : Composant Select

Réaliser un composant Vue.js qui reproduit le comportement de l'élément HTML `<select>`, avec les fonctionnalités suivantes :

- Propriété `multiple` qui permet de sélectionner plusieurs options à la fois
- Propriété `disabled` qui désactive le composant et empêche toute sélection
- Propriété `options` qui prend en entrée une liste d'options sous forme de tableau d'objets, avec au minimum les champs value et text
- Propriété `search` qui active un champ de recherche parmi les options

Voici les fonctionnalités attendues :

- Affichage de la liste des options avec la possibilité de sélectionner une ou plusieurs options (selon la valeur de `multiple`)
- Désactivation du composant lorsque `disabled` est `true`
- Mise à jour de la liste des options lorsque la propriété `options` est modifiée
- Affichage d'un champ de recherche lorsque `search` est `true`, qui permet de filtrer les options en fonction de la valeur saisie,
- Acceptation d'un `v-model` avec pour valeur la sélection courante du composant

Rendu :

- Fichier à rendre: `MultiSelect.vue`
- Librairies autorisées : `vue`
