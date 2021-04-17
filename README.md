Mini-projet d'informatique industrielle

Ce projet a été réalisé par Timothé Corre et Nicolas Trosino, deux élèves du M1 E3A de l'ENS Paris-Saclay, dans le cadre de l'UE d'Informatique Industrielle. Le sujet est relativement libre et a vocation a refléter les notions vues dans l'années.

Nous avons choisi de revisiter un des jeux les plus symboliques et historique de l'informatique : le pong. Nous proposons une sorte de "air-hockey" numérique, un pong en 2D donc. Les joueurs s'affrontent chacun sur leur carte STM32 et ne voient que leur moitié de jeu : leur raquette et la balle ou une indication lorsqu'elle est hors de l'écran.

On trouvera sur ce dépôt :
- Le dossier avec le programme Master, qui gère le jeu ;
- Le dossier avec le programme Slave, qui est téléversé sur la carte de l'adversaire.

Les deux cartes communiquent par liaison série et une liaison Bluetooth est également envisageable.