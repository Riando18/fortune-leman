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

## Ce qui donne envie de continuer
- Rêve de vie : `DREAMS` (chacun avec `p()` de 0 à 1), choisi dans `vDream()` tant que `S.dream` est vide (aussi pour les anciennes sauvegardes), accompli dans `S.dreamDone`. Milieu de départ `BGS` dans `S.bg` (modeste, moyen, aisé), choisi avant le 3e mois, avec un coefficient sur le score.
- `vCap()` en tête de l'accueil : barre du rêve et « prochaine étape » calculée par `nextStep()`. Y ajouter un cas quand une nouvelle mécanique mérite d'être mise en avant.
- Moments : `mom(émoji, titre, texte)` remplit `S.mom`, affiché par `vMom()`. Un moment bloque « Mois suivant » jusqu'à « Continuer ». `track()` en crée pour les grandes étapes, et `recap()` fait le point à 18 ans puis tous les 5 ans (comparaison avec `medNw()`, médiane inventée).
- Histoires à suites : drapeaux dans `S.fl`. Chaînes actuelles : prêt à un ami (`pret_ami`, `pret_suite`, `ami_retour`, `ami_refus`), collègue rival (`rival1`, `rival2`), faveur du chef (`faveur`, `faveur2`), jeune entreprise (`startup`, `startup2`), Madame Rochat (`voisine1`, `voisine2`). Une suite vérifie son délai avec le mois stocké dans `S.fl`.
- Dans un test Node, vider `S.mom` avec `act('momok')` et choisir un rêve avant d'enchaîner les mois.
- Train de vie `S.life` (0 sobre, 1 normal, 2 confortable, tableau `LIFES`) : coefficient sur les dépenses courantes et effet mensuel sur le bien-être, réglé dans Vie privée. L'aide sociale coûte du bien-être et de la réputation chaque mois.
- 91 situations, dont celles des propriétaires (`taux_hypo`, `energie`, `promoteur`), des patrons (`gros_contrat`, `guerre_prix`, `cyber`) et des actionnaires de clubs (`club_crise`, `club_vedette`). `amort(x)` rembourse les hypothèques.

## Sport
- Sous-page Parcours, Sport (`vSport()`). Chaque sport de `SPORTS` a une échelle `L` de ligues réelles : `[nom, niveau sportif minimal, revenu mensuel, catégorie 0 amateur à 3 élite, cachet par victoire]`. `full` : le contrat pro exige de quitter emploi et études ; `cb` : sport de combat ; `team` : ligue de `CLUBS`.
- Carrière dans `S.sp` : `t` échelon, `lvl` catégorie (tenue à jour par `spSet()`), `sk` niveau sur 100, `tal` talent caché, `inj` mois de blessure, `all` tout miser, `pal` palmarès, `w` et `l` victoires et défaites. `spMig()` convertit une carrière enregistrée avant les échelons. Ancienne carrière dans `S.spPast`.
- `spTick()` tourne dans `next()` avant le calcul du revenu : progression (plafond `spCeil()`, facteur d'âge `spAgeF()`), déclin dès 31 ans, blessures, revenu `r.sport` imposé comme un salaire, licence de CHF 60 par mois en amateur. `spSeason()` clôt la saison en juin : classement sur 12 (équipes), sur 30 (individuels) ou bilan de combats avec cachets, titres au palmarès, promotion automatique tant que la catégorie ne change pas. Un changement de catégorie passe par la situation `sp_up`.
- `sp_up`, `sp_blessure` et `sp_fin` ne sont jamais tirées au sort : `force(id, données)` les déclenche. Les autres situations de sport passent par `pickSit()`, dont trois pour les amateurs (`sp_tournoi`, `sp_barrage`, `sp_offre_club`).
- Mesure d'octobre 2026 sur 150 carrières menées jusqu'au bout : 25 % atteignent une ligue pro, 2 % l'élite. C'est volontairement bien plus généreux que la réalité, et le jeu le dit.
- Clubs : champ `l` (foot par défaut, hockey, basket), classement de fin de saison par ligue, couleurs des nouveaux clubs dans `CC2`. `start()` complète `S.club`, `S.clubV` et `S.clubS` pour les anciennes sauvegardes.

## Détails suisses
- Transports : `trList()` donne les offres selon le canton (`REG` : communauté tarifaire, exploitants, prix), l'âge (tarif jeune avant 25 ans) et l'inflation. `S.trans` vaut none, parc, tp, tp1, ag, ag1 ou car ; `S.half` est le demi-tarif. `trCost()`, `trMob()` (chances d'embauche) et `trJoy()` (bien-être) en découlent.
- Subside à l'assurance maladie `r.sub` : 40 % de la prime sous CHF 38'000 de revenu annuel et CHF 50'000 de fortune.
- Situations : `votation`, `impots_decl`, `caisse_maladie`, `frontiere`.

## Apprentissages, AFP et vie des femmes
- `FIELDS` : `afp:1` seulement quand l'attestation existe dans la branche (pas d'AFP en informatique, électricité, banque, esthétique, assistance en pharmacie, cabinet médical). `cy` donne la durée du CFC quand elle est de 4 ans. Vérifier sur orientation.ch avant d'ajouter une branche.
- Entreprises : `AP[secteur]` donne les métiers d'apprentis `[CFC, AFP ou null]`, ou `null` si la branche ne forme pas d'apprentis (sécurité privée). `apOf()` sert l'écran et l'action d'engagement ; l'apprenti garde son métier dans `n`.
- Grossesse : la situation `enfant` lance `S.preg` (9 mois), la naissance a lieu dans `next()`. Mère avec emploi : `S.mat` (congé de maternité, 80 %). Père avec emploi : `S.pat` (deux semaines). `fire()` ne licencie pas pendant la grossesse ni le congé.
- Situations réservées au personnage féminin : `preg_annonce`, `regles`, `entretien_enfant`, `salaire_egal`, `remarques`, `reseau_f`. `charge` vaut pour tous les parents en couple.
- `T()` accorde les titres au féminin : tester chaque nouveau titre avec `S.sex='f'`.

## Âge de départ, anniversaire et cadeaux
- Le joueur choisit son âge de départ (16 à 50 ans, `S.age0`) et sa date de naissance (`S.bd` : jour et mois). Dès 18 ans, une étape de plus demande son parcours (`PROFS`, stocké dans `S.prof`). `applyStart()`, appelé au choix du canton, en déduit diplômes, expérience, emploi, économies, logement, 2e pilier et obligations militaires.
- Le temps reste compté depuis 16 ans : un départ à l'âge A pose `S.m = S.m0 = (A − 16) × 12`. Le calendrier commence toujours en août 2027 : `dateLbl()` et `yrNow()` retranchent `S.m0`. Ne jamais calculer une année ou un âge à partir de `S.m` à la main : utiliser `age()`, `ageAt(mois)`, `dateLbl()`, `yrNow()`.
- L'âge change le mois de naissance (`bk()` : décalage par rapport à août). `track()` fête l'anniversaire ce mois-là et y déclenche `recap()`. La retraite reste à `S.m === 588`.
- Cadeaux : `vGifts()` dans Vie privée, action `gift`, dernier cadeau par destinataire dans `S.gifts`. Situation `noel` en novembre et décembre.
- Impôts : aucun avant 18 ans ni sur un salaire d'apprenti (CFC ou AFP sans allocation de formation), tant que le revenu imposable annuel reste sous CHF 30'000.

## Départ guidé, emploi temporaire, loisirs et comptes
- Guide de départ : `GD` (étapes avec `tab`, `sel` sélecteur de l'élément à toucher, `ok()`), étape courante `S.gd` (−1 : terminé). `gdTick()` tourne au début de `render()`, avance les étapes et change d'onglet ; `render()` pose la classe `gd-hi` (cadre et doigt) sur l'élément visé. Il remplace l'ancien tutoriel `TUT` pour les nouvelles parties.
- Compte en banque : `S.acct` est faux au début d'une nouvelle partie. Sans compte, pas de postulation, de mission, de stage ni d'apprentissage. L'action `bank` l'ouvre. Dans un test Node, appeler `act('bank','bc')` après `act('pick', …)`.
- Autres comptes dès 18 ans (`vAccts()`) : épargne `S.sv` (comptée dans `nw()`, retraits limités à CHF 20'000 par mois) et compte commercial `S.bizAcct`, exigé par `cocreate`.
- Emploi : 10 offres par mois, chacune avec un site d'origine `src`. `vTemp()` propose des missions temporaires (`S.job.tmp` mois restants) et des stages (`S.job.stage`, bonus dans `S.stg[domaine]`). La fin de mission est traitée dans `next()`.
- Onglet Vie, « Loisirs et achats » (`vLoisirs()`) : abonnements `ABO` (formules `[nom, prix, prix étudiant]`, choisis dans `S.abo`, coût ajouté à `r.subs`), sorties `OUT` (compteur du mois dans `S.out`), achats `GOODS` (`S.own`). Situations de boîte de nuit `mad_*` après une soirée (`S.clubM`).
- Prix relevés en octobre 2026 : Netflix, Disney+, Spotify, Apple Music et Deezer viennent de comparateurs suisses ; Prime Video, blue Sport, DAZN et NonStop Gym sont des estimations (`est:1`).

## Employeurs, permis, animaux et crédit
- Employeurs : `isBig(nom)` (petite entreprise si le nom correspond à `SMALLRX`), `perkOf(nom)` renvoie l'avantage du personnel (`ag` chez CFF : transports gratuits ; `bank` ; `food` ; `tel`), `perk(clé)` teste l'employeur courant (`curEmp()`, emploi ou apprentissage). `empInfo()` l'affiche.
- Grande entreprise : la moitié du brevet, du diplôme ES, du diplôme fédéral et des langues est payée (`empPays()`, `tcost()`), avec un engagement de deux ans dans `S.bond` (remboursement facturé en cas de démission). Apprentis : +3 points aux examens (`S.study.bon`). Petite entreprise : six mois d'expérience en plus à la fin du CFC (dans `track()`).
- Contrats : une offre sur cinq est un CDD de 12 mois (`o.cdd`, puis `S.job.tmp` et `S.job.cdd`).
- Permis de conduire par étapes dans Vie privée (`vPermis()`, état `S.pc`, actions `pc` et `pcl`) : premiers secours, test de la vue, théorie, sensibilisation, leçons (`MONI`, `PACKS`, prix selon le canton), examen pratique (`pcP()`). L'ancienne formation `permis` de `TRAIN` est masquée.
- Paiement en plusieurs fois : `credit(libellé, montant, mensualités)`, refusé avec des poursuites ou deux factures en retard. Utilisé pour les achats, les leçons et le vétérinaire. Le leasing est refusé de la même façon.
- Animaux : `PETS`, adoptés dans `S.pets` (deux au plus), coût mensuel dans `r.subs`, impôt sur les chiens en mars, situation `veto`.
- Livraison Uber Eats : trois petits business (`livv`, `livt`, `livc`, ce dernier avec `car:1`). Athlétisme : championnats suisses et Diamond League dans `spSeason()`.
