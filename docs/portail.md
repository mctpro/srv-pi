# Création du portail d’accueil de SRV-PI

## Méthode utilisée

Je vais d’abord écrire mon code Python dans PyCharm, et mon HTML dans Notepad++, puis je ferai un copier/coller dans nano :

```bash
nano app.py
nano index.html
```

## Création du serveur web Python Flask

* Création du fichier `app.py`

* Ajout du code Flask :

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def accueil():
    return render_template("index.html")

app.run(host="0.0.0.0", port=5000)
```

## Compréhension du fonctionnement

* Flask lance un serveur web.
* `@app.route("/")` correspond à la page d’accueil.
* `render_template()` affiche le fichier HTML.
* `0.0.0.0` permet l’accès depuis les autres machines du réseau.

## Dockerisation du portail SRV-PI

### Création du fichier requirements.txt

Contenu :

```text
flask
```

### Création du fichier Dockerfile

Contenu :

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

EXPOSE 5000

CMD ["python", "app.py"]
```

## Compréhension du Dockerfile

### FROM

* utilise une image Python légère.

### WORKDIR

* crée le dossier de travail du conteneur.

### COPY

* copie les fichiers du projet dans le conteneur.

### RUN

* installe Flask automatiquement.

### EXPOSE

* indique l’utilisation du port 5000.

### CMD

* lance automatiquement Flask au démarrage du conteneur.

## Construction de l’image Docker

* Construction de l’image :

```bash
sudo docker build -t srv-pi-portail .
```

### Compréhension

* Docker lit le Dockerfile.
* construit automatiquement l’image.
* installe Python et Flask.
* copie le portail dans l’image.

## Lancement du conteneur

* Commande utilisée :

```bash
sudo docker run -d -p 5000:5000 --name portail srv-pi-portail
```

### Compréhension

* `-d` → lancement en arrière-plan.
* `-p 5000:5000` → liaison du port du Pi avec celui du conteneur.
* `--name portail` → nom du conteneur.

