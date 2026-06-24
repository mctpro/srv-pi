# Installation de Nextcloud avec Docker Compose

**Date :** 24/06/2026

## Vérification de Docker

* Vérification de Docker Compose avec :

```bash
docker compose version
```

Résultat :

```text
Docker Compose version v5.1.4
```

* Vérification du service Docker avec :

```bash
sudo systemctl status docker
```

Le service était actif :

```text
active (running)
```

## Création du dossier du projet

* Création du dossier contenant le fichier Compose avec :

```bash
mkdir -p ~/docker/nextcloud
cd ~/docker/nextcloud
```

* Vérification du chemin avec :

```bash
pwd
```

Résultat :

```text
/home/mc/docker/nextcloud
```

## Création du fichier Compose

* Création du fichier avec :

```bash
sudo nano compose.yml
```

* Configuration utilisée :

```yaml
services:
  db:
    image: mariadb:11
    container_name: nextcloud-db
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - db_data:/var/lib/mysql
    environment:
      - MARIADB_ROOT_PASSWORD=********
      - MARIADB_DATABASE=nextcloud
      - MARIADB_USER=nextcloud
      - MARIADB_PASSWORD=********

  app:
    image: nextcloud:apache
    container_name: nextcloud-app
    restart: unless-stopped
    ports:
      - "8081:80"
    volumes:
      - nextcloud_data:/var/www/html
    environment:
      - MYSQL_HOST=db
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=********
      - NEXTCLOUD_ADMIN_USER=mcadmin
      - NEXTCLOUD_ADMIN_PASSWORD=********
      - NEXTCLOUD_TRUSTED_DOMAINS=192.X.X.X
    depends_on:
      - db

volumes:
  db_data:
  nextcloud_data:
```

## Explication de l'architecture

* Nextcloud est l’interface web et permet la gestion des fichiers.

* MariaDB est la base de données utilisée par Nextcloud.

* Les volumes Docker permettent le stockage persistant de la base de données et des fichiers/configurations Nextcloud.

| Service             | Rôle                                            |
| ------------------- | ----------------------------------------------- |
| `nextcloud-app`     | Interface web Nextcloud et gestion des fichiers |
| `nextcloud-db`      | Base de données MariaDB                         |
| `nextcloud_default` | Réseau Docker privé entre les conteneurs        |
| `nextcloud_data`    | Volume persistant de Nextcloud                  |
| `db_data`           | Volume persistant de MariaDB                    |

* Configuration du port Docker :

```yaml
- "8081:80"
```

`8081:80` signifie que le port `8081` du Raspberry Pi est relié au port `80` du conteneur Nextcloud.


## Démarrage des conteneurs

* Démarrage des conteneurs avec :

```bash
sudo docker compose up -d
```

* Vérification avec :

```bash
sudo docker compose ps
```

Résultat attendu :

```text
nextcloud-app   Up   0.0.0.0:8081->80/tcp
nextcloud-db    Up   3306/tcp
```

## Accès à Nextcloud

* Accès à Nextcloud depuis le réseau local avec :

```text
http://192.X.X.X:8081
```

* Compte administrateur créé :

```text
Utilisateur : mcadmin
```

* La connexion et la création de fichiers ont été testées avec succès.

* Les nombreux fichiers `.odt`, `.ods`, `.odp`, `.odg` et `.whiteboard` visibles lors de la première connexion étaient des fichiers d’exemple ajoutés automatiquement par Nextcloud.

## Accès hors domicile

* Nextcloud n’est pas exposé directement sur Internet, l’accès à distance fonctionne via WireGuard.

* Cela permet d’accéder aux fichiers depuis l’extérieur sans ouvrir publiquement Nextcloud sur Internet.

## Commandes utiles

| Action                              | Commande                          |
| ----------------------------------- | --------------------------------- |
| Voir l’état des conteneurs          | `sudo docker compose ps`          |
| Démarrer Nextcloud                  | `sudo docker compose up -d`       |
| Arrêter les conteneurs              | `sudo docker compose stop`        |
| Relancer les conteneurs             | `sudo docker compose start`       |
| Voir les logs Nextcloud             | `sudo docker compose logs -f app` |
| Voir les logs MariaDB               | `sudo docker compose logs -f db`  |
| Arrêter et supprimer les conteneurs | `sudo docker compose down`        |
| Mettre à jour les images            | `sudo docker compose pull`        |
| Appliquer une mise à jour           | `sudo docker compose up -d`       |

> Ne pas utiliser `sudo docker compose down -v` sauf si l’objectif est de supprimer complètement Nextcloud et MariaDB. L’option `-v` supprime les volumes Docker, donc les fichiers, la configuration et la base de données.


