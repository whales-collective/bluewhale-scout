# Configuration du Repository GitHub pour la Publication Automatique

Ce guide explique comment configurer votre repository GitHub pour permettre à la GitHub Action de builder, publier et scanner automatiquement votre image Docker lors de la création d'une release.

## Prérequis

- Un repository GitHub public ou privé
- Un compte Docker Hub avec accès à l'organisation `bluewhale`
- Des droits d'administration sur le repository GitHub

## Étape 1 : Créer un Personal Access Token Docker Hub

Pour que la GitHub Action puisse publier l'image sur Docker Hub, vous devez créer un token d'accès personnel (PAT).

1. Connectez-vous à votre compte Docker Hub : https://hub.docker.com
2. Cliquez sur votre avatar en haut à droite → **Account Settings**
3. Dans le menu de gauche, cliquez sur **Security** → **Personal Access Tokens**
4. Cliquez sur **Generate new token** ou **Create new token**
5. Donnez-lui un nom descriptif : `github-action-publisher`
6. Sélectionnez la permission **Read & Write** (pour publier les images)
7. Cliquez sur **Generate**
8. **Copiez le token** immédiatement (vous ne pourrez pas le revoir après)

## Étape 2 : Ajouter les Secrets GitHub

Les secrets GitHub permettent de stocker vos identifiants de manière sécurisée.

1. Allez sur votre repository GitHub
2. Cliquez sur **Settings** (onglet de configuration)
3. Dans le menu de gauche, cliquez sur **Secrets and variables** → **Actions**
4. Cliquez sur **New repository secret**

### Ajouter le secret DOCKER_HUB_USERNAME

- **Name** : `DOCKER_HUB_USERNAME`
- **Secret** : Votre nom d'utilisateur Docker Hub
- Cliquez sur **Add secret**

### Ajouter le secret DOCKER_HUB_PASSWORD

- **Name** : `DOCKER_HUB_PASSWORD`
- **Secret** : Collez le PAT (Personal Access Token) créé à l'étape 1
- Cliquez sur **Add secret**

## Étape 3 : Vérifier le fichier GitHub Action

Assurez-vous que le fichier `.github/workflows/build-push-scan.yml` est présent dans votre repository à la racine du projet. Ce fichier décrit le workflow automatisé.

### Structure attendue du repository

```
.
├── .github/
│   └── workflows/
│       └── build-push-scan.yml     ← Le fichier workflow
├── 01-little-go-web-app/
│   ├── Dockerfile
│   ├── main.go
│   └── ...
└── README.md
```

## Étape 4 : Vérifier les paramètres du Workflow

Le fichier workflow utilise les variables suivantes que vous pouvez personnaliser :

- `REGISTRY` : `docker.io` (Docker Hub)
- `IMAGE_NAME` : `bluewhale/little-go-web-app` — **À adapter si votre organisation ou nom de repo est différent**
- `WORKING_DIRECTORY` : `./01-little-go-web-app` — Le chemin vers votre Dockerfile

Pour modifier ces paramètres :
1. Ouvrez `.github/workflows/build-push-scan.yml`
2. Modifiez les valeurs dans la section `env:`
3. Validez et pushez les modifications

## Étape 5 : Activer les Permissions du Workflow

Par défaut, GitHub Actions a les bonnes permissions. Vérifiez que :

1. Allez sur votre repository → **Settings** → **Actions** → **General**
2. Sous **Workflow permissions**, assurez-vous que **Read and write permissions** est sélectionné
3. Cliquez sur **Save**

## Récapitulatif de la Configuration

| Élément | Configuration |
|--------|---------------|
| **GitHub Action** | `.github/workflows/build-push-scan.yml` |
| **Déclencheur** | Création d'une release |
| **Actions** | Build → Push → Scan (Docker Scout) |
| **Secrets requis** | `DOCKER_HUB_USERNAME`, `DOCKER_HUB_PASSWORD` |
| **Variables d'environnement** | `REGISTRY`, `IMAGE_NAME`, `WORKING_DIRECTORY` |

## Dépannage

### Le workflow ne s'exécute pas après une release

- Vérifiez que la release a le type `published` (pas `draft`)
- Vérifiez que le fichier `.github/workflows/build-push-scan.yml` est sur la branche `main` (ou celle par défaut)
- Allez sur votre repository → **Actions** pour voir les logs du workflow

### Erreur : "denied: requested access to the resource is denied"

- Vérifiez que `DOCKER_HUB_USERNAME` et `DOCKER_HUB_PASSWORD` sont correctement configurés
- Confirmez que le token PAT a la permission **Read & Write**
- Assurez-vous que votre utilisateur Docker Hub a accès à l'organisation `bluewhale`

### Erreur : "Error response from daemon: manifest not found"

- Vérifiez que la variable `IMAGE_NAME` dans le workflow correspond à votre organisation et repository Docker Hub
- Confirmez que le Dockerfile est au chemin spécifié dans `WORKING_DIRECTORY`

### Le scan Docker Scout ne s'exécute pas

- Docker Scout est gratuit pour les images publiques
- Si votre image est privée, vérifiez vos permissions Docker Scout
- Les résultats du scan apparaissent sous **Security** → **Code scanning alerts** dans GitHub

## Prochaines Étapes

Une fois configuré, chaque nouvelle release créée déclenchera automatiquement :
1. **Build** de l'image Docker
2. **Push** vers Docker Hub avec les tags de version et `latest`
3. **Scan** des vulnérabilités avec Docker Scout
4. **Upload** du rapport SARIF sur GitHub (visible dans la section Security)

Consultez le guide **"Créer une Release"** pour les instructions sur la création d'une nouvelle release.
