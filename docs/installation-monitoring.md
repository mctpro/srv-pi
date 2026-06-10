# Installation du service de monitoring

**Date :** 22/05/2026

## Installation de Node Exporter

* Création du dossier monitoring avec :

```bash
mkdir monitoring
```

* Installation de node exporter (archive compressée) pour la récupération de métriques avec la commande :

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-arm64.tar.gz
```

* Vérification de la présence du contenu dans monitoring avec :

```bash
ls
```

* Décompression de l’archive avec :

```bash
tar xvf node_exporter-1.9.1.linux-arm64.tar.gz
```

* Vérification du contenu du fichier node exporter avec la commande :

```bash
cd node_exporter-1.9.1.linux-arm64
```

* Lancement du programme node exporter avec :

```bash
./node_exporter
```

* Visualisation des métriques dans le navigateur web :

```text
http://192.168.X.X:9100/metrics
```

## Installation de Prometheus

* Téléchargement de Prometheus pour le stockage des métriques :

```bash
wget https://github.com/prometheus/prometheus/releases/download/v3.3.1/prometheus-3.3.1.linux-arm64.tar.gz
```

* Extraction de l’archive avec :

```bash
tar xvf prometheus-3.3.1.linux-arm64.tar.gz
```

* Ouverture du fichier de configuration :

```bash
nano prometheus.yml
```

* Modifications de la configuration pour demander à Prometheus de récupérer les métriques de Node Exporter sur le port 9100.

## Lancement des services

* Lancement des deux services manuellement sur deux terminaux différents :

### Terminal 1 : Node Exporter

```bash
cd ~/monitoring/node_exporter-1.9.1.linux-arm64
./node_exporter
```

### Terminal 2 : Prometheus

```bash
cd ~/monitoring/prometheus-3.3.1.linux-arm64
./prometheus --config.file=prometheus.yml
```

* Les deux terminaux sont comme bloqués (normal car les services sont en cours d’exécution).

* Lancement de l’interface web Prometheus avec :

```text
http://192.168.X.X:9090
```

* Vérification du bon fonctionnement des services (petit 1 en bout de ligne).

## Installation de Grafana

* Installation d’un outil qui sert à télécharger des paquets depuis internet avec :

```bash
sudo apt install wget gnupg2 -y
```

(l’outil qui gère les signatures cryptographiques GPG)

* Téléchargement de la clé GPG de Grafana :

```bash
wget -q -O - https://packages.grafana.com/gpg.key | gpg --dearmor | sudo tee /usr/share/keyrings/grafana.gpg > /dev/null
```

* Ajout du dépôt Grafana :

```bash
echo "deb [signed-by=/usr/share/keyrings/grafana.gpg] https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```

(pour que Debian sache qu’il peut télécharger des paquets depuis ici)

* Installation du dépôt Grafana avec :

```bash
sudo apt install grafana -y
```

* Activation de Grafana au démarrage avec :

```bash
sudo systemctl enable grafana-server
```

* Démarrage immédiat de Grafana :

```bash
sudo systemctl start grafana-server
```

* Vérification de l’état du service avec :

```bash
sudo systemctl status grafana-server
```

## Configuration de Grafana

* Ouverture de Grafana sur le navigateur web avec :

```text
http://192.168.X.X:3000
```

* Changement du mdp admin

* Ajout de Prometheus dans la data source

* Importation du dashboard 1860 (dashboard communautaire déjà prêt pour Node Exporter)
