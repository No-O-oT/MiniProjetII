# Fonctionnement des programmes

Pour fonctionner sur deux cartes, nous avons choisi de développer deux versions d'un même programme : Master et Slave. Il conviendra de compiler l'un de chaque sur deux cartes différentes pour pouvoir utiliser le Pong.


## Programme Master


La carte qui se voit attribuer ce programme a pour responsabilité de gérer le déroulement du jeu, de transmettre les informations du jeu à la carte slave et de recevoir des informations de cette dernière. Elle est donc en charge de diriger la balle, et de gérer les rebonds sur les raquettes.
Son écran affiche la partie gauche du terrain de jeu.

### Liste des tâches

* LRacket
* Ball 
* BgChanger
* Transmit
* HAL_UART_RxCpltCallback
* Lost

#### LRacket

* Lit les valeurs renvoyées par le joystick et prévoit le mouvement de la raquette gauche
* Interaction Blackboard
	* Lit sa position actuelle
	* Ecrit sa nouvelle position
* Affiche la nouvelle position de la raquette et détruit l'ancienne (Mutex pour l'écran)

#### Ball

* Met à jour la position de la balle périodiquement
* Affiche la nouvelle position et détruit la dernière
* Si la balle est sur l'autre écran, affichage d'une flèche
* Gère les collisions, les rebonds
* Interaction Blackboard
	* Lit la position actuelle de la balle
	* Ecrit la nouvelle position
	* Change l'état de la variable `perdu` si la balle est perdue (game over)
* Lorsque la balle est perdue :
	* Affiche un message de perte
	* Détruit la tâche LRacket
	* S'autodétruit

#### BgChanger

* Attend une impulsion sur le bouton pour changer la couleur

#### Transmit

* Envoie périodiquement les informations sur le jeu en UART/Bluetooth
  * Coordonnées de la balle, rayon de la balle
  * Etat du jeu (perdu)

#### HAL_UART_RxCpltCallback

* Reçoit par interruption les valeurs reçues par l'UART7 et modifie le blackboard en conséquence
	* Position de la raquette droite

#### Lost

* Relance la balle sur appui du bouton après avoir perdu (à l'infini)
	* Recrée les tâches Ball et LRacket pour cela


## Programme Slave

La carte qui se voit attribuer ce programme doit recevoir les données de jeu du Master pour afficher la partie relative à son écran, et doit envoyer au master la position de sa raquette.
Son écran affiche la partie droite du terrain de jeu

### Liste des tâches

* RRacket
* BallDisplay
* BgChanger
* HAL_UART_RxCpltCallback

#### RRacket

* Lit les valeurs renvoyées par le joystick et prévoit le mouvement de la raquette gauche
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
	* Etat perdu
	* Rayon de la balle
