# Création du conteneur Docker pour Dozzle et installation

**Date :** 27/05/2026

## Préparation

* Création du dossier dozzle dans le dossier docker du SRV-PI :

```bash
cd docker
mkdir dozzle
```

## Installation de Dozzle

* Lancement du conteneur :

```bash
sudo docker run -d \
  --name dozzle \
  --restart always \
  -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  amir20/dozzle:latest
```

### Explication de la commande

* `-d` : exécute le conteneur en arrière-plan.
* `--name dozzle` : attribue le nom "dozzle" au conteneur.
* `--restart always` : redémarre automatiquement le conteneur après un redémarrage du serveur.
* `-p 8080:8080` : rend l'interface web accessible sur le port 8080.
* `-v /var/run/docker.sock:/var/run/docker.sock` : permet à Dozzle d'accéder aux informations Docker et aux logs des conteneurs.
* `amir20/dozzle:latest` : télécharge et exécute la dernière version disponible de Dozzle.

## Vérification du fonctionnement

* Utilisation de la commande suivante pour afficher les conteneurs Docker :

```bash
sudo docker ps
```

* Vérification du fonctionnement du service en tapant la commande :

```bash
sudo docker restart dozzle
```

puis en regardant dans Dozzle l'apparition des logs liés au redémarrage du conteneur.

## Choix d'architecture

* Afin de ne pas casser mon système stable, je laisse les premiers services installés en natif. Je les passerai sur Docker dans quelques temps avec de l’expérience acquise.


