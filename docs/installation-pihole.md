# Installation du logiciel Pi-Hole

**Date :** 20/05/2026

## Préparation du système

- Mise à jour du système avec :

```bash
sudo apt update && sudo apt upgrade -y
```

- Vérification de la configuration réseau

## Installation de Pi-Hole

- Installation complète du logiciel Pi-Hole avec la commande :

```bash
curl -sSL https://install.pi-hole.net | bash
```

- Choix du DNS CloudFlare car plus rapide et plus respectueux des données personnelles, DNSSEC activé pour la vérification supplémentaire de l’intégrité des réponses

- Configuration de base du Pi-Hole

- Changement du mot de passe Pi-Hole avec :

```bash
pihole setpassword
```

- Changement des paramètres réseau sur mon PC

## Accès à l'interface web

- Accès au tableau de bord web avec :

```text
http://192.168.X.X/admin
```

dans le navigateur.

## Déploiement sur le réseau

- Ajout d’autres postes présents sur le réseau

## Vérification du fonctionnement

- Vérification du bon fonctionnement du service sur plusieurs sites web
