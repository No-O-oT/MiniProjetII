# Fonctionnement des programmes

Pour fonctionner sur deux cartes, nous avons fait le choix de développer deux versions distinctes d'un même programme : la version Master et la version Slave, bien que ces désignations soient plus que jamais remises en causes. Il conviendra de compiler chacune des deux versions sur un des cartes utilisées pour jouer au Pong2BT.

## Programme Master


La carte qui se voit attribuer ce programme a pour responsabilité de gérer le déroulement du jeu, de transmettre les informations du jeu à la carte slave et de recevoir des informations de cette dernière. Elle est donc en charge de diriger la balle, et de gérer les rebonds sur les raquettes.
Son écran affiche toujours la partie gauche du terrain de jeu.

### Liste des tâches

* LRacket
* Ball 
* BgChanger
* Transmit
* HAL_UART_RxCpltCallback
* Lost

#### LRacket

* Lit les valeurs renvoyées par le joystick et fait afficher le mouvement de la raquette gauche
* Interaction Blackboard
	* Lit sa position actuelle
	* Ecrit sa nouvelle position
* Affiche la nouvelle position de la raquette et détruit l'ancienne (Mutex pour l'écran)

#### Ball

* Initie le mouvement de la balle de manière aléatoire 2D dans une fenêtre rectangulaire de départ, et dans un sens aléatoire
* Met à jour la position de la balle périodiquement
* Affiche la nouvelle position et détruit la dernière
* Si la balle est sur l'autre écran, affichage d'une flèche
* Gère les collisions, les rebonds sur les bords supérieur et inférieur de l'écran
* Interaction Blackboard
	* Lit la position actuelle de la balle
	* Ecrit la nouvelle position
	* Change l'état de la variable `perdu` si la balle est perdue (game over)
* Lorsque la balle est perdue :
	* Affiche un message de fin de partie
	* Détruit la tâche LRacket
	* S'autodétruit

#### BgChanger

* Attend une impulsion sur le bouton pour changer la couleur. Cette fonction est un peu sensible compte tenu de sa faible priorité, il faut donc possiblement rester un peu plus longtemps que ce que l'on ferait normalement pour mener à bien l'inversion des couleurs.

#### Transmit

* Envoie périodiquement les informations sur le jeu en UART. Que la transmission soit finalement UART ou sans-fil Bluetooth, rien ne change ici : seul le cablage sera modifié. Les données envoyées sont :
  * Les coordonnées de la balle, le rayon de la balle
  * L'état du jeu (perdu ou en cours)

#### HAL_UART_RxCpltCallback

* Reçoit par interruption les valeurs reçues par l'UART7 et modifie le blackboard en conséquence. Données reçues (émises par la carte slave) :
	* Position de la raquette droite

#### Lost

* Relance la balle lors de l'appui du bouton BP2 après avoir perdu (permet de jouer à l'infini sans appui sur reset) :
	* Recrée les tâches Ball et LRacket qui se sont détruites à la fin de la partie précédente


## Programme Slave

La carte qui se voit attribuer ce programme doit recevoir les données de jeu émises par la première carte (programme Master) pour afficher la partie relative à son écran, et doit envoyer en retour la position de sa raquette.
Son écran affiche toujours la partie droite du terrain de jeu.

### Liste des tâches

* RRacket
* BallDisplay
* BgChanger
* HAL_UART_RxCpltCallback

#### RRacket

* Lit les valeurs renvoyées par le joystick et fait afficher le mouvement de la raquette gauche
* Lecture du blackboard
* Envoie les informations sur la position de la raquette droite par UART à chaque déplacement de la raquette

#### BallDisplay

* Affiche la nouvelle position de la balle et détruit l'ancienne périodiquement
* Lecture du blackboard
* Si la balle est sur l'autre écran, affichage d'une flèche

#### BgChanger

* Attend une impulsion sur le bouton pour changer la couleur 


#### HAL_UART_RxCpltCallback

* Reçoit par interruption les valeurs reçues par l'UART7 et modifie le blackboard en conséquence
	* Position de la balle
	* Etat de la variable perdu
	* Rayon de la balle

## Utilisation de sections critiques et de Mutexes

### Mutex pour le LCD
Notre pong communique avec les joueurs *via* la seule interface qu'est l'écran, il faut donc le partager intelligemment entre des tâches qui se réalisent en même temps et qui peuvent s'interrompre les unes les autres. Pour éviter des soucis d'affichage particulièrement embêtants (notament ceux qui impliquent des couleurs donc bgchanger), on utilise un mutex pour l'écran (dénommé myMutex_LCD et déclaré en ligne 91, créé en lignes 252-253). Lorsqu'une tâche s'accapare le mutex, elle ne peut pas être interrompue par une tâche utilisant le même mutex avant la libération de la ressource. Par exemple, voici la liste des tâches du programme Master utilisant le mutex myMutex_LCD :
* Horloge (lignes 1529 à 1535 de Starhorloge)
* LRacket (lignes 1599 à 1611 de StartLRacket)
* Ball (lignes 1705 à 1711 puis 1734 à 1740 et enfin 1749 à 1786 de StartBall)
* BgChanger (lignes 1827 à 1835 puis 1839 à 1847 de StartBgChanger)

### Section critique pour les coordonnées
On utilise également des sections critiques pour pouvoir réduire les risques de mauvaise lecture d'une ou plusieurs coordonnées (de la balle, d'une raquette ou bien des deux). En effet, lors de la réception des coordonnées, si une tâche "auteur" est interrompue par une tâche "lecteur", les valeurs lues par la tâche lecteur ne seront pas forcément celles que la tâche aurait dû lire. On peut voir un exemple de l'emploi d'une section critique entre les lignes 1879 et 1889 du programme main de la carte dotée du programme Master, dans StartTransmit. Entre les deux balises (début et fin de la tâche critique), le programme ne peut être interrompu par une autre tâche, même de priorité supérieure.