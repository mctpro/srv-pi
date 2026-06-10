# Installation et configuration de Fail2ban

**Date :** 10/06/2026

## Installation de Fail2ban

* Installation de Fail2ban avec les commandes :

```bash
sudo apt update
sudo apt install fail2ban -y
```

## Vérification du service

* Vérification de l’activité du paquet :

```bash
sudo systemctl status fail2ban
```

* Vérification du statut de Fail2ban :

```bash
sudo fail2ban-client status
```

## Configuration de la protection SSH

* Création du fichier de configuration :

```bash
sudo nano /etc/fail2ban/jail.local
```

Contenu du fichier :

```ini
[DEFAULT]
bantime = 1h
findtime = 10m
maxretry = 5

[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s
backend = systemd
```

## Compréhension de la configuration

* `bantime = 1h` → adresse IP bloquée pendant 1 heure.
* `findtime = 10m` → fenêtre d'observation de 10 minutes.
* `maxretry = 5` → bannissement après 5 tentatives de connexion échouées.
* `[sshd]` → active la protection du service SSH.
* `enabled = true` → active la règle.
* `port = ssh` → surveille le service SSH.
* `logpath = %(sshd_log)s` → utilise les journaux SSH du système.
* `backend = systemd` → lecture des journaux via systemd.

## Activation de la configuration

* Redémarrage de Fail2ban :

```bash
sudo systemctl restart fail2ban
```

* Activation automatique au démarrage du SRV-PI :

```bash
sudo systemctl enable fail2ban
```

## Vérification de la protection SSH

* Vérification de la prison SSH :

```bash
sudo fail2ban-client status sshd
```

Cette commande permet de vérifier :

* que la protection SSH est active ;
* le nombre d'adresses IP bannies ;
* les adresses IP actuellement bloquées.

## Consultation des journaux

* Vérification des logs en temps réel :

```bash
sudo tail -f /var/log/fail2ban.log
```

Cette commande permet d'observer les détections et bannissements effectués par Fail2ban.

