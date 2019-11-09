# TP Formation DOCKER

Charbonnel Damien V1.1
18/09/2019

## Partie 1 : DOCKER – Prise en main

1. Installer DOCKER :heavy_check_mark:

    ```bash
    apt-get install docker-ce
    ```

2. Lister les images installer :heavy_check_mark:

    ```bash
    docker images
    ```

3. Installer l'image "hello-world:latest" :heavy_check_mark:

    ```bash
    docker pull hello-world:latest
    ```

4. Installer une autre image au choix (perl, syncthing….)

    ```bash
    docker pull nginx:alpine
    ```

5. Lancer un ou plusieurs conteneurs avec un shell actif (par exemple Ubuntu , bash) :heavy_check_mark:

    ```bash
    docker run -d -t -i --name nginx-alpine nginx:alpine
    docker pull debian:stable
    docker run -d -t -i --name debian-stable debian:stable
    docker ps -a # --all
    ```

6. Vérifier si les containers sont isolés ou non :heavy_check_mark:

    - isolés car aucun port n'a été exposés, aucun docker network leur a été attachés, ils n'ont donc aucun moyen de communiquer avec un autre système que le système hôte.

7. Installer une version alternative d'une image :heavy_check_mark:

    ```bash
    docker pull debian:stable
    ```

8. Supprimer une image :heavy_check_mark:

    - une image ne peut être supprimée que si elle n'est utilisée par aucun conteneur

    ``` bash
    docker stop debian-stable
    docker rm debian-stable
    docker rmi debian:stable:
    ```

9. Utiliser les commandes container start & stop pour gérer les containers actifs :heavy_check_mark:

    ```bash
    docker stop nginx-alpine
    docker start nginx-alpine
    docker restart nginx-alpine
    ```

## Partie 2 : DOCKER – Partage de fichier inter container

1. [Regarder le fonctionnement des "volumes" sur la documentation officielle Docker](https://docs.docker.com/storage/volumes/) :heavy_check_mark:

2. Créer un volume nommer "VolContainer1" :heavy_check_mark:

    ```bash
    docker volume create VolContainer1
    docker volume ls
    ```

3. Partager ce volume entre différents containers :heavy_check_mark:

    ```bash
    docker stop nginx-alpine
    docker rm nginx-alpine
    docker pull debian:stable:
    docker run -d -t -i --name nginx-alpine --mount source=VolContainer1,target=/home/root/ nginx:alpine
    docker run -d -t -i --name debian-stable --mount source=VolContainer1,target=/home/root/ debian:stable
    ```

4. Écrire des fichiers dessus : :heavy_check_mark:

    ```bash
    docker exec -itu root nginx-alpine echo "hello from nginx" >> /home/root/toto.txt
    docker exec -itu root debian-stable echo "hello from debian" >> /home/root/toto.txt
    ```

5. Vérifier l'état des fichiers dessus :hourglass:

## Partie 3 : DOCKER – Réseau

1. Regarder le fonctionnement du "réseau" sur la documentation officielle Docker :hourglass:
2. Essayer de relier les containers entre eux en effectuant un réseau privé entre eux. :hourglass:
3. Vérifier si le ping fonctionne :hourglass:
4. Vérifier si le transfert de fichier fonctionne :hourglass:

## Partie 3 : DOCKER – Stack – Docker Compose

1. Regarder le fonctionnement du "stack" sur la documentation officielle Docker :hourglass:
2. Créer un fichier Yaml en version 3 afin de démarrer : :hourglass:
    - 2 container ngnix:latest
    - un réseau isolé bridge entre eux
    - Un volume partagé
3. Lancer la stack :hourglass:
4. Vérifier le fonctionnement :hourglass:

## Partie 4 : DOCKER – Création de Micro services

1. Récupérer l'image "httpd" (apache 2) :hourglass:
2. Lancer le container en forwardant le port en mode automatique : :hourglass:
    - docker run --name=httpd -d -p 80  httpd
3. Regarder sur quel port est transféré le port 80 du serveur Apache :hourglass:
4. Essayer d'y accéder via l'url local de la machine http://127.0.0.1:XXXX :hourglass:
5. Lancer un autre container en forwardant le port en mode manuel (fixé): :hourglass:
    - docker run --name=httpd -d -p 80:80  httpd
6. Regarder sur quel port est transféré le port 80 du serveur Apache :hourglass:
7. Essayer d'y accéder via l'url local de la machine http://127.0.0.1 :hourglass:
8. Arrêter les deux containers actifs :hourglass:
9. Supprimer les deux containers :hourglass:
