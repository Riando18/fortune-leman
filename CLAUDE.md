# Fortune Léman

Jeu de simulation financière suisse, en français, joué sur iPhone comme application web installée.
En ligne : https://riando18.github.io/fortune-leman/ (GitHub Pages, branche `main`, racine).

## Mettre à jour le jeu
- Tout le jeu est dans `index.html` (HTML, CSS et JavaScript dans un seul fichier, sans dépendance à installer).
- Un push sur `main` met le site à jour en une à deux minutes. Le propriétaire ne touche pas à GitHub : c'est Claude qui pousse.
- `sw.js` sert le réseau d'abord, puis la copie hors ligne. Pas besoin de changer son numéro de version pour une mise à jour.
- Icônes : `icon-180.png`, `icon-192.png`, `icon-512.png`. Manifeste : `manifest.webmanifest`.

## Sauvegardes des joueurs
- La partie est dans `localStorage`, clé `fortune-leman-4`, avec `v:4` dans l'état.
- Un nouveau champ d'état doit avoir une valeur par défaut dans `fresh()` : `start()` fusionne l'ancienne sauvegarde par-dessus.
- Si un changement casse les anciennes sauvegardes, prévenir le propriétaire avant de pousser, puis changer la clé et `v`.

## Avant de pousser
- Vérifier la syntaxe du script et simuler des parties avec Node (stub de `document`, appels à `act()` et `next()`), y compris 40 ans de jeu.
- Vérifier le rendu à largeur de téléphone (400 px), sans défilement horizontal.

## Contenu
- Les textes du jeu vouvoient le joueur.
- Pas de vrais logos ni de photos. Les chiffres (salaires, impôts, cours, conditions des banques, valeurs des clubs) sont des ordres de grandeur inventés, et le jeu le dit.
- But du propriétaire : un jeu qui entraîne à investir et à gérer son argent en Suisse.

## Parcours de vie
- La partie commence à 16 ans (août 2027) et finit par un bilan à 65 ans. `cal()` donne le mois civil, `age()` l'âge.
- Formations à plein temps dans `S.study` (apprentissage CFC ou AFP, gymnase, maturité pro, université, HES, stage d'avocat, HEP), diplômes dans `S.dip`, emploi dans `S.job`, offres du mois dans `S.offers`.
- `qlvl(domaine)` donne le niveau de qualification de 0 à 6, `T()` accorde les noms de métiers au féminin.
- Avant 18 ans, pas d'investissement. Les parents et l'aide sociale empêchent la faillite quand le joueur n'a ni dette ni actif.
- Statut : `S.nat` (pays), `S.ch` (nationalité suisse), `S.permit` (B ou C), `S.natu` (naturalisation en cours), `S.mil` (obligation de servir). Règles reprises du droit suisse : impôt à la source pour le permis B seulement, taxe d'exemption de 3 % (CHF 400 au minimum, 11 ans au plus, jusqu'à 37 ans), Lex Koller pour le permis B hors UE et AELE.
- Chômage : `S.contrib` (mois cotisés, 12 exigés), `orp()` inscrit, `S.chom` mois restants, `S.chomW` mois de suspension après démission. Indemnité de 70 %, ou 80 % sous CHF 3'797, réduite d'un quart avec moins de 2 postulations dans le mois.
- Reconversions : deuxième CFC en 2 ans (`S.dip.cfc0` garde l'ancien), article 32 (formation `art32`), allocation de formation (`S.study.afo`). `vGuide()` affiche conditions et débouchés de chaque diplôme : le tenir à jour quand une formation change.
- Messagerie : `S.mail` (adresse fictive et messages), `mail()` envoie un message, le troisième argument de `log()` dans `next()` le double d'un e-mail. Postuler : `S.pend` (réponse des RH le mois suivant), entretien dans `S.itv` (questions `ITV`), proposition acceptée par `hire()`. Une adresse est exigée pour postuler.
- Situations à choix : tableau `SITS` (`c` condition, `cd` délai avant répétition en mois, `x` texte, `o` choix avec leur effet et le texte de résultat). `pickSit()` en tire une environ 3 mois sur 10 et bloque le mois suivant tant que `S.sit` n'est pas résolue. Le texte est figé dans `S.sit.x` au tirage, car l'état change après le choix.
- Vie privée : `S.joy` (bien-être) et `S.pro` (réputation), réseau `S.net`, couple `S.partner`, enfants `S.kids` (mois de naissance), mobilité `S.trans` et `S.car`, factures `S.bills` (poursuite après 3 mois), dette saisie `S.owe`, registre `S.pours`, casier `S.record`, prison `S.jail`, mensualités `S.subs`.
- Ne pas faire tomber en faillite ni licencier un employeur réel : une suppression de poste se dit « réorganisation ».
- Réalisme de l'embauche : `salary()` réduit le salaire avant 18 ans (60 %) et sans diplôme (`LM[0]`), chaque offre peut exiger de l'expérience (`o.rx` en mois, comparé à `xpEq()`), `chance()` pénalise les mineurs et les non-diplômés.
- Revenus annexes : travail non déclaré `S.black` (`BLACK`, risque de contrôle mensuel, pas de cotisation ni d'expérience), petits business `S.hus` (`HUS`, revenu par `husInc()`, deux au plus), paris `S.bet` (espérance négative, bilan dans `S.betTot`).
- Logement : quitter ses parents exige un revenu brut de 3 fois le loyer (1,5 fois en colocation, `S.coloc`) et une garantie de loyer (`S.dep`, comptée dans la fortune). Coût de la vie par canton dans `col`.
- Réglages : onglet `aide` (bouton « Réglages » de l'en-tête), rendu par `vAide()`. Thème dans `localStorage` sous `fortune-leman-theme` (hors de l'état de partie), appliqué par `setTheme()` via l'attribut `data-theme`.
- Avant le début de la partie (menu ou création du personnage), `body.pre` masque l'en-tête, les onglets et la barre du bas.
- Menu de démarrage : `menu` (variable hors état, vraie à chaque lancement) affiche `vMenu()` avec Continuer, Charger un emplacement et Nouvelle partie. Dans un test Node, mettre `menu=false` après le chargement du script.
- Onglet Guide (`vGuideTab()`) : `LEARN` explique chaque écran, `SOL` donne un plan par objectif, chaque étape pouvant porter un test d'état qui coche l'étape. Ajouter une entrée `SOL` quand une nouvelle mécanique peut bloquer un joueur. Le lexique `LEX` s'y affiche aussi.
- Sauvegarde : `autoSave` (préférence `fortune-leman-auto`, hors état). En mode manuel, seul `save(true)` écrit.

## Navigation, bilan et succès
- Barre du bas `#nav` : cinq groupes dans `NAV` (Accueil, Parcours, Argent, Vie, Plus). Les onglets du haut sont les sous-pages du groupe courant (`grp()`), `S.gt` retient la dernière page de chaque groupe.
- Parcours est découpé en sous-pages `VSUB` (`S.vsub` : sit, emploi, form, rev, guide). `vVie()` construit toutes les sections puis n'affiche que la sous-page choisie.
- `track()` tourne à la fin de `next()` : il compare l'état à `S.sf` et écrit les étapes de vie dans `S.story` avec `story()`, met à jour `S.topLvl` et `S.topT`, puis appelle `checkAch()`. Une ancienne sauvegarde sans `S.sf` est initialisée sans rien écrire.
- `score()` note la vie sur 1000 (fortune 400, carrière 200, vie privée 250, réputation 150). `vBilan()` (onglet « Ma vie ») l'affiche avec l'histoire, ouvert d'office à 65 ans. `shareTxt()` est le texte partagé.
- Succès : tableau `ACH`, obtenus dans la partie dans `S.ach`, conservés entre les parties dans `localStorage` sous `fortune-leman-ach`.
- 70 situations dans `SITS`. Une situation peut rester ouverte pendant que le joueur agit ailleurs : chaque choix doit revérifier l'état (exemple : ne jamais faire tomber `co.emp` à 0).
- Le score compte la fortune en francs de 2027 (`nw()/S.infl`), sur une échelle de CHF 20'000 à 20 millions. Mesure d'octobre 2026 : un CFC en logistique qui épargne sans rien faire d'autre finit vers CHF 1,5 million nominal à 65 ans, 2e pilier compris, soit environ 900'000 en francs de 2027.
- Situation d'ouverture `debut`, forcée au premier mois par `pickSit()`. `sico()` choisit l'émoji d'une situation d'après son titre (`SICO`).
- Sons : `beep()` (WebAudio, sans fichier), préférence `fortune-leman-snd` hors état, réglage dans `vAide()`.
- Sur téléphone (700 px et moins), l'en-tête tient sur une ligne : la devise se règle dans Réglages, le bouton de l'en-tête n'affiche que la roue.
- Test de toutes les situations : forcer `S.sit` pour chaque entrée de `SITS` dans un état riche (emploi, couple, enfant, entreprise, bien, titres) et exécuter chaque choix.
