# Installation et configuration de UFW

**Date :** 10/06/2026

## Installation de UFW

* Installation de UFW avec les commandes :

```bash
sudo apt update
sudo apt install ufw -y
```

## Vérification de l'installation

* Vérification de l'installation avec :

```bash
sudo ufw status
```

À ce stade :

* tout est bloqué en entrée 
* tout est autorisé en sortie

## Configuration de la politique par défaut

* Configuration des règles par défaut :

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### Explications:

* `deny incoming` : bloque toutes les connexions entrantes non autorisées.
* `allow outgoing` : autorise les connexions sortantes initiées par le serveur.

Cette configuration applique le principe de sécurité du moindre privilège, c'est à dire que seuls les services explicitement autorisés seront accessibles.

## Autorisation des services nécessaires

* Autorisation des ports utilisés par les services du SRV-PI avec :

```bash
sudo ufw allow 22/tcp
sudo ufw allow 53/tcp
sudo ufw allow 53/udp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 3000/tcp
sudo ufw allow 5000/tcp
sudo ufw allow 9090/tcp
sudo ufw allow 9100/tcp
```

### Correspondance des ports

| Port | Service        |
| ---- | -------------- |
| 22   | SSH            |
| 53   | Pi-hole (DNS)  |
| 80   | HTTP           |
| 443  | HTTPS          |
| 3000 | Grafana        |
| 5000 | Portail SRV-PI |
| 9090 | Prometheus     |
| 9100 | Node Exporter  |

## Activation du pare-feu

* Activation de UFW :

```bash
sudo ufw enable
```

Une confirmation est demandée avant l'application des règles.

## Vérification de la configuration

* Vérification détaillée des règles :

```bash
sudo ufw status verbose
```

Cette commande permet de vérifier :

* l'état du pare-feu 
* la politique par défaut 
* les ports autorisés 
* les règles actuellement appliquées


