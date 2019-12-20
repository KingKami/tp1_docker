# TP Formation DOCKER

Charbonnel Damien V1.1
18/09/2019

Ezhilarasan Karthike V1
08/11/2019

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
    docker exec -t nginx-alpine echo "hello from nginx" >> /home/root/toto.txt
    docker exec -t debian-stable echo "hello from debian" >> /home/root/toto.txt
    ```

5. Vérifier l'état des fichiers dessus :hourglass:

## Partie 3 : DOCKER – Réseau

1. [Regarder le fonctionnement du "réseau" sur la documentation officielle Docker](https://docs.docker.com/v17.09/engine/userguide/networking/) :heavy_check_mark:
2. Essayer de relier les containers entre eux en effectuant un réseau privé entre eux. :heavy_check_mark:

    ```bash
    docker network create -d bridge --subnet 192.168.0.0/24 --gateway 192.168.0.1 my_private_network
    docker network connect my_private_network nginx-alpine
    docker network connect my_private_network debian-stable
    ```

3. Vérifier si le ping fonctionne :heavy_check_mark:

    ```bash
    docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' debian-stable
    docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx-alpine
    docker exec -t nginx-alpine ping #insert debian ip
    ```

## Partie 4 : DOCKER – Stack – Docker Compose

1. [Regarder le fonctionnement du "stack" sur la documentation officielle Docker ](https://docs.docker.com/compose/) :heavy_check_mark:
2. Créer un fichier Yaml en version 3 afin de démarrer : :heavy_check_mark:
    - 2 container ngnix:latest
    - un réseau isolé bridge entre eux
    - Un volume partagé
    ```yaml
    version: "3"
    services:
    webserver:
        image: nginx
        container_name: "webserver"
        restart: unless-stopped
        tty: true
        volumes:
        - "VolContainer1:/VolContainer1"
        ports:
        - "80:80"
        - "443:443"
    webserver2:
        image: nginx
        container_name: "webserver2"
        restart: unless-stopped
        tty: true
        ports:
        - "81:81"
        - "444:444"
    volumes:
        VolContainer1:
    networks:
    default:
        external:
        name: myNetwork
    ```
3. Lancer la stack :heavy_check_mark:
    ```bash
    docker-compose up -d
    ```

## Partie 5 : DOCKER – Création de Micro services :heavy_check_mark:

1. Récupérer l'image "httpd" (apache 2) :heavy_check_mark:
    ```bash
    docker pull httpd
    ```
2. Lancer le container en forwardant le port en mode automatique : :heavy_check_mark:
    ```bash
    docker run --name=httpd -d -p 80  httpd
    ```
3. Regarder sur quel port est transféré le port 80 du serveur Apache :heavy_check_mark:
    ```bash
    docker ps -a | grep httpd
    ```
4. Essayer d'y accéder via l'url local de la machine http://127.0.0.1:XXXX :heavy_check_mark:
    ```bash
    curl <ip>
    ```
5. Lancer un autre container en forwardant le port en mode manuel (fixé): :heavy_check_mark:
    ```bash
    docker run --name=httpd2 -d -p 80:80  httpd
    ```
6. Regarder sur quel port est transféré le port 80 du serveur Apache :heavy_check_mark:
    ```bash
    docker ps -a | grep httpd2
    ```
7. Essayer d'y accéder via l'url local de la machine http://127.0.0.1 :heavy_check_mark:
    ```bash
    curl <ip>
    ```
8. Arrêter les deux containers actifs :heavy_check_mark:
    ```bash
    docker stop httpd httpd2
    ```
9.  Supprimer les deux containers :heavy_check_mark:
    ```bash
    docker rm httpd httpd2
    ```