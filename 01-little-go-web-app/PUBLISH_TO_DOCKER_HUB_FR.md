# Publier votre application sur Docker Hub

Ce guide explique comment construire et publier l'image de votre application web Go sur l'organisation Docker Hub **bluewhale** avec la version **0.0.0**.

## Prérequis

- Docker est installé et en cours d'exécution
- Vous disposez d'un compte Docker Hub avec accès à l'organisation `bluewhale`
- Vous êtes connecté à Docker Hub via la CLI

## Étape 1 : Se connecter à Docker Hub

Si vous n'êtes pas déjà connecté, authentifiez-vous auprès de Docker Hub :

```bash
docker login
```

Vous serez invité à entrer votre nom d'utilisateur et votre mot de passe Docker Hub (ou un token d'accès personnel).

## Étape 2 : Construire l'image Docker

Naviguez vers le répertoire du projet et construisez l'image avec la balise appropriée :

```bash
docker build -t bluewhale/little-go-web-app:0.0.0 .
```

Cette commande :
- Construit l'image à partir du `Dockerfile`
- La marque avec le tag `bluewhale/little-go-web-app:0.0.0` (remplacez `bluewhale` par votre nom d'organisation si différent)
- Utilise le répertoire actuel (`.`) comme contexte de compilation

## Étape 3 : Vérifier l'image

Listez vos images pour confirmer qu'elle a été construite avec succès :

```bash
docker images | grep little-go-web-app
```

Vous devriez voir une entrée avec le tag `bluewhale/little-go-web-app:0.0.0` et la taille de l'image.

## Étape 4 : Envoyer l'image vers Docker Hub

Envoyez l'image vers l'organisation Docker Hub :

```bash
docker push bluewhale/little-go-web-app:0.0.0
```

Cela télécharge l'image vers l'organisation `bluewhale` sur Docker Hub.

## Étape 5 : (Optionnel) Marquer comme Latest

Si vous souhaitez également marquer cette version comme `latest` :

```bash
docker tag bluewhale/little-go-web-app:0.0.0 bluewhale/little-go-web-app:latest
docker push bluewhale/little-go-web-app:latest
```

## Étape 6 : Vérifier sur Docker Hub

Visitez https://hub.docker.com/orgs/bluewhale et naviguez vers vos dépôts pour confirmer que l'image est publiée.

## Référence rapide : Toutes les commandes

```bash
# Se connecter (si nécessaire)
docker login

# Construire l'image
docker build -t bluewhale/little-go-web-app:0.0.0 .

# Vérifier la construction
docker images | grep little-go-web-app

# Envoyer vers Docker Hub
docker push bluewhale/little-go-web-app:0.0.0

# (Optionnel) Marquer également comme latest
docker tag bluewhale/little-go-web-app:0.0.0 bluewhale/little-go-web-app:latest
docker push bluewhale/little-go-web-app:latest
```

## Dépannage

### La transmission échoue avec « denied: requested access to the resource is denied »
- Confirmez que vous êtes connecté : `docker login`
- Vérifiez que vous disposez des permissions pour l'organisation `bluewhale`

### La construction échoue
- Assurez-vous que Docker est en cours d'exécution
- Vérifiez que tous les fichiers référencés dans le `Dockerfile` existent
- Examinez la sortie de la compilation pour les dépendances manquantes

### Le téléchargement d'image est lent
- Vérifiez votre connexion Internet
- Envisagez de construire sur une machine avec une meilleure bande passante si vous téléchargez depuis une connexion lente
