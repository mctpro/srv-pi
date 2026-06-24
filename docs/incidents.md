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
192.168.X.X
```

Connexion SSH via :

```bash
ssh mc@192.168.X.X
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

# Incident - Déploiement de WireGuard sur SRV-PI

## Observations

Après l'installation de PiVPN et la création du premier profil client :

* Le tunnel WireGuard pouvait être activé sur mon téléphone.
* Aucun accès aux services du serveur n'était possible depuis l'extérieur.
* La commande `sudo wg` n'affichait aucun `latest handshake`.
* Aucun trafic n'était échangé entre le client et le serveur.

Le VPN semblait correctement installé mais aucune connexion n'atteignait SRV-PI.

---

## Diagnostic

### Vérification du service WireGuard

Contrôle de l'écoute du port UDP 51820 :

```bash
sudo ss -ulnp | grep 51820
```

Résultat :

```text
0.0.0.0:51820
*:51820
```

Le service WireGuard fonctionnait correctement.

---

### Vérification des échanges WireGuard

Commande exécutée :

```bash
sudo wg
```

Résultat :

Aucun `latest handshake` n'était affiché.

Le client ne contactait donc jamais le serveur.

---

## Cause identifiée

Analyse du fichier de configuration client :

```bash
sudo cat /etc/wireguard/configs/MC.conf
```

Résultat :

```ini
Endpoint = X.X.X.X:51820
```

Cette adresse correspondait à l'ancienne adresse IP publique détectée avant le passage en IP publique Orange.

Après la modification du modem, la nouvelle adresse publique était :

```text
X.X.X.X
```

Cependant, PiVPN conservait toujours l'ancienne valeur dans son fichier de configuration :

```bash
cat /etc/pivpn/wireguard/setupVars.conf | grep HOST
```

Résultat :

```text
pivpnHOST=X.X.X.X
```

Tous les nouveaux profils VPN générés utilisaient donc une adresse IP incorrecte.

---

## Résolution

Modification du fichier :

```bash
sudo nano /etc/pivpn/wireguard/setupVars.conf
```

Remplacement de :

```text
pivpnHOST=X.X.X.X
```

par :

```text
pivpnHOST=X.X.X.X
```

Puis suppression et recréation du profil VPN :

```bash
pivpn remove
pivpn add
```

Génération d'un nouveau QR Code :

```bash
pivpn -qr
```

Le nouveau profil a ensuite été importé dans l'application WireGuard du smartphone.

---

## Validation

Nouvelle vérification :

```bash
sudo wg
```

Résultat :

```text
latest handshake: 11 seconds ago
transfer: 29.88 KiB received, 45.79 KiB sent
```

Le tunnel VPN était alors pleinement opérationnel.

---

## Incident : port 8080 déjà utilisé

* Au premier démarrage, Nextcloud devait utiliser le port `8080`.

* Erreur obtenue :

```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```

* Recherche du service utilisant ce port avec :

```bash
sudo ss -tulpn | grep :8080
```

* Résultat : le port `8080` était déjà utilisé par Docker.

* Le port externe de Nextcloud a été changé de `8080` vers `8081`.

* Configuration finale :

```yaml
ports:
  - "8081:80"
```

## Incident : accès refusé à MariaDB

* Après le démarrage, Nextcloud affichait une erreur lors de la création du compte administrateur :

```text
SQLSTATE[HY000] [1045] Access denied for user 'nextcloud'
```

* Cause identifiée : le mot de passe configuré dans Nextcloud ne correspondait pas à celui créé dans MariaDB.

* Le mot de passe `MYSQL_PASSWORD` a été remplacé par le même mot de passe que `MARIADB_PASSWORD`.

* Comme la base avait déjà été initialisée avec la mauvaise configuration, les volumes ont été supprimés afin de recommencer l’installation proprement avec :

```bash
sudo docker compose down -v
sudo docker compose up -d
```

* Cette commande a été utilisée uniquement car l’installation était neuve et ne contenait encore aucun fichier personnel.


