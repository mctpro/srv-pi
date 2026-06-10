# Configuration de mes conteneurs et installation et configuration de Portainer

**Date :** 26/05/2026

## Vérification de Docker

* Vérification du bon démarrage de Docker avec :

```bash
sudo systemctl status docker
```

* Listing des conteneurs actuellement lancés avec :

```bash
sudo docker ps
```

## Tests Docker

* Création d’un conteneur temporaire pour avoir le message officiel Docker qui m’explique que tout fonctionne.

* Utilisation de la commande suivante pour voir les conteneurs qui ont été arrêtés aussi :

```bash
sudo docker ps -a
```

* Nettoyage des conteneurs tests qui ne servent plus avec la commande :

```bash
sudo docker rm wizardly_bell crazy_volhard
```

## Préparation de Portainer

* Création d’un volume Docker pour la data de Portainer avec la commande :

```bash
sudo docker volume create portainer_data
```

* Vérification de la création du volume avec :

```bash
sudo docker volume ls
```

## Installation de Portainer

* Lancement du conteneur Portainer avec :

```bash
sudo docker run -d \
  -p 8000:8000 \
  -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```
### Explication de la commande

Cette commande télécharge puis lance automatiquement le conteneur Portainer en arrière-plan.

* `-d` : exécute le conteneur en arrière-plan.
* `-p 8000:8000` : ouvre le port 8000 utilisé par certaines fonctionnalités de Portainer.
* `-p 9443:9443` : ouvre le port sécurisé de l’interface web Portainer.
* `--name portainer` : attribue le nom "portainer" au conteneur.
* `--restart=always` : redémarre automatiquement le conteneur après un redémarrage du serveur.
* `-v /var/run/docker.sock:/var/run/docker.sock` : permet à Portainer de communiquer avec Docker afin de gérer les conteneurs.
* `-v portainer_data:/data` : stocke la configuration et les données de Portainer dans le volume Docker `portainer_data`.
* `portainer/portainer-ce:latest` : télécharge et exécute la dernière version communautaire de Portainer.

Elle permet donc de déployer une interface web d’administration Docker persistante et accessible depuis le réseau local.

* Utilisation de la commande suivante pour savoir si Portainer fonctionne correctement :

```bash
sudo docker ps
```

`0.0.0.0:9443->9443/tcp` veut dire que le service web est accessible sur mon réseau.

## Première connexion à Portainer

* Connexion à l’interface web de Portainer :

```text
https://192.168.X.X:9443
```

* Choix du mot de passe

* Découverte de l’interface

## Vérification du système

* Vérification des ports utilisés par le SRV-PI avec la commande :

```bash
sudo ss -tulpn
```


