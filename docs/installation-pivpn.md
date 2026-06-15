# Installation du VPN WireGuard avec PiVPN

**Date :** 15/06/2026

## Préparation du système

* Mise à jour système avec :

```bash
sudo apt update
sudo apt upgrade -y
```

## Installation de PiVPN

* Lancement de la commande d’installation officielle de PiVPN avec :

```bash
curl -L https://install.pivpn.io | bash
```

* Choix de WireGuard car plus moderne, plus rapide et plus simple.

## Configuration du VPN

* Configuration du VPN.

* Reboot du SRV-PI.

## Création du premier profil VPN

* Création du premier profil avec :

```bash
pivpn add
```

* Demande du QR code avec :

```bash
pivpn -qr
```

à scanner avec l’application WireGuard sur mon téléphone.

## Configuration de la box Internet

* Création d’une règle de redirection sur la box Orange de la maison.

* Contournement du CGNAT avec le passage à une adresse IP publique officielle.

* Création de la règle NAT :

| Paramètre         | Valeur        |
| ----------------- | ------------- |
| Protocole         | UDP           |
| Port externe      | 51820         |
| Port interne      | 51820         |
| Adresse IP locale | 192.168.0.X |

## Vérification du fonctionnement

* Vérification du port écouté dans le terminal avec :

```bash
sudo ss -ulnp | grep 51820
```

* Essai de connexion au portail de SRV-PI sur mon téléphone via le réseau 5G.

