# Installation du service Docker (manière recommandée par Docker)

**Date :** 24/05/2026

## Suppression des paquets conflictuels

* Désinstallation des paquets conflictuels avec :

```bash
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-doc podman-docker containerd runc | cut -f1)
```

* Aucun paquet conflictuel

## Configuration du dépôt Docker

* Configuration du Docker apt avec :

```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

## Ajout du dépôt Docker

* Création du fichier de configuration APT pour ajouter le dépôt Docker au système avec :

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

## Installation de Docker

* Mise à jour avec :

```bash
sudo apt update
```

* Installation des packages Docker :

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Vérification du fonctionnement

* Vérification du bon fonctionnement :

```bash
sudo systemctl status docker
```

* Test de la réussite de l’installation en exécutant l’image hello-world :

```bash
sudo docker run hello-world
```

## Configuration du démarrage automatique

* Mise en place du lancement automatique de Docker à chaque démarrage du SRV-PI avec :

```bash
sudo systemctl enable docker
```


