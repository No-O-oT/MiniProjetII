# Mini-Projet microcontrôleur STM32 : *Pong 2D en réseau*

Ce projet a été réalisé par Timothé Corre et Nicolas Trosino, deux élèves du M1 E3A de l'ENS Paris-Saclay, dans le cadre de l'UE d'Informatique Industrielle. Le sujet est relativement libre et a vocation a refléter les notions vues dans l'années.


**_Le projet (et cette présentation) ne sont pas finis et seront rendus complets après les examens_**


Le but est de prendre en main l'utilisation d'un système d'exploitation temps réel (FreeRTOS) sur un microcontrôleur 32 bits (STM32).

Nous avons choisi de revisiter l'un des jeux les plus symboliques et historique de l'informatique : le pong. Nous proposons une sorte de "air-hockey" numérique, un pong en 2D donc. Les joueurs s'affrontent chacun sur leur carte STM32 et ne voient que leur moitié de jeu : leur raquette et la balle ou une indication lorsqu'elle est hors de l'écran.

La valeur ajoutée réside alors dans la communication entre 2 microcontrôleurs (UART ou Bluetooth), qui devront s'échanger les informations afin que chaque joueur puisse jouer sur sa propre carte et ait une vue différente du terrain.

On trouvera sur ce dépôt :
- Le dossier avec le programme Master, qui gère le jeu ;
- Le dossier avec le programme Slave, qui est téléversé sur la carte de l'adversaire.

## Programme Master

La carte qui se voit attribuer ce programme a pour responsabilité de gérer le déroulement du jeu, de transmettre les informations du jeu à la carte slave et de recevoir des informations de cette dernière. Elle est donc en charge de diriger la balle, et de gérer les rebonds sur les raquettes.
Son écran affiche la partie gauche du terrain de jeu.

### Liste des tâches

* LRacket
* Ball 
* BgChanger
* Transmit
* HAL_UART_RxCpltCallback

#### LRacket

* Lit les valeurs renvoyées par le joystick et prévoit le mouvement de la raquette gauche
* Lecture du blackboard
* Affiche la nouvelle position de la raquette et détruit l'ancienne (Mutex pour l'écran)

#### Ball

* Met à jour la position de la balle périodiquement
* Gère les collisions, les rebonds
* Lecture du blackboard
* Gère le game over

#### BgChanger

* Attend une impulsion sur le bouton pour changer la couleur

#### Transmit

* Envoie périodiquement les informations sur le jeu en UART/Bluetooth
  * Coordonnées de la balle, rayon de la balle
  * Etat du jeu

#### HAL_UART_RxCpltCallback

* Reçoit par interruption les valeurs reçues par l'UART7 et modifie le blackboard en conséquence

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

#### BgChanger

* Attend une impulsion sur le bouton pour changer la couleur 


#### HAL_UART_RxCpltCallback

* Reçoit par interruption les valeurs reçues par l'UART7 et modifie le blackboard en conséquence
