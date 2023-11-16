⚠️CE PROJET A ÉTÉ RÉALISÉ DANS UN CADRE UNIVERSITAIRE. IL A ÉTÉ DÉPOSÉ SUR CE GIT POUR CORRECTION, LE CODE PEUT DONC CONTENIR DES ERREURS⚠️

# Tp02-Services-processus-signaux

## 1.1 Connexion ssh root

L'élément de la configuration SSH qu'il a été nécessaire de changer a été le PermitRootLogin et nous avons mis l'option yes, qui l'autorise simplement. Cependant, il y a aussi l'option prohibit-password qui désactive la connexion de root et les interactions entre root et le clavier : cela
empêche donc tout usage de root pour se connecter à la machine. Pour finir, il y a l'option forced-
commands-only, qui permet de se connecter avec root uniquement via une clé d'authentification et
seulement des commandes choisit et prédéfinis avec le compte root. <br>

L'option "yes" pose un soucis de sécurité. En effet, si quelqu'un réussit à récupérer le mot de passe
(par brut force par exemple) et donc l'accès au compte root, il aura tous les droits et pourras agir sur
tout comme il le souhaite. <br>

L'option "prohibit-password" permet de désactiver l'usage de root car le compte ne peut plus
communiquer avec les entrées clavier. C'est une bonne solution de connexion si plusieurs
utilisateurs utilisent la machine, mais peut poser des soucis en cas de besoin par l'administrateur du
super utilisateur. <br>
L'option "forced-commands-only" est, selon moi, la plus pratique. Elle permet de sécuriser le
compte car on se connecte via une clé (ce qui est beaucoup plus pratique pour les installations de
grande envergure), mais aussi parce qu'elle ne désactive par son utilisation mais la limite seulement
aux commandes voulue. Cela permet donc, si besoin, d'adapter le compte root à nos besoins sans
pour autant prendre des risques de sécurité.<br>

### 1.2 Authentification par clef / Génération de clefs
C'est une mauvaise idée de ne pas mettre de passphrase dans un cas réel, car le passphrase peut être
vu comme le mot de passe de la clé privée. En effet, si on en met un, la clé privée ne peut plus être
accessible sans connaître le passphrase. Il est donc dangereux de ne pas en mettre, car n'importe qui
ayant accès au poste pourrait subtiliser la clé privée.

### 1.5 Sécuriser
La procédure pour sécuriser l'accès à la machine par clé uniquement est de modifier comme dans le
TP1 le fichier sshd_config, afin de mettre la ligne "PasswordAuthentication" et de lui mettre
l'option "no". Ainsi, l'accès via mot de passe est interdit et on ne peut y accéder que via clé ssh. Les
attaques brute-force-ssh permettent de trouver un mot de passe via des bibliothèques de milliers de
mots de passe. On les teste tous 1 à 1 et on finit, si le mot de passe est trop simple, par le trouver et
donc avoir accès à la machine.<br>

L’administrateur du serveur peut se protéger de ce type d'attaque en mettant en place un pare-feu
avec une whitelist dans laquelle on ne mettra que les machines / réseaux ayant l'autorisation de se
connecter. L'avantage est que cela filtre de manière radicale, mais cela ne fonctionne que sur les
réseaux internes, sur lesquels il n'est pas nécessaire de communiquer avec l’extérieur.
La solution Fail2Ban existe aussi : c'est un logiciel qui surveille le nombre d’exécutions de
commandes choisies (ici on mettrait donc les commandes utilisées en force brut). S'il détecte que la
commande est utilisée trop de fois, alors le logiciel bloque l'IP d'où proviennent les commandes
dans le pare-feu. <br>

L'option visant à limiter la fréquence des nouvelles connexions peut aussi être une solution. En
effet, cette option permet de limiter la fréquence de tentative des mots de passe. Le problème est
cependant que cela bloque aussi les utilisateurs lambda.

## 2.1 Étude des processus UNIX
1)
La commande ps -eo user,pid,%cpu,%mem,stat,start,time,command | head -n5 permet d'afficher
ps avec les colonnes dans l'ordre spécifié de l’énoncé du tp. Le head -n5 permet lui de n'afficher que
les cinq premières lignes. <br>

L'information TIME correspond au temps CPU cumulé au format [JJ-]HH:MM:SS, c'est-à-dire le
temps écoulé depuis le lancement du processus.<br>

La commande ps x -o %cpu,cmd permet de trouver les processus courant ainsi que leurs
pourcentages d'usage sur le cpu. Ici, celui qui utilise le plus le CPU est la dernière commande que
j'ai effectué : elle l'utilise à 200%.<br>

Le premier processus lancé à été le processus systemd : c'est également dit dans son man qu'il se
lance en premier après le boot.<br>

Avec la commande uptime, je peux voir l'heure actuelle et ainsi la durée écoulée depuis le
démarrage de ma machine. Ma machine est donc allumée depuis 1 heure 46 minutes. Avec la
commande ps -f, je peux voir que bash a été démarré à 14h10. Avec la commande cat /proc/stat je
lis le fichier stat, ainsi je peux récupérer le "btime" et avec la commande date -d @monbtime je
peux voir que ma machine a été lancé à 14h07 et 48 secondes.<br>

Grâce à la commande ps -edf, on peut voir toute la liste des processus lancés depuis le début de la
machine. Certains n'apparaissent pas car ils ont été stoppé, ici 705 processus au total.<br>

2)
La commande ps -l permet d'afficher le PPID de chaque processus.
Les processus ancêtres de la commande ps en cours d’exécution est bash tout seul. On le trouve
grâce à le commande __ps -edf | grep "le ppid de processus".<br>

3)
Les processus ancêtres de la commande pstree en cours d’exécution est systemd > sshd > bash > ps.<br>

4)
La touche "f" permet d'afficher un résumé de l'aide de top.<br>

Le processus le plus gourmand de la machine, est la commande top quand on la lance, mais sinon
c'est le processus systemd, car c'est le processus de base du système linux.<br>

Afin de trier les colonnes de top, il faut faire appuyer sur les touches shift+p afin de trier les
processus par le %CPU, shif+n quand on trie sur le PID, shift+m sur la mémoire et pour finir shift+t
pour trier sur le temps d'usage cpu. Pour modifier le couleur, il suffit d'appuyer sur la touche "z"
mais si on veut d'autres couleurs que le rouge (couleur par défaut de z), il faut faire shift+z et enfin
appuyer sur une touche allant de 0 à 7 afin de choisir. Si on appuie sur "x", la colonne de tri actuelle
sera mise en évidence en gras.<br>

Htop vient avec une interface par défaut plus lisible, ainsi qu'une option pour afficher les arbres de
parenté de chaque processus (en appuyant sur F5). Cependant l'inconvénient de htop est qu'il faut
l'installer en plus par rapport à top qui vient avec les packages de base de linux.<br>

## 3 Arrêt d'un processus
Avec la commande ps je peux voir que suite au CTRL+Z mes scripts sont encore en cours en arrière
plan. Je peux donc les forcer à se stopper en faisant la commande kill -9 "PID de mon processus".
Ainsi avec l'option -9 je force, quoi qu'il arrive, ma machine à stopper mon processus.<br>

Le premier script permet d'exprimer comme ça en langage courant : "Tant que c'est vrai, tu fais la
commande sleep '' (la commande sleep permet d'effectuer une pause du nombre de seconde
transmit en paramètres, ici 1), on affiche la date (la commande date permet d'afficher la date du
jour), mais l'option +%T permet de dire que l'on veut l'heure au format Heure, Minute, Seconde.<br>

L'option -n au début de la commande permet de lui dire d'éviter les sauts de ligne.<br>

Le second script lui demande toute les 1 seconde,d'éviter les sauts de ligne, d'écrire "toto", et de
donner l'heure qu'il était il y a 5 heures dans le passé, par rapport à l'heure actuelle. Ici, ce qui
change est surtout l'option --date qui permet "d'afficher la date indiquée dans la chaîne plutôt que la
date actuelle".<br>

man permet de concaténer des fichiers et de les afficher sur la sortie standard, alors que tee permet
de lire depuis l'entrée standard et écrire sur la sortie standard et dans des fichiers.<br>

La commande ls | cat permet d'afficher tous les fichiers que j'ai dans mon répertoire.<br>

La commande ls -l | cat > liste me permet de rediriger le résultat de la commande ls -l dans un
fichier liste, alors que la commande ls -l | tee liste le fait aussi tout en m'affichant le fichier liste.
La commande ls -l | tee liste | wc -l me permet d'afficher le nombre de lignes que j'ai dans ma
commande ls -l sans m'afficher les fichiers.<br>

Cron est une application qui permet de lancer des scripts de manière régulière et on peut donc, grâce
à cron, lancer des scripts de manière répétée à certains moments.<br>

tail -f permet d'écouter en temps réel un fichier, et donc de voir en temps réel ce qui y est ajouté.
Dans le cas de fichiers log par exemple, cela peut être intéressant.<br>

Le fichier /etc/logrotate.conf permet de limiter la taille des fichiers journaux en effectuant une
rotation. Il compresse les anciens et supprime les plus vieux pour optimiser l'espace de stockage.<br>

La commande dsmeg sert à renvoyer les logs des drivers de chaque composant de l'ordinateur.<br>

Avec la commande dsmeg | grep 'CPU' renvoie les logs du cpu, et on peut donc voir que notre cpu est un
Intel i7 12700 de 12e génération. Ensuite, avec la commande dsmeg | grep 'eth0' on nous donne
les logs contenant la chaîne de caractère 'eth0' : cela correspond au port Ethernet 0 et donc au driver
de la carte réseau. On peut donc voir que la carte réseau est une Intel(R) PRO/1000 Network.
