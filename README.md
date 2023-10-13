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
L'information TIME correspond au temps CPU cumulé au format [JJ-]HH:MM:SS, c'est à dire le temps écoulé depuis le lancement du processus. </br>
Les 2 processus (bash et ps), étant à TIME : 00:00:00, aucun n'a utilisé le processeur plus que l'autre.</br>
Le premier processus lancé à été le processus Bash après le démarrage du système.</br>
Avec la commande __uptime__, je peux voir l'heure actuel, ainsi la durée écoulé depuis le démarrage de ma machine. Ma machine est donc allumé depuis 1 heure 46 minutes. Avec la commande __ps -f__ je peux voir que bash à été démarré à 14h10, la machine a donc été démarré à 14h10. </br>
Grâce à la commande __ps -edf | wc -l__ on peut voir que 156 processus on été lancé depuis le boot de la machine.

2)</br>
La commande __ps -l__ permet d'afficher le PPID de chaque processus. </br>
Les processus ancêtres de la commande ps en cours d'éxécution est bash tout seul, on le trouve grâce à le commmande __ps -edf | grep "le ppid de processus".</br>

3)</br>
Les processus ancêtres de la commande pstree en cours d'éxécution est systemd > sshd > bash > ps.</br>

4)</br>
La touche "f" permet d'afficher un résumé de l'aide de top.</br>


