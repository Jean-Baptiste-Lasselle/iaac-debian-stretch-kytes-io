# iaac-debian-stretch-kytes-io
Pour l'organisation kytes.io, l'initialisation du cycle iaac sur debian stretch, typiquement un poste opérateur

(Kytes utilise Kytes, pour Kytes...)

Un repo qui documente la recette "pre-ssh", à appliquer sur un hôte Debian Stretch fournit par le niveau IAAS, avant d'exécuter des recettes de cuisine Kytes 

# Problématique juste après installation : les repository de package manager

## PB 1 à régler :  le repo référencé par le  "CD ROM"

J'ai:
* installé Debian Stretch à partir de ma clé USB bootable,
* retiré la cleé USB bootale de son slot hardware
* redémarré la machine, entrer dans le BIOS, dchanger l'ordre de Boot, pour qu'il se fasse sur le disque sélectionné pdt l'installation Debian, et re-démrrer pour entrer dans Debian
* J'ai installé git, ça a fonctionné
* J'ai configuré le backport debian stretch pour `apt` avec le petit script : 
```
export FICHIER_CONF=/etc/apt/sources.list.d/stretch-backports-repo.list
export FICHIER_TEMP=$HOME/etc.apt.sources.list.d.stretch-backports-repo.list
rm -f $FICHIER_TEMP
touch $FICHIER_TEMP
echo "deb http://ftp.debian.org/debian stretch-backports main" >> $FICHIER_TEMP
# on créée, s'il n'existe pas, le répertoire dans lequel devra être placé le fichert de configuration $FICHIER_CONF
sudo mkdir -p /etc/apt/sources.list.d/
# on backup le precedent, 
sudo cp -f $FICHIER_CONF "$FICHIER_CONF".previous
# pour le remplacer par le nouveau
sudo rm -f $FICHIER_CONF
sudo cp -f $FICHIER_TEMP $FICHIER_CONF
# Et on rétablit les droits de propriété légitimes du système
sudo chmod a-r $FICHIER_CONF
sudo chmod a-x $FICHIER_CONF
sudo chmod a-w $FICHIER_CONF
# pour moi, le système doit tolérer qu'un utilisateur consulte ce fichier en lecture seule.
sudo chmod a+r $FICHIER_CONF
sudo chmod o+w $FICHIER_CONF

sudo chown root:root $FICHIER_CONF

# puis installation virtualbox
sudo apt-get install -y virtualbox
```
* et j'ai fait un `apt-get update -y`. J'en arrvie alors à ma première remarquie, à savoir que j'ai des indications dans la sortie standard d'APT, qui indiquent qu'un problème est survenu avec un repository, celui référencé comme étant le local de la clé USB, en fait. Et il y a une indication expliqunt que je devrais metttre en oeuvre mon propre repository APT, , etc... Je vuex fai re le lien avec le kickstart clairement, pour remplacer automatiquement, cette entrée de repository, par une entrée d'un repository que j'aurai provisionné en private. La stdout d'APT, dans cet état précis : 

```
Ign:1 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch InRelease
Ign:2 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch Release
Ign:3 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all Packages
Hit:4 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 Packages
Ign:5 http://ftp.fr.debian.org/debian stretch InRelease                                             
Hit:6 http://security.debian.org/debian-security stretch/updates InRelease                          
Ign:4 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 Packages
Ign:7 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en_US
Hit:8 http://ftp.fr.debian.org/debian stretch-updates InRelease
Hit:9 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en
Ign:10 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all DEP-11 Metadata
Ign:9 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en                              
Ign:11 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 DEP-11 Metadata                      
Ign:12 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main DEP-11 64x64 Icons
Ign:3 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all Packages
Hit:4 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 Packages
Ign:7 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en_US
Ign:4 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 Packages                        
Ign:10 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all DEP-11 Metadata                  
Hit:9 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en
Ign:11 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 DEP-11 Metadata
Ign:9 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en
Ign:12 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main DEP-11 64x64 Icons
Ign:3 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all Packages
Ign:7 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en_US
Hit:4 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 Packages
Ign:10 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all DEP-11 Metadata
Ign:4 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 Packages
Ign:11 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 DEP-11 Metadata
Hit:9 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en
Ign:12 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main DEP-11 64x64 Icons
Ign:9 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en
Ign:3 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all Packages
Ign:7 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en_US
Ign:10 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all DEP-11 Metadata
Hit:4 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 Packages
Ign:11 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 DEP-11 Metadata
Ign:12 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main DEP-11 64x64 Icons
Hit:9 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en
Ign:3 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all Packages
Ign:7 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en_US
Ign:10 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all DEP-11 Metadata
Ign:11 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 DEP-11 Metadata
Hit:13 http://ftp.fr.debian.org/debian stretch Release                                                              
Ign:12 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main DEP-11 64x64 Icons
Ign:3 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all Packages
Ign:7 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main Translation-en_US
Ign:10 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main all DEP-11 Metadata
Ign:11 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main amd64 DEP-11 Metadata
Ign:12 cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch/main DEP-11 64x64 Icons
Get:14 http://ftp.debian.org/debian stretch-backports InRelease [91.8 kB]                                                        
Get:16 http://ftp.fr.debian.org/debian stretch/main amd64 DEP-11 Metadata [3,066 kB]             
Get:17 http://ftp.debian.org/debian stretch-backports/main amd64 Packages [397 kB]
Get:18 http://ftp.debian.org/debian stretch-backports/main Translation-en [293 kB]
Get:19 http://ftp.fr.debian.org/debian stretch/main DEP-11 64x64 Icons [6,804 kB]                            
Fetched 10.7 MB in 1s (6,536 kB/s)                                     
Reading package lists... Done
W: The repository 'cdrom://[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25] stretch Release' does not have a Release file.
N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```
## Contenu des fichiers de conf APT juste après conf package manager et install git 

### pour le `/etc/apt/sources.list` 

Avant tout changement de ma part (docn le généré par la procédure d'installation) :  
```
jibl@pc-alienware-jib:~$ cat /etc/apt/sources.list
# 

# deb cdrom:[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25]/ stretch main

deb cdrom:[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25]/ stretch main

deb http://ftp.fr.debian.org/debian/ stretch main
deb-src http://ftp.fr.debian.org/debian/ stretch main

deb http://security.debian.org/debian-security stretch/updates main
deb-src http://security.debian.org/debian-security stretch/updates main

# stretch-updates, previously known as 'volatile'
deb http://ftp.fr.debian.org/debian/ stretch-updates main
deb-src http://ftp.fr.debian.org/debian/ stretch-updates main

```

et j'ai commenté la ligne començant par `deb cdrom: etc...`  : 

```
jibl@pc-alienware-jib:~$ cat /etc/apt/sources.list
# 

# deb cdrom:[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25]/ stretch main

# deb cdrom:[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 xfce-CD Binary-1 20180714-10:25]/ stretch main

deb http://ftp.fr.debian.org/debian/ stretch main
deb-src http://ftp.fr.debian.org/debian/ stretch main

deb http://security.debian.org/debian-security stretch/updates main
deb-src http://security.debian.org/debian-security stretch/updates main

# stretch-updates, previously known as 'volatile'
deb http://ftp.fr.debian.org/debian/ stretch-updates main
deb-src http://ftp.fr.debian.org/debian/ stretch-updates main

```
* Pour le `/etc/apt.conf.d/`


de et juste après l'installation, j'ai, dans le fichier `/etc/apt/sources.list`


# Dernière erreur install virutalbox / debian stretch


```
jibl@pc-alienware-jib:~$ sudo apt-get update -y && sudo apt-get install -y virtualbox
Ign:1 http://ftp.fr.debian.org/debian stretch InRelease
Hit:2 http://ftp.fr.debian.org/debian stretch-updates InRelease                                                              
Hit:3 http://ftp.fr.debian.org/debian stretch Release                                                                                                   
Hit:4 http://ftp.debian.org/debian stretch-backports InRelease                                                                                          
Hit:5 http://security.debian.org/debian-security stretch/updates InRelease
Reading package lists... Done
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package virtualbox is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'virtualbox' has no installation candidate
jibl@pc-alienware-jib:~$ sudo apt-get update -y && sudo apt-get install -y virtual-box
Ign:1 http://ftp.fr.debian.org/debian stretch InRelease
Hit:2 http://security.debian.org/debian-security stretch/updates InRelease                                                                         
Hit:3 http://ftp.fr.debian.org/debian stretch-updates InRelease                                                                                    
Hit:4 http://ftp.fr.debian.org/debian stretch Release                                                                                                   
Hit:5 http://ftp.debian.org/debian stretch-backports InRelease                                                                                          
Reading package lists... Done                                                                    
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package virtual-box

```


# Procédure complète de provision "pre-SSH"

À l'issue de la procédure, on pourra exécuter des recettes telles que:

* https://github.com/Jean-Baptiste-Lasselle/provision-hote-docker-sur-debian-stretch
* https://github.com/Jean-Baptiste-Lasselle/provision-k8s



## Si vous utilisez LVM pour vos disques durs virtuel de VMs

### TODO : LVM boot
j'ai vu que la procédure d'installation de debian stretch propoe un setup de LVM, je pense que (la personne à laquelle je pense) a utilsié cela, pour mettre en oeuvre LVM

TODO 1 : voir le cycle de gestion complet LVM, pour l'utiliser tel que je l'ai utilisé pour le "thin provisionning et les images QCOW2 "

TODO 2 : voir comment provisionner LVM de différentes manoières, par exemple récupérer le kickstart , suit eà une installation Debian avec option LVM cochée, et tester des configurations différentes avec des  post installations scripts de kickstart

TODO 3 ": comment utilsier LVM pour tester en black box l'utilisation disque dur d'un logiciel quelconque (c'est rpatique pour ela, querls plugins peut-o n penser, au niveau des frameworks de tests infra.... ou même de tests Jenkins, dans le pipeline de développement d'une application quelcoque  ...

pipeline de développement, pipeline de livraison....

### Docker, espace disque et LVM

Extension de capacité de disque LVM (afin d'attribuer donner "assez" d'espace disque libre sous `/var`, pour le moteur de conteneurs):

```
# On suppose que la VM fournie au niveau IAAS a été créée avec
# un disque dur virtuel d'une capacité supérieure ou égale à 30Go
sudo lvextend -rn /dev/mapper/vg00-lv_var -L 20G
```



### Si vous utilisez un proxy

Configuration du package manager:

```
# - pour configurer le proxy pour le package manager Debian Stretch 7:
sudo -s
# il faut être "root", sudo ne fonctionnera pas.

export PROXY_SRV_HOST_OR_IP
export PROXY_SRV_IP_PORT_NUMBER
# echo "Acquire::http::proxy \"http://$PROXY_SRV_HOST_OR_IP:$PROXY_SRV_IP_PORT_NUMBER/\" " >> /etc/apt.conf.d/80proxy

echo "   ====>>>>> TODO : recette à compléter !!!  poru la conf auto. des proxy package manager Debian Stretch ... "
echo "   ====>>>>> TODO : rdv  https://github.com/Jean-Baptiste-Lasselle/iaac-debian-stretch-kytes-io/  "

```

### Installation de Git

```
sudo apt-get install -y git
```


### Authentifcation GIT ssh avec paire de clés

Paire de clés asymétriques RSA:

```
ssh-keygen -t rsa -b 4096 && cat $HOME/.ssh/id_rsa.pub
# à copier pour ajouter la clé aux clefs SSH de votre utilisateur Gitlab
```

# Cycles IAAC

### Vocabulaire

* IAAS: Infrastructure As A Service (ex.: OpenStack)
* IAAC: Infrastructure Adressed As Code

## Description du cycle Infrastructure As Code

### Cycle élémentaire (sans mémoire des opérations)

Le but de ce petit paragraphe, est de décrire un cycle élémentaire complet respectant Infrastructure As Code, applicable tel quel, dans l'infrastructure dans laquelle vos machines vivent.

Le niveau IAAS créée une Machine Virtuelle `uneVM` sur laquelle est installé et configuré CentOS, avec 2 interfaces réseaux, connectée à des réseaux distincts (modèle OSI, niveau L2).

On fait usage d'un nom d'utilisateur linux désigné par `$UTILISATEUR`, fournit par l'administrateur du niveau IAAS, 
pour ouvrir une session ssh vers la machine `uneVM`.

On configure le package manager pour le proxy de l'infrastructure:


```
# -
# Pour CentOS, le package manager est yum, et il suffit, en tant que
# root, d'exécuter les commandes ci-dessous pour appliquer la configuration.
# -
$ sudo -i
# echo "proxy=http://proxy:8080">> /etc/yum.conf
# su $**UTILISATEUR**
# -
```
Pour les Ubuntu / Debian, le package manager est apt, et il suffit, en tant que root, d'exécuter les instructions ci-dessous pour appliquer la configuration:
```
$ sudo -i
# echo "Acquire::http::proxy \"http://proxy:8080/\";">> /etc/apt/apt.conf
# echo "Acquire::https::proxy \"http://proxy:8080/\";">> /etc/apt/apt.conf
# su $UTILISATEUR
```
On installe git dans la machine uneVM :

CentOS:
```
$ sudo yum install -y git
```
Debian / Ubuntu:
```
$ sudo apt-get install -y git
```
On génère une paire de clés asymétriques, sur `uneVM` , pour l'utilisateur `$UTILISATEUR` :
```
$ ssh-keygen -t rsa -b 4096
$ cat $HOME/.ssh/id_rsa.pub
```
On utilisera un repository Git dans l'instance Gitlab, afin de versionner une recette que l'on va concevoir.
Connectez-vous au Gitlab  (https://votre-serveur-gitlab/) , créez-vous un compte, puis:

Dans le coin haut-droit de la page web gitlab, vous trouverez le menu permettant de gérer votre compte utilsiateur Gitlab. Lorsque vous cliquez sur celui-ci, un menu déroulant aappraît, cliquez sur le sous-menu “Settings”. Pour indication, dans ce même menu, on trouve: le bouton “sign out”, les sous-menus “Profile” et “Help”.
    Sur la gauche vous voyez maintenant un menu vertical , comportant un sous menu “SSH Keys”, cliquez sur ce sous-menu. Vous êtes maintenant dans une page, comportant un formulaire, et dans ce formulaire, collez la valeur de la clé publique que vous avez générée. Vous pouvez afficher (et copier) la valeur de cette clé publique avec l'instruction:
```
$ cat $HOME/.ssh/id_rsa.pub
```
Désormais, il est possible, dans la machine uneVM , pour l'utilisateur linux $UTILISATEUR, de cloner un repo auquel votre utilisateur Gitlab a accès (soit parce qu'on lui a donné accès à celui-ci, soit parce qu'il l'a créé), avec le protocole SSH, et en s'authentifiant 'silencieusement'. Vérifiez-le en exécutant:

```
$ cd $HOME
$ export MAISON_OPERATIONS=$(pwd)/ma-meilleure-recette
$ mkdir -p $MAISON_OPERATIONS
$ cd $MAISON_OPERATIONS
$ export NOM_DU_REPO_QUE_VOUS_AVEZ_CREE="iaac-centos-base"
$ export VOTRE_USERNAME_GITLAB="Jean-Baptiste-Lasselle"
$ # export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=2222
$ export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=22
$ export NOM_D_HOTE_OU_IP_DE_VOTRE_SRV_GITLAB=gitlab.mon-entreprise.io
$ export URI_SSH_GIT_REMOTE="ssh://git@$NOM_D_HOTE_OU_IP_DE_VOTRE_SRV_GITLAB:$NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB"
$ export GIT_SSH_COMMAND="ssh -p$NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB -i ~/.ssh/id_rsa" && git clone "$URI_SSH_GIT_REMOTE/$VOTRE_USERNAME_GITLAB/$NOM_DU_REPO_QUE_VOUS_AVEZ_CREE" .
$ echo "et pour vérifier...:"
$ ls -all
```

Enfin, si vous avez exécuté exactement la procédure de clonage git précédente, vous pourrez entrer dans le cycle suivant:

* exécutez la recette
* exécutez vos tests
* interprétez vos tests
* préparez votre message de commit, en suivant la boucle exacte (utilsiez putty ou un terminal shell classique):

```
$ export MESSAGE_COMMIT=""
$ # Pressez la touche clavier "flèche haute", double cliquez sur "MESSAGE_COMMIT" (ce qui 
# # copiera la chaîne de caractères  "MESSAGE_COMMIT" dans le presse-papier)
$ export MESSAGE_COMMIT="$MESSAGE_COMMIT "
$ # Pressez une deuxième fois la flèche haute, et complétez le message de commit:
$ # export MESSAGE_COMMIT="$MESSAGE_COMMIT Vous compléterez ici le message de commit qui vous chante "
```
apportez un changement au code source de la recette (avec vi)
faîtes le commit and push:

```
$ export MESSAGE_COMMIT=""
$ # Pressez la touche flèche haute, double cliquez sur "MESSAGE_COMMIT" (ce qui copiera la chaîne de caractère "MESSAGE_COMMIT")
$ export MESSAGE_COMMIT="$MESSAGE_COMMIT "
$ # Pressez une deuxième fois la flèche haute, et complétez le message de commit: 
$ export MESSAGE_COMMIT="$MESSAGE_COMMIT Modification de la fonction [ synchroniserAuSrvNTP () ] "
$ # Et seulement alors, commit && push
$ # export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=2222
$ export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=22
$ export GIT_SSH_COMMAND="ssh -p$NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB -i ~/.ssh/id_rsa"
$ git add * *.* **/* && git commit -m "$MESSAGE_COMMIT" && git push
```

## Recette initialisation du cycle Infrastructure As Code

```
#!/bin/bash
# Création du fichier de script
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- ENV.
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
export MAISON_OPERATIONS

# export NOM_D_HOTE_OU_IP_DE_VOTRE_SRV_GITLAB=gitlab.mon-entreprise.io
export NOM_D_HOTE_OU_IP_DE_VOTRE_SRV_GITLAB=github.com
# export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=2222
export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=22

export URI_SSH_GIT_REMOTE_FOR_GITLAB="ssh://git@$NOM_D_HOTE_OU_IP_DE_VOTRE_SRV_GITLAB"
export URI_SSH_GIT_REMOTE_FOR_GITHUB="git@$NOM_D_HOTE_OU_IP_DE_VOTRE_SRV_GITLAB"
# Valeur par défaut
export URI_SSH_GIT_REMOTE=$URI_SSH_GIT_REMOTE_FOR_GITLAB


export VOTRE_USERNAME_GITLAB="Jean-Baptiste-Lasselle"
export GIT_SSH_COMMAND="ssh -p$NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB -i ~/.ssh/id_rsa"

# -
# Pour initialiser un cycle IAAC, on commmence par créer
# le repository de versionning du code source de la recette IAAC
# On pourra aller jusqu'à créer un archetype Maven"
# -
# L'utilisateur a fournit la chaîne de caractères vide, pour
# nom du repository Git surl lequel il souhaite travailler. 
# Aucun nom de repository ne peut être fournit par défaut.
# 
export NOM_DU_REPO_QUE_VOUS_AVEZ_CREE

# - 
#   Le chemin du répertoire contenant les fichiers à versionner
# - 
# 
export REPERTOIRE_FICHIERS_A_VERSIONNER



# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- FONCTIONS
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 

# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# Cette fonction permet de demander interactivement à l'utilisateur du
# script, quel est le nom du repo git sur lequel il souhaite travailler
# ERREURS: si le nom du repo choisit est vide.
demander_NomRepoGit () {

        echo "Quel est le nom du repository Git sur lequel vous souhaitez travailler?"
        echo " "
        echo " "
        read NOM_REPO_CHOISIT
        # Vérifier l'existence du repo avec CURL ?
        if [ "x$NOM_REPO_CHOISIT" = "x" ]
        then
          export MESSAGE_ERREUR=""
          export MESSAGE_ERREUR="$MESSAGE_ERREUR L'utilisateur a fournit la chaîne de caractères vide, pour"
          export MESSAGE_ERREUR="$MESSAGE_ERREUR nom du repository Git surl lequel il souhaite travailler. "
          export MESSAGE_ERREUR="$MESSAGE_ERREUR Aucun nom de repository ne peut être fournit par défaut."
		  # un arrêt total des opérations, avec message d'erreur envoyé sur le canal standard d'erreur OS.
          $(>&2 echo "$MESSAGE_ERREUR") && exit 1
        else
          NOM_DU_REPO_QUE_VOUS_AVEZ_CREE=$NOM_REPO_CHOISIT
        fi
}
demander_NomRepoGit
export MAISON_OPERATIONS=$(pwd)/$NOM_DU_REPO_QUE_VOUS_AVEZ_CREE-$RANDOM
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# Cette fonction permet de demander interactivement à l'utilisateur du
# script, quel est le chemin du répertoire contenant les fichiers à récupérer.
# Le contenu de ce répertoire sera copié tel quel et récursivement, à la racine
# du repo local Git.
# - 
#  Dépendances: [demander_NomRepoGit ()] doit être exécutée avant
# 
# - 
# 
demander_CheminRepertoireFichiersAversionner () {

        echo "Quel est le chemin du répertoire contenant les fichiers à versionner?"
        echo " "
        echo " (ATTENTION: Si vous donnez un chein relatif, donnez-le relativement au )"
        echo " ( répertoire [$MAISON_OPERATIONS] )"
        echo " "
        read NOM_REPERTOIRE_CHOISIT
        echo " "
        # Vérifier l'existence du repo avec CURL ?
        if [ "x$NOM_REPERTOIRE_CHOISIT" = "x" ]
        then
          export MESSAGE_ERREUR=""
          export MESSAGE_ERREUR="$MESSAGE_ERREUR L'utilisateur a fournit un chaîne de caractères vide, pour"
          export MESSAGE_ERREUR="$MESSAGE_ERREUR le chemin du réperoire dans lequel sont les fichiers à versionner. "
          export MESSAGE_ERREUR="$MESSAGE_ERREUR Aucun chemin ne peut être fournit par défaut."
		  # un arrêt total des opérations, avec message d'erreur envoyé sur le canal standard d'erreur OS.
          $(>&2 echo "$MESSAGE_ERREUR") && exit 1
        else
          REPERTOIRE_FICHIERS_A_VERSIONNER=$NOM_REPERTOIRE_CHOISIT
        fi
	if [ -d "$REPERTOIRE_FICHIERS_A_VERSIONNER" ]
        then
          export MESSAGE_INFO=""
          export MESSAGE_INFO="$MESSAGE_INFO Le répertoire "
	      export MESSAGE_INFO="$MESSAGE_INFO    [$REPERTOIRE_FICHIERS_A_VERSIONNER]   "
          export MESSAGE_INFO="$MESSAGE_INFO existe et va être copié dans [ $(pwd) ]"
		  echo "$MESSAGE_INFO"
        else
          export MESSAGE_ERREUR3=""
          export MESSAGE_ERREUR3="$MESSAGE_ERREUR3 Le répertoire "
	      export MESSAGE_ERREUR3="$MESSAGE_ERREUR3    [$REPERTOIRE_FICHIERS_A_VERSIONNER]   "
          export MESSAGE_ERREUR3="$MESSAGE_ERREUR3 n'existe pas. Quels Fichiers souahitez-vous versionner?"
          # un arrêt total des opérations, avec message d'erreur envoyé sur le canal standard d'erreur OS.
          $(>&2 echo "$MESSAGE_ERREUR3") && exit 1
        fi
}




# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- OPERATIONS
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 




echo " DEBUG 1 - PWD= [$(pwd)]"
echo " DEBUG 1 - NOM_DU_REPO_QUE_VOUS_AVEZ_CREE= [$NOM_DU_REPO_QUE_VOUS_AVEZ_CREE]"
read

rm -rf $MAISON_OPERATIONS
mkdir -p $MAISON_OPERATIONS
cd $MAISON_OPERATIONS
echo " DEBUG 2 - PWD= [$(pwd)]"


demander_CheminRepertoireFichiersAversionner

# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- Configuration Git pour mon environnement de travail IAAC "Infrastructure Code Management Environnement"
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 

git config --global user.email "jean.baptiste.lasselle.it@gmail.com" 
git config --global user.name "Jean-Baptiste-Lasselle" 
# Enfin, depuis Git 2.0 :
git config --global push.default matching

# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- PREMIER GIT CLONE DU REPO VIDE
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 

# - On détermine s'il s'agit de Github ou d'un Gitlab
echo "$NOM_D_HOTE_OU_IP_DE_VOTRE_SRV_GITLAB" > ./es-ce-github.kytes
export ES_CE_GITHUB=$(cat ./es-ce-github.kytes | grep github)

if [ "x$ES_CE_GITHUB" = "x" ]
then
    echo "[$NOM_DU_REPO_QUE_VOUS_AVEZ_CREE] : Il s'agit d'un repository dans un serveur Gitlab privé"
	export URI_SSH_GIT_REMOTE=$URI_SSH_GIT_REMOTE_FOR_GITLAB 
else
    echo "[$NOM_DU_REPO_QUE_VOUS_AVEZ_CREE] : Il s'agit d'un repository https://github.com"
    export URI_SSH_GIT_REMOTE=$URI_SSH_GIT_REMOTE_FOR_GITHUB
fi
rm -f ./es-ce-github.kytes

# - On fait le git clone
git clone "$URI_SSH_GIT_REMOTE:$VOTRE_USERNAME_GITLAB/$NOM_DU_REPO_QUE_VOUS_AVEZ_CREE" .


# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- copie, dans le répertoire contenant le ".git", de tous les fichiers et répertoires
#     que vous souhaitez versionner
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
echo " DEBUG 3 - PWD= [$(pwd)]"
cp -rf $REPERTOIRE_FICHIERS_A_VERSIONNER/* .
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- initialisation du cycle iaac
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 

export MESSAGE_COMMIT=""
# Pressez la touche flèche haute, double cliquez sur "MESSAGE_COMMIT" (ce qui copiera la chaîne de caractère "MESSAGE_COMMIT")
export MESSAGE_COMMIT="$MESSAGE_COMMIT "
# Pressez une deuxième fois la flèche haute, et complétez le message de commit: 
export MESSAGE_COMMIT="$MESSAGE_COMMIT commit initial "
# Et seulement alors, ajout de tous les fichiers commit && push

export GIT_SSH_COMMAND="ssh -p$NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB -i ~/.ssh/id_rsa"
git add * *.* **/* && git commit -m "$MESSAGE_COMMIT" && git push --set-upstream origin master

```

## Recette reprise du cycle Infrastructure As Code

```
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- ENV.
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=2222
export NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB=22
export GIT_SSH_COMMAND="ssh -p$NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB -i ~/.ssh/id_rsa"
# --- EDITEZ LE CODE SOURCE DE VOTRE RECETTE IAAC


# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
# --- reprise du cycle iaac
# --- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
export MESSAGE_COMMIT=""
# Pressez la touche flèche haute, double cliquez sur "MESSAGE_COMMIT" (ce qui copiera la chaîne de caractère "MESSAGE_COMMIT")
export MESSAGE_COMMIT="$MESSAGE_COMMIT "
# Pressez une deuxième fois la flèche haute, et complétez le message de commit: 
export MESSAGE_COMMIT="$MESSAGE_COMMIT commit initial "
# Et seulement alors, commit && push
export GIT_SSH_COMMAND="ssh -p$NUMERO_PORT_IP_DE_VOTRE_SRV_GITLAB -i ~/.ssh/id_rsa"
git add * *.* **/* && git commit -m "$MESSAGE_COMMIT" && git push
```
