# Installation du service de partage de fichiers Samba

**Date :** 28/05/2026

## Installation de Samba

* Installation de Samba avec :

```bash
sudo apt install samba -y
```

## Création du dossier de partage

* Création du dossier partage dans `/srv` avec :

```bash
sudo mkdir -p /srv/partage
```

## Gestion des permissions

* Mise en place des droits pour l’utilisateur `mc` avec la commande :

```bash
sudo chown -R mc:mc /srv/partage
```

## Vérification du service

* Vérification du statut du service avec :

```bash
systemctl status smbd
```

## Création de l'utilisateur Samba

* Ajout de l’utilisateur `mc` à Samba avec :

```bash
sudo smbpasswd -a mc
```

Cette commande permet de créer un mot de passe Samba pour l'utilisateur et de l'autoriser à accéder aux partages réseau.

## Configuration de Samba

* Modification du fichier de configuration de Samba avec :

```bash
sudo nano /etc/samba/smb.conf
```

* Ajout de la section partage tout en bas du fichier :

```ini
[Partage]
path = /srv/partage
browseable = yes
read only = no
writable = yes
guest ok = no
valid users = mc
```

## Vérification de la configuration

* Utilisation de la commande suivante pour vérifier la configuration :

```bash
testparm
```

Cette commande vérifie la syntaxe du fichier de configuration Samba et signale d’éventuelles erreurs.

## Redémarrage du service

* Redémarrage du service avec :

```bash
sudo systemctl restart smbd
```

## Accès depuis Windows

* Installation des lecteurs Windows en `\\192.168.X.X\Partage` depuis les ordinateurs locaux

* Vérification du bon fonctionnement du partage réseau.


