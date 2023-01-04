# Exercice 2 : Jeu de mémoire

Réaliser un jeu de mémoire avec Vue.js qui affiche plusieurs cartes face cachée et qui permet à l'utilisateur de retourner deux cartes à la fois, en essayant de trouver des paires de cartes identiques.

Fonctionnalités :

- Afficher une grille de cartes face cachée (par exemple, 4x4 cartes)
- Permettre à l'utilisateur de cliquer sur une carte pour la retourner et révéler l'image qui se trouve dessous
- Si l'utilisateur retourne deux cartes identiques, les laisser visible et afficher un message de réussite
- Si l'utilisateur retourne deux cartes différentes, les retourner face cachée et afficher un message d'erreur
- Afficher un compteur de points qui augmente à chaque paire trouvée et afficher un message de fin de jeu lorsque toutes les paires ont été trouvées
- Ajouter une fonction "recommencer" qui remet le jeu à zéro (en remélangeant les cartes et en réinitialisant le compteur de points)

Voici une suggestion d'api pour récupérer des images de cartes :

```
https://deckofcardsapi.com/api/deck/new/draw/?count=8
```

Rendu :

- Fichier à rendre : `memory.html`
- Librairies autorisées : `vue`
