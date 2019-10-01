# TP 4 - Utilisateurs, groupes et permissions

## Exercice 1. Gestion des utilisateurs et des groupes

* Commencez par créer deux groupes groupe1 et groupe2**

```
serveur@serveur:~$ sudo addgroup groupe1
Adding group `groupe1' (GID 1001) ...
Done.
serveur@serveur:~$ sudo addgroup groupe2
Adding group `groupe2' (GID 1002) ...
Done.
serveur@serveur:~$
```

* Créez ensuite 4 utilisateurs u1, u2, u3, u4 avec la commande useradd, en demandant la création de
leur dossier personnel et avec bash pour shell

Je vous passe les interractions avec l'invite de commande pour définir le nom, numéro de salle, téléphone, etc.

```
 sudo adduser u1 --home /home/u1 --shell $SHELL
 sudo adduser u2 --home /home/u2 --shell $SHELL
 sudo adduser u3 --home /home/u3 --shell $SHELL
 sudo adduser u4 --home /home/u4 --shell $SHELL
```

* Placez les utilisateurs dans les groupes :

  - u1, u2, u4 dans groupe1
  
```  
serveur@serveur:~$ sudo usermod -a -G groupe1 u1
serveur@serveur:~$ sudo usermod -a -G groupe1 u2
serveur@serveur:~$ sudo usermod -a -G groupe1 u4
```

  - u2, u3, u4 dans groupe2

```
serveur@serveur:~$ sudo usermod -a -G groupe2 u2
serveur@serveur:~$ sudo usermod -a -G groupe2 u3
serveur@serveur:~$ sudo usermod -a -G groupe2 u4
```

* Donnez deux moyens d’afficher les membres de groupe2

```
serveur@serveur:~$ grep "groupe2" /etc/group
groupe2:x:1002:u2,u3,u4
```

ou

```
serveur@serveur:~$ getent group groupe1
groupe1:x:1001:u1,u2,u4
```


* Faites de groupe1 le groupe propriétaire de /home/u1 et /home/u2 et de groupe2 le groupe propriétaire de /home/u3 et /home/u4

```
serveur@serveur:/home$ sudo chgrp groupe1 u1 u2
serveur@serveur:/home$ sudo chgrp groupe2 u3 u4
serveur@serveur:/home$ ls -l
total 20
drwxr-xr-x 9 serveur serveur 4096 sept. 23 16:43 serveur
drwxr-xr-x 2 u1      groupe1 4096 sept. 30 14:06 u1
drwxr-xr-x 2 u2      groupe1 4096 sept. 30 14:11 u2
drwxr-xr-x 2 u3      u3      4096 sept. 30 14:12 u3
drwxr-xr-x 2 u4      u4      4096 sept. 30 14:12 u4
```

* Remplacez le groupe primaire des utilisateurs :

  - groupe1 pour u1 et u2
  
  ```
  serveur@serveur:~$ sudo usermod u1 -g groupe1
  serveur@serveur:~$ sudo usermod u2 -g groupe1
  ```
  
  - groupe2 pour u3 et u4
  ```
  serveur@serveur:~$ sudo usermod u3 -g groupe2
  serveur@serveur:~$ sudo usermod u4 -g groupe2
  ```

* Créez deux répertoires /home/groupe1 et /home/groupe2 pour le contenu commun aux groupes, et
mettez en place les permissions permettant aux membres de chaque groupe d’écrire dans le dossier
associé.

```
serveur@serveur:/home$ sudo mkdir groupe1
serveur@serveur:/home$ sudo mkdir groupe2
serveur@serveur:/home$ sudo chgrp groupe1 groupe1
serveur@serveur:/home$ sudo chgrp groupe2 groupe2
serveur@serveur:/home$ ls -l
total 28
drwxr-xr-x 2 root    groupe1 4096 sept. 30 14:39 groupe1
drwxr-xr-x 2 root    groupe2 4096 sept. 30 14:39 groupe2
serveur@serveur:/home$ sudo chmod g+w groupe1
serveur@serveur:/home$ sudo chmod g+w groupe2
serveur@serveur:/home$ ls -l
total 28
drwxrwxr-x 2 root    groupe1 4096 sept. 30 14:39 groupe1
drwxrwxr-x 2 root    groupe2 4096 sept. 30 14:39 groupe2
```

* Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer ou supprimer ce fichier ?

Pour cela il est nécessaire d'ajouter  un sticky bit au droits du dossier. Cela est faisable via les commandes:

```
serveur@serveur:/home$ sudo chmod +t groupe1
serveur@serveur:/home$ sudo chmod +t groupe2
```

* Pouvez-vous vous connecter en tant que u1 ? Pourquoi ?

Non, il n'est pas possible de ce connecter en tant que u1 car celui-ci n'as pas de mot de passe et est donc considérer comme un compte désactivé. Ont ne peut donc pas s'y connecter.

* Activez le compte de l’utilisateur u1 et vérifiez que vous pouvez désormais vous connecter avec son
compte.

```
serveur@serveur:/home$ sudo passwd u1
New password:
Retype new password:
passwd: password updated successfully
serveur@serveur:/home$ su u1
Password:
u1@serveur:/home$
```

* Quels sont l’uid et le gid de u1 ?

```
u1@serveur:/home$ id
uid=1001(u1) gid=1001(groupe1) groups=1001(groupe1)
```
u1 à pour uid 1001 et gid 1001.

* Quel utilisateur a pour uid 1003 ?

```
u1@serveur:/home$ id 1003
uid=1003(u3) gid=1002(groupe2) groups=1002(groupe2)
```

l'utilisateur qui à pour uid 1003 et l'utilisateur u3.

* Quel est l’id du groupe groupe1 ?

```
u1@serveur:/home$ getent group groupe1
groupe1:x:1001:u1,u2,u4
```

L'id du groupe 1 est le 1001.

* Quel groupe a pour guid 1002 ? ( Rien n’empêche d’avoir un groupe dont le nom serait 1002...)

```
u1@serveur:/home$ getent group 1002
groupe2:x:1002:u2,u3,u4
```
le groupe qui à le guid 1002 est le groupe2.

* Retirez l’utilisateur u3 du groupe groupe2. Que se passe-t-il ? Expliquez.

* Modifiez le compte de u4 de sorte que :
  — il expire au 1er juin 2019
  — il faut changer de mot de passe avant 90 jours
  — il faut attendre 5 jours pour modifier un mot de passe
  — l’utilisateur est averti 14 jours avant l’expiration de son mot de passe
  — le compte sera bloqué 30 jours après expiration du mot de passe

```
serveur@serveur:/home$ sudo chage -E 2019-06-01 u4
serveur@serveur:/home$ sudo chage -M 90 u4
serveur@serveur:/home$ sudo chage -m 5 u4
serveur@serveur:/home$ sudo chage -W 14 u4
serveur@serveur:/home$ sudo chage -l u4
Last password change                                    : sept. 30, 2019
Password expires                                        : déc. 29, 2019
Password inactive                                       : janv. 28, 2020
Account expires                                         : juin 01, 2019
Minimum number of days between password change          : 5
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 14
```

* Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?

```
serveur@serveur:~$ getent passwd root
root:x:0:0:root:/root:/bin/bash
```
l'interpréteur de commandes de l'utilisateur rout est /bin/bash.

* à quoi correspond l’utilisateur nobody ?

L'utilisateur nobody est utilisé par tout programme qui ne nécessite pas de permission particulière. Cela est particulièrement utile dans le cas de service qui peuvent être compromis (httpd par exemple). Cela permet de limiter les dégats.

 * Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ?
Quelle commande permet de forcer sudo à oublier votre mot de passe ?
 minutes par défaut.
 
Selon le manuel de la commande sudo, celle-ci conserve le mot de passe en mémoire pendant 15
for 15 minutes. Cela peux être réinitialiser avec la commande:

```
serveur@serveur:~$ sudo -K
```

## Exercice 2. Gestion des permissions

* Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques
lignes de texte. Quels sont les droits sur test et fichier ?

```
serveur@serveur:~$ ll
drwxrwxr-x  2 serveur serveur    4096 oct.   1 11:56 test/
```

Test possèdes les droits suivants:
 - Utilisateur: Lecture, Écriture, Exécution
 - Groupe: Lecture, Écriture, Exécution
 - Autres: Lecture, Exécution

```
serveur@serveur:~/test$ ll
-rw-rw-r--  1 serveur serveur   48 oct.   1 11:56 fichier
```

pour le fichier fichier, celui-ci possède les droits suivants:
 - Utilisateur: Lecture, Écriture
 - Groupe: Lecture, Écriture
 - Autres: Lecture

* Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en tant que root. Conclusion ?

```
serveur@serveur:~/test$ sudo chmod 000 fichier
serveur@serveur:~/test$ sudo cat fichier
un super fichier
avec quelques
lignes de textes
serveur@serveur:~/test$ sudo nano fichier
serveur@serveur:~/test$ sudo cat fichier
un super fichier
avec quelques
lignes de textes
et une de plus !
```
Les droits d'accès configuré ne s'applique pas au super utilisateur root.

* Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo
Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un
fichier s’il existe déjà. Que peut-on dire au sujet des droits ?

```
serveur@serveur:~/test$ sudo chmod 300 fichier
serveur@serveur:~/test$ echo "echo Hello" > fichier
serveur@serveur:~/test$ cat fichier
cat: fichier: Permission denied
serveur@serveur:~/test$ sudo cat fichier
echo Hello
```
Comme nous possédons le droit d'écriture sur le fichier, il est possible d'écrire dans celui-ci et donc de remplacer son contenu.

* Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez.

```
serveur@serveur:~/test$ ./fichier
bash: ./fichier: Permission denied
serveur@serveur:~/test$ sudo ./fichier
Hello
```
Comme nous n'avons pas les droits d'exécution, le système nous bloque. En revache, comme root à tout les droits, cela ne le bloque pas.

* Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ? Rétablissez le droit en lecture sur test

```
serveur@serveur:~/test$ sudo chmod u-r ../test
serveur@serveur:~/test$ ls
ls: cannot open directory '.': Permission denied
serveur@serveur:~/test$ ./fichier
bash: ./fichier: Permission denied
serveur@serveur:~/test$  
```
Les droits sont testé au fur et à mesure des commandes. Et donc sans le droit de l'ecture je ne peux afficher le contenu du dossier.

* Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au
répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvezvous déduire de toutes ces manipulations ?

```
serveur@serveur:~/test$ nano nouveau
serveur@serveur:~/test$ mkdir sstest
serveur@serveur:~/test$ chmod u-w nouveau
serveur@serveur:~/test$ chmod u-w ../test
serveur@serveur:~/test$ nano nouveau
[ Error writing nouveau: Permission denied ]
serveur@serveur:~/test$ chmod u+w ../test
serveur@serveur:~/test$ nano nouveau
[ Error writing nouveau: Permission denied ] 
serveur@serveur:~/test$ rm nouveau
rm: remove write-protected regular file 'nouveau'? yes
serveur@serveur:~/test$ ls
fichier  sstest
```
Les fichiers n'hérite pas des droits d'un dossier, ils ont chacun des droits spécifique.

* Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test. Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?

```
serveur@serveur:~$ chmod u-x test
serveur@serveur:~$ nano test/nouveau
[ Path 'test' is not accessible ]
serveur@serveur:~$ cd test/
-bash: cd: test/: Permission denied
serveur@serveur:~$ ls test
ls: cannot access 'test/sstest': Permission denied
ls: cannot access 'test/fichier': Permission denied
fichier  sstest
```

Si une commande nécessite d'accéder au contenu d'un répertoir alors que le droit de lecture lui est retiré, il ne sera pas possible d'exécuter la commande.

* Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd ..” ? Pouvez-vous donner une explication ?

```
serveur@serveur:~$ cd test/
serveur@serveur:~/test$ chmod u-x ../test
serveur@serveur:~/test$ nano nouveau
[ Path '.': Permission denied ]
serveur@serveur:~/test$ cd sstest
-bash: cd: sstest: Permission denied
serveur@serveur:~/test$ ls
ls: cannot open directory '.': Permission denied
serveur@serveur:~/test$ cd ..
serveur@serveur:~$ 
```

les droits du répertoir courant sont aussi important que si l'on essayais de faire les mêmes opérations depuis un répertoir parent. Comme pour la question précédente, les droits du dossier sont pris en compte pour chaque commande nécessitant d'utiliser le contenu d'un dossier.

* Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suffisants pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.

```
serveur@serveur:~/test$ chmod 650 fichier
serveur@serveur:~/test$ ll
total 16
drwxr-xr-x  3 serveur serveur 4096 oct.   1 12:30 ./
drwxr-xr-x 10 serveur serveur 4096 oct.   1 11:56 ../
-rw-r-x---  1 serveur serveur   11 oct.   1 12:12 fichier*
drwxrwxr-x  2 serveur serveur 4096 oct.   1 12:21 sstest/
```

* Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture, ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.

```
serveur@serveur:~$ touch fichier
serveur@serveur:~$ mkdir folder
serveur@serveur:~$ umask 177
serveur@serveur:~$ ll
total 3728
drwxr-xr-x 10 serveur serveur    4096 oct.   1 12:47 ./
drwxr-xr-x  9 root    root       4096 sept. 30 14:39 ../
-rw-------  1 serveur serveur       0 oct.   1 12:47 fichier
drw-------  2 serveur serveur    4096 oct.   1 12:47 folder/
```

* Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires, mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire.

```
serveur@serveur:~$ umask 011
serveur@serveur:~$ ll
total 3728
drwxr-xr-x 10 serveur serveur    4096 oct.   1 12:47 ./
drwxr-xr-x  9 root    root       4096 sept. 30 14:39 ../
-rwxr-xr-x  1 serveur serveur       0 oct.   1 12:47 fichier*
drwxr-xr-x  2 serveur serveur    4096 oct.   1 12:47 folder/
serveur@serveur:~$  
```

* Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.

```
serveur@serveur:~$ umask 037
serveur@serveur:~$ ll
total 3728
drwxr-xr-x 10 serveur serveur    4096 oct.   1 12:47 ./
drwxr-xr-x  9 root    root       4096 sept. 30 14:39 ../
-rwxr-----  1 serveur serveur       0 oct.   1 12:47 fichier*
drwxr-----  2 serveur serveur    4096 oct.   1 12:47 folder/
serveur@serveur:~$  
```

* Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous pourrez vous aider de la commande stat pour valider vos réponses) :
  - chmod u=rx,g=wx,o=r fic `534`
  - chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x--- `501`
  - chmod 653 fic en sachant que les droits initiaux de fic sont 711 `Hein ?`
  - chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x--- `520`

* Affichez les droits sur le programme passwd. Que remarquez-vous ? En affichant les droits du fichier /etc/passwd, pouvez-vous justifier les permissions sur le programme passwd ?

```
serveur@serveur:~/folder$ ll /etc/passwd
-rw-r--r-- 1 root root 1801 sept. 30 15:08 /etc/passwd
```
