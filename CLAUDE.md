# Fortune Léman

Jeu de simulation financière suisse, en français, joué sur iPhone comme application web installée.
En ligne : https://riando18.github.io/fortune-leman/ (GitHub Pages, branche `main`, racine).

## Mettre à jour le jeu
- Tout le jeu est dans `index.html` (HTML, CSS et JavaScript dans un seul fichier, sans dépendance à installer).
- Un push sur `main` met le site à jour en une à deux minutes. Le propriétaire ne touche pas à GitHub : c'est Claude qui pousse.
- `sw.js` sert le réseau d'abord, puis la copie hors ligne. Pas besoin de changer son numéro de version pour une mise à jour.
- Icônes : `icon-180.png`, `icon-192.png`, `icon-512.png`. Manifeste : `manifest.webmanifest`.

## Sauvegardes des joueurs
- La partie est dans `localStorage`, clé `fortune-leman-3`, avec `v:3` dans l'état.
- Un nouveau champ d'état doit avoir une valeur par défaut dans `fresh()` : `start()` fusionne l'ancienne sauvegarde par-dessus.
- Si un changement casse les anciennes sauvegardes, prévenir le propriétaire avant de pousser, puis changer la clé et `v`.

## Avant de pousser
- Vérifier la syntaxe du script et simuler des parties avec Node (stub de `document`, appels à `act()` et `next()`), y compris 40 ans de jeu.
- Vérifier le rendu à largeur de téléphone (400 px), sans défilement horizontal.

## Contenu
- Les textes du jeu vouvoient le joueur.
- Pas de vrais logos ni de photos. Les chiffres (salaires, impôts, cours, conditions des banques, valeurs des clubs) sont des ordres de grandeur inventés, et le jeu le dit.
- But du propriétaire : un jeu qui entraîne à investir et à gérer son argent en Suisse.
