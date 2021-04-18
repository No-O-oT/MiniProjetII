## Mini-projet d'informatique industrielle

Ce projet a été réalisé par Timothé Corre et Nicolas Trosino, deux élèves du M1 E3A de l'ENS Paris-Saclay, dans le cadre de l'UE d'Informatique Industrielle. Le sujet est relativement libre et a vocation a refléter les notions vues dans l'années.

Le but est de prendre en main l'utilisation d'un système d'exploitation temps réel (FreeRTOS) sur un microcontrôleur 32 bits (STM32).

Nous avons choisi de revisiter l'un des jeux les plus symboliques et historique de l'informatique : le pong. Nous proposons une sorte de "air-hockey" numérique, un pong en 2D donc. Les joueurs s'affrontent chacun sur leur carte STM32 et ne voient que leur moitié de jeu : leur raquette et la balle ou une indication lorsqu'elle est hors de l'écran.

La valeur ajoutée réside alors dans la communication entre 2 microcontrôleurs (UART ou Bluetooth), qui devront s'échanger les informations afin que chaque joueur puisse jouer sur sa propre carte et ait une vue différente du terrain.

On trouvera sur ce dépôt :
- Le dossier avec le programme Master, qui gère le jeu ;
- Le dossier avec le programme Slave, qui est téléversé sur la carte de l'adversaire.
