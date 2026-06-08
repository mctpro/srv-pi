# Premier lancement du SRV-PI

**Date :** 19/05/2026

## Premier démarrage

* Branchement du Raspberry Pi 5
* Lancement de la configuration initiale
* Observation du comportement de la LED du boîtier
* Connexion SSH depuis mon PC portable

## Configuration réseau

* Réservation de l'adresse IP `192.168.0.229` via le serveur DHCP de la box Internet

## Mise à jour du système

Mise à jour complète du système :

```bash
sudo apt update 
sudo apt full-upgrade -y
```

Redémarrage du serveur :

```bash
sudo reboot
```

## Vérifications système

Contrôle des informations système :

```bash
hostname
hostname -I
free -h
df -h
uname -a
```

Informations vérifiées :

* Nom d'hôte
* Adresse IP
* RAM disponible
* Espace disque disponible
* Version du noyau Linux

## Installation des utilitaires

Installation des premiers outils d'administration :

```bash
sudo apt install htop fastfetch curl wget
```

### Utilitaires installés

* **htop** : surveillance CPU/RAM
* **fastfetch** : informations du système
* **curl** : téléchargements et API
* **wget** : téléchargement de fichiers

## Vérification des paquets installés

```bash
apt list --installed | grep nom_du_paquet
```

## Surveillance de la température

```bash
vcgencmd measure_temp
```

## Arrêt du système

```bash
sudo shutdown -h now
```

## Résultat

- Première mise en service réussie

- Accès SSH fonctionnel

- Système mis à jour

- Outils d'administration installés

- Serveur prêt pour les premiers services comme Pi-Hole, Samba, Grafana, etc...


