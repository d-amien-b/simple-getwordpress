## Simple-getwordpress
# Installation de Wordpress sur un server Ubuntu 
# Tester sur ubuntu 18.04 et 20.04
# **********************************************

Le module Simple-getwordpress installe un site worpress sur une ou plusieurs machines en quelques minutes seulement. 
C'est parfait si vous voulez réaliser ou tester des modules et autres extensions surr cet excellent CMS opensource. 

    -   Le 1er playbook installe un site wordpress de A à Z avec l'environnement LAMP (Linux, Apache, Mysql, PHP).

    -   Un second playbook fait 2 sauvegardes :
        - Il copie la base de données et la transfère dans un dossier de sauvegarde 
        - Il copie l'instance de wordpress et la déplace dans un dossier de sauvegarde

# Il est nécessaire d'avoir au moins 2 machines :
    -   1 machine qui sera le node manager pour jouer les playbooks 
    -   1 machine Ubuntu 18.04 ou 20.04 sur laquelle les playbooks installeront et configureront 
        un site wordpress de dernière génération et ses sauvegardes.

# Au préalable, une configuration s'impose sur le node manager :
    -   Il est nécessaire d'installer PHP (automatique via update et upgrade) 
    -   Installation de Ansible 
    -   Il est important d'installer Python (python3-pip, avec le paquet sshpass) pour pouvoir travailler
        en toute sécurité au sein d'un environnement de travail virtuel (avec env ou virtualenv par exemple).
        Pour ma part,j'utilise l'IDE Visual Code de microsoft (parfait sur linux aussi avec le module remote ssh).
        Python peut vous permettre d'aller plus loin vers une automatisation encore plus spécifique.
    -   Il est aussi impératif de créer un nouvelle utilisateur sur la machine distante avec les droits SUDO.
        Ceci permettra d'accéder depuis le node manager en ssh (je recommande l'utilisation de ssh-keygen + ssh-copy-id -i "" user@machine).
    -   Dans le cas de machine virtuels, il faut absolument déclarer leur propre IP et nom de server dans le etc/hosts :
        Adresse IP locale -> [10|172|192].*.*.* - Idem pour le node manager 
    -   2 Collections devront être ajouter à Ansible (les fichiers se trouvent dans Collections-a-installer) :
            community-general-2.3.0.tar.gz  
            community-mysql-1.3.0.tar.gz
            -> Commande pour l'installation : $ ansible-galaxy collection install *.tar.gz


Ce premier playbook c'est largement inspiré de part les nombreux tutoriels de DigitalOcean (hébergeur web).
Des liens intéressants relatif à la configuration d'ansible, de lamp et de Python sont proposés à la fin de cette page.
Un cours sur Ansible est particulièrement intéressant et disponible sur OPENCLASSROOM.

Ce projet s'inscrit dans le cadre d'une formation d'administrateur en infrastucture et cloud sour OPENCLASSROOM
www.openclassroom.com


### Let's play with Simple-getwordpress

## Les paramètres
- `php_modules`:            un tableau comprend les différentes extensions indispensables à l'installation 
                            de wordpress. Il n'est pas nécessaire d'en ajouter de nouvelle au demeurant.
                            Cette dernière version de wordpress est fonctionnelle et prête à l'emploi immédiatement
- `mysql_root_password`:    Le mot de passe MYSQL du compte **root**. Pensez à utiliser la commande ansible-vault encrypt-string.
- `mysql_db`:               Le nom de la Base de donnée pour wordpress.
- `mysql_user`:             Le nom de l'utilisateur de MYSQL pour la bdd de WordPress.
- `mysql_password`:         Le mot de passe de l'utilisateur de MySQL. Pensez à utiliser la commande ansible-vault encrypt-string.
- `http_host`:              Le nom de l'instance du host en cours.
- `http_conf`:              Le nom du fichier de configuration du site qui sera créé dans Apache.
- `http_port`:              Le port HTTP pour ce hôte virtuel est 80 par défaut. 

# ** Mise en place **

### 1 - Paramètres system -> dépendances de php -> Automatique rien à renseigner
php_modules: [ 'php-curl', 'php-gd', 'php-mbstring', 'php-xml', 'php-xmlrpc', 'php-soap', 'php-intl', 'php-zip' ]

### 2 - Paramétrage des variables
Les variables se trouvent dans le fichier default.yml qui se trouve dans le dossier vars des 2 playbooks respectifs.

    # paramètres de MySQL
        mysql_root_password: "motdepassedel'utilisateurrootdemysql"
        mysql_db: "nomdelabdddewordpress"
        mysql_user: "admindemysql"
        mysql_password: "motdepassedel'admindemysql"

    # Paramètre HTTP
        http_host: ['machine1','machine2','machineX']  !!!! idem idem hosts  !important .
        http_conf: "nomdufichierdeconfigurationdusitewordpress"
        http_port: "80"

        La variable "http_host" est un tableau comprenant la list des machines à configurer.

### 3 - Une seule commande pour jouer les 2 playbooks :
    
## Exécution du playbook 1 - Dans le dossier wordpress-lamp
    $ cd ansible-playbooks
    $ cd wordpress-lamp
    $ ansible-playbook playbook.yml -i ../hosts -l "groupsDansInventaire"  -u "VotreUtilisateur" --ask-pass --become --ask-become-pass
    
## Exécution du playbook 2 : commande idem précédente depuis wordpress-bdd_backup :) 
    $ cd ..
    $ cd wordpress-bdd_backup
    $ ansible-playbook playbook.yml -i ../hosts -l "groupsDansInventaire"  -u "VotreUtilisateur" --ask-pass --become --ask-become-pass

# ** Vos machines sont prêtes en quelques minutes par machine **

# Cours et tutos traitants de ce playbook 
    *******************************************
    https://openclassrooms.com/fr/courses/2035796-utilisez-ansible-pour-automatiser-vos-taches-de-configuration
    https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-20-04-server-fr
    https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-20-04-quickstart-fr
    https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-20-04-fr
    https://docs.ansible.com/
