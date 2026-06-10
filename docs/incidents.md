# Incidents rencontrés et résolutions

Ce document regroupe les différents incidents rencontrés lors du déploiement et de l'administration du SRV-PI ainsi que les solutions mises en œuvre.

---

# Ping de SRV-PI impossible

**Date :** 19/05/2026

## Symptôme

Ping de `srv-pi.local` impossible.

## Vérifications

```bash
ping srv-pi.local
arp -a
```

## Cause

La résolution du nom d'hôte ne fonctionnait pas sur le réseau.

## Solution

Recherche de l'adresse IP sur l'interface d'administration de la Livebox :

```text
192.168.0.229
```

Connexion SSH via :

```bash
ssh mc@192.168.0.229
```

## Résultat

Connexion SSH fonctionnelle.

---

# Node Exporter indisponible dans Prometheus

**Date :** 22/05/2026

## Symptôme

Dans l'interface Prometheus (`/targets`), la cible Node Exporter apparaissait en erreur.

La requête :

```text
up
```

ne renvoyait aucune donnée.

## Vérifications

```bash
sudo systemctl status node_exporter
```

Le service systemd n'existait pas.

```bash
ls /usr/local/bin/node_exporter
```

Le binaire n'était pas présent.

## Cause

Installation incomplète de Node Exporter :

* binaire téléchargé mais non installé ;
* aucun service systemd ;
* Prometheus interrogeait un service inexistant.

## Solution

Recherche du binaire :

```bash
find / -name node_exporter 2>/dev/null
```

Copie du binaire :

```bash
sudo cp /home/mc/monitoring/node_exporter-1.9.1.linux-arm64/node_exporter /usr/local/bin/
```

Création du service systemd :

```text
/etc/systemd/system/node_exporter.service
```

Activation et démarrage :

```bash
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

## Résultat

Node Exporter opérationnel et visible dans Prometheus.

---

# Prometheus indisponible après redémarrage

**Date :** 22/05/2026

## Symptôme

Après redémarrage du Raspberry Pi :

* graphiques Grafana affichant N/A ;
* disparition de certaines métriques ;
* Prometheus inaccessible.

## Vérifications

```bash
sudo systemctl status prometheus
```

Aucun service systemd Prometheus n'existait.

## Cause

Installation incomplète :

* binaire non copié ;
* service systemd absent ;
* dossiers système manquants ;
* lancement uniquement manuel.

## Solution

Création de l'utilisateur dédié :

```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```

Copie du binaire :

```bash
sudo cp /home/mc/monitoring/prometheus-3.3.1.linux-arm64/prometheus /usr/local/bin/
```

Création des dossiers :

```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```

Attribution des permissions :

```bash
sudo chown prometheus:prometheus /var/lib/prometheus
```

Création du service systemd puis activation :

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

## Résultat

Prometheus et Grafana fonctionnels après redémarrage.

---

# Accès Portainer bloqué par le navigateur

**Date :** 26/05/2026

## Symptôme

Accès à l'interface web refusé car le certificat était considéré comme non sécurisé.

## Solution

Utilisation de :

```text
Avancé → Continuer vers le site
```

Puis redémarrage de Portainer :

```bash
sudo docker restart portainer
```

## Résultat

Accès à l'interface restauré.

---

# Partage Samba vide sous Windows

**Date :** 28/05/2026

## Symptôme

Le partage était accessible mais apparaissait vide.

## Vérifications

```bash
ls -la /srv/partage
```

Les fichiers étaient présents.

```bash
testparm
```

Configuration valide.

## Cause

Options Samba manquantes :

```ini
browseable = yes
guest ok = no
```

## Solution

Modification de :

```text
/etc/samba/smb.conf
```

Puis :

```bash
sudo systemctl restart smbd
```

## Résultat

Les fichiers sont redevenus visibles depuis Windows.

---

# Portail inaccessible après une mise à jour interrompue

**Date :** 03/06/2026

## Symptôme

Erreur :

```text
ERR_CONNECTION_REFUSED
```

## Vérifications

```bash
cd portail
python app.py
```

Application fonctionnelle.

```bash
sudo docker ps -a
```

Résultat :

```text
portail Exited (137)
```

## Cause

Le conteneur Docker était arrêté et ne possédait aucune politique de redémarrage automatique.

## Solution

Redémarrage :

```bash
sudo docker start portail
```

Vérification :

```bash
sudo docker ps
```

Configuration du redémarrage automatique :

```bash
sudo docker update --restart unless-stopped portail
```

Contrôle :

```bash
sudo docker inspect portail --format='{{.HostConfig.RestartPolicy.Name}}'
```

## Résultat

Le portail redémarre désormais automatiquement après un reboot.
