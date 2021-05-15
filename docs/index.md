# Mini-Projet microcontrôleur STM32 : *Pong 2D en réseau*

Ce projet a été réalisé par Timothé Corre et Nicolas Trosino, deux élèves du M1 E3A de l'ENS Paris-Saclay, dans le cadre de l'UE d'Informatique Industrielle. Le sujet est relativement libre et a vocation a refléter les notions vues dans l'années.

*Les fonctionnalités prévues par la suite et celles effectivement implémentées sont dans l'onglets _Issues_*

*Par soucis de compatibilité, le projet entier est sur le dépôt et pas seulement les sources, ce qui permet de le faire fonctionner directement avec copier-coller*

Le but est de prendre en main l'utilisation d'un système d'exploitation temps réel (FreeRTOS) sur un microcontrôleur 32 bits (STM32).

Nous avons choisi de revisiter l'un des jeux les plus symboliques et historique de l'informatique : le pong. Nous proposons une sorte de "air-hockey" numérique, un pong en 2D donc. Les joueurs s'affrontent chacun sur leur carte STM32 et ne voient que leur moitié de jeu : leur raquette et la balle ou une indication lorsqu'elle est hors de l'écran.

La valeur ajoutée réside alors dans la communication entre 2 microcontrôleurs (UART ou Bluetooth), qui devront s'échanger les informations afin que chaque joueur puisse jouer sur sa propre carte et ait une vue différente du terrain.

On trouvera sur ce dépôt :
- Le dossier avec le programme Master, qui gère le jeu ;
- Le dossier avec le programme Slave, qui est téléversé sur la carte de l'adversaire.

## L'utilisation du Pong2DBT

Une petite vidéo démo montrant une partie est présentée ci-dessous.

<div class="video-wrapper">
  <iframe width="1280" height="720" src="https://www.youtube.com/embed/vZNyjuNLhIk" frameborder="0" allowfullscreen></iframe>
</div>

Pour l'heure, les fonctionnalités implémentées sont les suivantes :

- 2 Raquettes, une sur chaque carte, contrôlées en 2 dimensions grâce au joystick de la carte ENS.
	- Contrôlent les rebonds
	- L'angle de rebond de la balle sur la raquette est contrôlable en fonction de la position d'impact sur la raquette
- Une balle faisant des allers-retours d'une carte à une autre jusqu'à sortir du terrain
	- Vitesse de la balle incrémentée à chaque rebond pour limiter la durée des parties
	- Relance possible de la balle lorsqu'elle sort du jeu, avec appui sur BP2
- Communication d'une carte à l'autre
	- Bluetooth avec modules RN42, voir page Bluetooth
	- ou UART direct