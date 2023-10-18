# Tp02-Services-processus-signaux

## 1.1 Connection ssh root

L'élément de la configuration SSH qu'il a été nécéssaire de changer à été le __PermitRootLogin__, nous avons mis l'option yes, qui l'autorise simplement. Cependant il y a aussi l'option prohibit-password qui désactive la connexion de root et les interactions entre root et le clavier, cela empêche donc tout usage de root pour se connecter à la machine. Pour finir, il y a l'option forced-commands-only, cette option permet de se connecter avec root uniquement via une clé d'authentification, et seulement des commandes choisit et prédefinis avec le compte root. </br>
L'option "yes" pose un soucis de sécurité, en effet si quelqu'un réussit à récuperer le mot de passe (par brut force par exemple) et donc l'accès au compte root, il aura tous les droits et pourras agir sur tout comme il le souhaite. </br>
L'option "prohibit-password" permet de désactiver l'usage de root car le compte ne peut plus communiquer avec les entrers clavier. C'est une bonne solution de connection si plusieurs utilisateurs utilise la machine, mais peut poser des soucis en cas de besoin par l'administrateur du super utilisateur. </br>
L'option "forced-commands-only" est selon moi la plus pratique, elle permet de sécurisé le compte car on se connecte via une clé (ce qui est beaucoup plus pratique pour les installations de grande envergure), mais aussi parce que elle ne désactive par son utilisation mais la limite seulement aux commandes voulue, cela permet donc si besoin d'adapter le compte root à nos besoins sans pour autant prendre des risques de sécurité. </br>

## 1.2 Authentification par clef / Génération de clefs

C'est une mauvaise idée de ne pas mettre de passphrase dans un cas réel car le passphrase peut être vue comme le mot de passe de la clé privé, en effet si on en met un, la clé privé ne peut plus accessible sans connaître le passphrase. Il est donc dangereux de ne pas en mettre, car n'importe qui aillant accès au poste pourrais subtiliser la clé privé.</br>

## 1.5 Sécurisez

La procédure pour sécurisé l'accés à la machine par clé uniquement, est de modifier comme dans le TP1 le fichier sshd_config, afin de mettre la ligne "PasswordAuthentication" et de lui mettre l'option "no". Ainsi l'accès via mot de passe est interdit et on ne peut y accéder que via clé ssh. Les attaques brute-force-ssh est une attaque qui permet de trouver un mot de passe via des bibliothèque de milliers de mot de passe, on les test tous 1 à 1 et on finis, si le mot de passe est trop simple, par le trouver et donc avoir accès à la machine.

L'adminstrateur du serveur peut se protéger de ce type d'attaque en mettant en place un par feu avec une whitelist dans laquel on ne mettra que les machines / réseaux aillant l'autorisation de connecter, l'avantage est que cela filtre de manière radical, mais cela ne fonctionne que sur les réseaux interne, sur lesquels il n'est pas nécéssaire de communiquer avec l'exterieur.</br>
La solution Fail2Ban existe aussi, c'est un logiciel qui surveille le nombre d'éxécution de commandes choisit (ici on mettrait donc les commande utiliser en force brut), s'il detecte que la commande est utilisé trop de fois alors le logiciel bloque l'IP d'où provient les commandes dans le par feu.</br> 
L'option visant à limiter la fréquence des nouvelles connexions peut aussi être une solution, en effet cette option permet de limiter la fréquence de tentative des mots de passe, le problème est cependant que cela bloque aussi les utilisateurs lambda.</br>

## 2.1 Etude des processus UNIX 

1)</br>
__ps -eo user,pid,%cpu,%mem,stat,start,time,command | head -n5__, cette commande permet d'afficher ps avec les colonnes dans l'ordre spécifié de l'énnoncé du tp, le head -n5 permet lui de n'afficher que les 5 première ligne.</br>
L'information TIME correspond au temps CPU cumulé au format [JJ-]HH:MM:SS, c'est à dire le temps écoulé depuis le lancement du processus. </br>
__ps x -o %cpu,cmd__: Cette commande permet de trouver les processus courant, ainsi que leurs pourcentage d'usage sur le cpu, ici celui qui utilise le plus le CPU est la dernière commande que j'ai effectué, elle l'utilise à 200%.</br>
Le premier processus lancé à été le processus systemd, c'est même dit dans son man, qu'il se lance en premier après le boot.</br>
Avec la commande __uptime__, je peux voir l'heure actuel, ainsi la durée écoulé depuis le démarrage de ma machine. Ma machine est donc allumé depuis 1 heure 46 minutes. Avec la commande __ps -f__ je peux voir que bash à été démarré à 14h10. Avec la commande __cat /proc/stat__ je lis le fichier stat, ainsi je peux récuperer le "btime" et avec la commande __date -d @monbtime__ je peux voir que ma machine a été lancé à 14h07 et 48 secondes.</br>
Grâce à la commande __ps -edf__ on peut voir toute la liste des processus lancés depuis le début de la machine, certains n'apparaissent pas car ils ont été stoppés, ici 705 processus au total.</br>

2)</br>
La commande __ps -l__ permet d'afficher le PPID de chaque processus. </br>
Les processus ancêtres de la commande ps en cours d'éxécution est bash tout seul, on le trouve grâce à le commmande __ps -edf | grep "le ppid de processus".</br>

3)</br>
Les processus ancêtres de la commande pstree en cours d'éxécution est systemd > sshd > bash > ps.</br>

4)</br>
La touche "f" permet d'afficher un résumé de l'aide de top.</br>
Le processus le plus gourmand de la machine, est la commande __top__ quand on la lance, mais sinon, c'est le processus systemd, car c'est le processus de base du systeme linux.</br>
Afin de trier les colonnes de __top__, il faut faire appuyer sur les touches shift+p afin de trier les processus par le %CPU, shif+n quand on trie sur le PID, shift+m sur la mémoire et pour finir shift+t pour trier sur le temps d'usage cpu. Pour modifier le couleur il suffit d'appuyer sur la touche "z" mais si on veut d'autres couleurs que le rouge (couleur par défault de z), il faut faire shift+z et enfin appuyer sur une touche allant de 0 à 7 afin de choisir d'autres couleurs. Si on appuie sur "x", la colonne de tri actuel sera mise en évidence, en gras.</br>
__Htop__ viens avec une interface par défault plus lisible, ainsi qu'une option pour afficher les arbres de parentés de chaque processus (en appuyant sur F5), cependant l'inconvénient de __htop__, est qu'il faut l'installé en plus par rapport à __top__ qui viens avec les packages de base de linux.

## 3 Arrêt d'un processus

Avec la commande __ps__ je peux voir que suite au CTRL+Z mes scritps sont encore en cours en arrière plan, je peux donc les forcer à se stopper en faisant la commande __kill -9 "PID de mon processus"__ ainsi avec l'option __-9__ je force quoi qu'il arrive, ma machine à stopper mon processus.</br>
Le premier script permet s'exprimerais comme ça en langage courant : "Tant que c'est vrai, tu fais la commande __sleep__ (la commande __sleep__ permet d'effectuer une pause du nombre de seconde transmit en paramètres, ici 1), tu affiche la date (la commande __date__ permet d'afficher la date du jour), mais l'option __+%T__ permet de dire que l'on veut l'heure au format Heure, Minute, Seconde. L'option __-n__ au début de la commande permet de lui dire d'éviter les sauts de ligne.</br>
Le second script demande lui toute les 1 seconde, d'éviter les sauts de ligne, d'écrire "toto", et de donner l'heure qu'il était il y a 5 heures dans le passé, par rapport à l'heure actuel. Ici ce qui change est surtout l'option __--date__ qui permet "d'afficher la date indiquée dans  la chaîne plutot que la date actuelle".</br>
__man__ permet de concaténer des fichiers et de les afficher sur la sortie standard, alors que __tee__ permet de lire depuis l'entrée standard et écrire sur la sortie standard et dans des fichiers.</br>
La commande __ls | cat__ permet d'afficher tous les fichier que j'ai dans mon répertoire.</br>
La commande __ls -l | cat > liste__ me permet de rediriger le résultat de la commande __ls -l__ dans un fichier liste, alors que la commande __ls -l | tee liste__ le fait aussi tout en m'affichant le fichier liste.</br>
La commande __ls -l | tee liste | wc -l__ me permet d'afficher le nombre de ligne que jai dans ma commande __ls -l__ sans m'afficher les fichiers.</br>
Cron est une application qui permet de lancer des scripts de manière régulière, on peut donc, grâce à __cron__ lancé des scripts de manière répété à certains moments.</br>
__tail -f__ Permet d'écouter en temps réel un fihier, et donc de voir en temps réel ce qui y est ajouté, dans le cas de fichiers log par exemple cela peut être intéréssant.</br>
Le fichier __/etc/logrotate.conf__ permet de limiter la taille des fichiers journaux, en effectuant une rotation, il compresse les anciens, et supprime les plus vieux pour optimiser l'espace de stockage.</br>
La commande __dsmeg__ sert à renvoyer les logs des drivers de chaque composant de l'ordinateur. Avec la commande __dsmeg  | grep 'CPU'__ renvoie les logs du cpu, et on peut donc voir que notre cpu est un Intel i7 12700 de 12e génération. Ensuite, avec la commande __dsmeg | grep 'eth0'__ nous donne les logs contenant la chaine de caractère 'eth0', cela correspond au port ethernet 0 et donc au driver de la carte réseau, on peut donc voir que la carte réseau est une Intel(R) PRO/1000 Network.</br>




