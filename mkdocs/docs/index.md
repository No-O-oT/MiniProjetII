# Mini-Projet microcontrôleur STM32 : *Pong 2D en réseau*

Ce projet a été réalisé par Timothé Corre et Nicolas Trosino, deux élèves du M1 E3A de l'ENS Paris-Saclay, dans le cadre de l'UE d'Informatique Industrielle. Le sujet est relativement libre et a vocation à refléter les notions d'Informatique vues dans l'année.

*Par soucis de compatibilité, le projet entier est sur le dépôt et pas seulement les sources, ce qui permet de le faire fonctionner directement après un simple copier-coller*

Ce mini-projet a pour objectif de prendre en main un système d'exploitation temps réel (ici FreeRTOS) sur un microcontrôleur 32 bits (ici une carte STM32).

Nous avons choisi de revisiter l'un des jeux les plus symboliques et historiques de l'informatique : le Pong. Nous proposons une sorte de "air-hockey" numérique et sans fil : un pong en 2D donc. Les joueurs s'affrontent chacun sur une carte STM32 et ne voient que leur moitié de jeu : leur raquette et la balle ou une indication lorsqu'elle est hors de l'écran. Compte tenu de la portée de la liaison Bluetooth, les joueurs doivent être à proximité, mais ils ne sont pas nécessairement dans la même pièce, et capables de voir le jeu de l'adversaire.

C'est en cela que réside la principale valeur ajoutée : la communication entre 2 microcontrôleurs (UART dans un premier temps, Bluetooth enfin), qui doivent s'échanger les informations afin de permettre à chaque joueur de jouer sur sa propre carte.

On trouvera sur ce dépôt :
- Le dossier avec le programme Master, qui gère le jeu ;
- Le dossier avec le programme Slave, qui est téléversé sur la carte de l'adversaire.
L'ordre des cartes est complètement interchangeable, et sans influence sur le déroulement du jeu, à la vue près (gauche pour le Master, droite pour le Slave)

## L'utilisation du Pong2DBT

Pour l'heure, les fonctionnalités implémentées sont les suivantes :

- Deux raquettes, une sur chaque carte, contrôlées en 2 dimensions à l'aide du joystick présent sur la carte ENS accompagnant la carte STM32. Ces dernières :
	- Contrôlent les rebonds en fonction de la position relative de la balle par rapport à la raquette
	- Calculent l'angle de rebond de la balle sur la raquette en fonction de la position d'impact sur la raquette (la position d'impact étant l'ordonnée lors du rebond, pouvant se situer à l'interieur de la raquette si la balle est rapide)
- Une balle faisant des allers-retours d'une carte à l'autre jusqu'à sortir du terrain horizontalement, les bords supérieur et inférieur étant des frontières de rebond. Cette balle :
	- Contrôle sa propre vitesse en l'incrémentant à chaque rebond pour limiter la durée des parties et rendre une partie un peu plus pimentée
	- Permet de relancer le jeu après la perte d'un des joueurs et un appui sur BP2
- Communication entre les cartes possible *via* :
	- Liaison sans-fil Bluetooth à l'aide de modules RN42 à brancher en supplément (voir page Bluetooth), la connexion étant automatique après configuration ;
	- Liaison série asynchrone UART direct entre les cartes à l'aide de trois fils.
	
Une petite vidéo démo montrant une partie, depuis le démarrage des cartes, est présentée ci-dessous. (https://youtu.be/ZiN9ByJ4qZM)

<div class="video-wrapper">
	<iframe width="560" height="315" src="https://www.youtube.com/embed/ZiN9ByJ4qZM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
