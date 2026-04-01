# Guide : Créer une Nouvelle Release

Ce guide explique comment créer une nouvelle release de votre image Docker et déclencher automatiquement la publication et le scan avec Docker Scout.

## Prérequis

- Votre repository GitHub est correctement configuré (voir `GITHUB_SETUP_FR.md`)
- Vous avez des droits de création de release sur le repository
- Votre code a été pushé sur la branche principale (`main` ou `master`)

## Avant de Créer une Release

Avant de créer une release, assurez-vous que :

1. **Votre code est à jour** sur la branche principale
2. **Les tests passent** (si vous en avez)
3. **La version est prête** pour la publication

### Choisir une Stratégie de Versioning

Ce projet utilise **Semantic Versioning** (SemVer) : `MAJOR.MINOR.PATCH`

- `MAJOR` : Changement majeur incompatible (ex: 1.0.0 → 2.0.0)
- `MINOR` : Nouvelle fonctionnalité rétro-compatible (ex: 1.0.0 → 1.1.0)
- `PATCH` : Correction de bug (ex: 1.0.0 → 1.0.1)

Exemples de versions :
- `0.0.1` (version initiale)
- `0.1.0` (ajout de fonctionnalités)
- `1.0.0` (première version stable)
- `1.1.0` (nouvelles fonctionnalités)
- `1.1.1` (correction de bug)

---

## Méthode 1 : Créer une Release avec Git (Ligne de Commande) ⚡

Cette méthode crée un tag Git et le pousse vers GitHub, ce qui déclenche **automatiquement** le workflow.

### Étape 1 : Créer et Pousser le Tag

```bash
# 1. Assurez-vous que tout est commité et à jour
git status

# 2. Créer un tag annoté pour la version
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# 3. Pousser le tag vers GitHub (CECI DÉCLENCHE LE WORKFLOW)
git push origin v0.0.1
```

**Remplacez `v0.0.1` par votre numéro de version.**

### Étape 2 : Exemple Complet avec Changements

Si vous avez des changements à commiter d'abord :

```bash
# Ajouter tous les changements
git add .

# Commiter les changements
git commit -m "Prepare release v0.0.1"

# Créer le tag annoté
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# Pousser le commit vers la branche principale
git push origin main  # ou master, selon votre branche par défaut

# Pousser le tag (CECI DÉCLENCHE IMMÉDIATEMENT LE WORKFLOW)
git push origin v0.0.1
```

### Étape 3 : Vérifier que le Tag est Bien Poussé

```bash
# Lister les tags locaux
git tag

# Voir les tags distants
git ls-remote --tags origin
```

Vous devriez voir votre tag dans la liste.

### Étape 4 : Vérifier le Déclenchement du Workflow

Après 5-10 secondes :

1. Allez sur votre repository GitHub
2. Cliquez sur l'onglet **Actions**
3. Vous verrez le workflow : `Build, Push and Scan with Docker Scout` en cours d'exécution

---

## Méthode 2 : Créer une Release via l'Interface GitHub Web

Vous pouvez aussi créer une release avec description et notes via l'interface web.

### Étape 1 : Accéder à la Page des Releases

1. Allez sur `https://github.com/VOTRE_USERNAME/votre-repo/releases`
2. Vous verrez vos tags existants (créés via Git)

### Étape 2 : Créer la Release GitHub

#### Option A : Depuis un Tag Existant

1. Cherchez votre tag (ex: `v0.0.1`)
2. Cliquez sur les trois points `...` à côté du tag
3. Cliquez sur **Create release**
4. Remplissez les informations ci-dessous

#### Option B : Créer une Nouvelle Release Directement

1. Cliquez sur **Draft a new release** ou **Create a new release**
2. Cliquez sur **Choose a tag**
3. Sélectionnez le tag que vous venez de créer (ex: `v0.0.1`)
4. Remplissez les informations ci-dessous

### Étape 3 : Remplir les Informations de la Release

#### Release title (Titre de la release)

- **Value**: Un titre descriptif
- **Exemples**:
  - `Release v0.0.1 - Initial Release`
  - `Release v1.0.0 - Stable Version`
  - `Release v1.1.0 - New Features and Fixes`

#### Description

- **Value**: Une description détaillée des changements
- **Template recommandé**:

```markdown
## 🎉 Changes in this Release

### ✨ New Features
- Feature 1
- Feature 2

### 🐛 Bug Fixes
- Bug fix 1
- Bug fix 2

### 📝 Improvements
- Improvement 1
- Improvement 2

### 🔒 Security Updates
(If applicable)

## Docker Image

This release publishes the Docker image to Docker Hub:

**Organization**: bluewhale
**Image**: bluewhale/little-go-web-app
**Tags**: 
- `0.0.1` (exemple)
- `latest`
```

#### Set as a pre-release (Marquer comme pré-release)

- Décochez cette option pour une release stable
- Cochez cette option pour une pré-release (ex: `1.0.0-beta`, `1.0.0-rc1`)

#### Set as the latest release (Marquer comme dernière release)

- Cochez cette option si c'est la version stable à jour
- GitHub l'affichera avec un badge `Latest`

### Étape 4 : Publier la Release

Cliquez sur **Publish release**

Cela va :
1. ✅ Créer la release GitHub avec description
2. ✅ **Le workflow a déjà été déclenché lors du push du tag**

---

## Surveiller le Workflow

Le workflow `Build, Push and Scan with Docker Scout` se déclenche automatiquement après le push du tag.

### Suivre la Progression

1. Allez sur votre repository GitHub
2. Cliquez sur l'onglet **Actions**
3. Vous verrez le workflow en cours : `Build, Push and Scan with Docker Scout`
4. Cliquez sur le workflow pour voir les détails

### Les Étapes du Workflow

Le workflow exécute automatiquement :

1. **Checkout code** : Récupère le code de la release
2. **Set up Docker Buildx** : Prépare l'outil de build
3. **Login to Docker Hub** : Authentifie avec Docker Hub (utilise les secrets configurés)
4. **Extract version from release tag** : Extrait la version du tag
5. **Build and push Docker image** : 
   - Construit l'image Docker
   - La publie sur Docker Hub avec le tag de version
   - La publie avec le tag `latest`
6. **Run Docker Scout vulnerability scan** : Scanne l'image pour les vulnérabilités

### Durée Estimée

Le workflow prend généralement **2 à 5 minutes** selon la taille de l'image et la vitesse de votre connexion.

---

## Vérifier la Publication

### Sur Docker Hub

1. Allez sur https://hub.docker.com/orgs/bluewhale
2. Cliquez sur le repository `little-go-web-app`
3. Vous devriez voir les nouveaux tags :
   - La version créée (ex: `0.0.1`)
   - Le tag `latest` mis à jour

### Sur GitHub

1. Allez sur votre repository → **Security** → **Code scanning alerts**
2. Vous verrez le rapport Docker Scout SARIF avec les résultats du scan

---

## Consulter les Résultats du Scan

Les résultats du scan Docker Scout apparaissent :

### 1. Dans GitHub Security

- Allez sur votre repository → **Security** → **Code scanning alerts**
- Vous verrez les vulnérabilités identifiées par Docker Scout
- Cliquez sur une vulnérabilité pour voir les détails

### 2. Sur la Console du Workflow

- Dans le workflow GitHub Actions, la dernière étape affiche le rapport en texte

### 3. Sur Docker Scout (optionnel)

- Visitez https://scout.docker.com pour une analyse détaillée

---

## Dépannage

### Le workflow ne s'exécute pas

- Vérifiez que vous avez configuré les secrets `DOCKER_HUB_USERNAME` et `DOCKER_HUB_PASSWORD`
- Assurez-vous que le tag a bien été poussé : `git push origin vX.X.X`
- Attendez quelques secondes et rechargez la page **Actions**
- Vérifiez que le tag est visible dans **Releases** sur GitHub

### Erreur : "denied: requested access to the resource is denied"

- Vérifiez les secrets Docker Hub configurés
- Confirmez que votre utilisateur Docker Hub a accès à l'organisation `bluewhale`

### Erreur : "manifest not found"

- Vérifiez que le `Dockerfile` existe au chemin spécifié dans le workflow
- Confirmez que tous les fichiers requis par le `Dockerfile` sont présents

### L'image n'apparaît pas sur Docker Hub

- Attendez 1-2 minutes (le push peut être lent)
- Vérifiez le log du workflow pour les erreurs
- Confirmez que le tag a bien été créé (voir **Actions** → workflow → **Build and push Docker image**)

### Le tag ne remonte pas sur GitHub

```bash
# Vérifier que le tag local existe
git tag

# Pousser tous les tags
git push origin --tags

# Ou pousser un tag spécifique
git push origin v0.0.1
```

---

## Résumé du Processus

| Étape | Action | Automatique | Résultat |
|-------|--------|-------------|----------|
| 1 | Créer tag Git | ❌ Manuel | Tag créé localement |
| 2 | Pousser tag | ❌ Manuel | Tag poussé sur GitHub |
| 3 | Déclencher workflow | ✅ Automatique | Workflow démarre |
| 4 | Build image Docker | ✅ Automatique | Image construite |
| 5 | Push sur Docker Hub | ✅ Automatique | Image publiée |
| 6 | Scan Docker Scout | ✅ Automatique | Rapport généré |
| 7 | Upload SARIF | ✅ Automatique | Résultats visibles dans GitHub |

---

## Commandes Rapides de Référence

### Créer et Pousser une Release (Complet)

```bash
# Commiter les changements
git add .
git commit -m "Release v0.0.1"

# Créer le tag
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# Pousser tout
git push origin main
git push origin v0.0.1
```

### Créer plusieurs tags rapidement

```bash
# v0.0.1
git tag -a v0.0.1 -m "Release v0.0.1"
git push origin v0.0.1

# v0.1.0
git tag -a v0.1.0 -m "Release v0.1.0"
git push origin v0.1.0

# v1.0.0
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

### Lister les Versions

```bash
# Tags locaux
git tag

# Tags distants
git ls-remote --tags origin

# Voir les détails d'un tag
git show v0.0.1
```

### Supprimer un Tag (Si Erreur)

```bash
# Supprimer le tag localement
git tag -d v0.0.1

# Supprimer le tag sur GitHub
git push origin --delete v0.0.1
```

---

## Prochaines Étapes

Après avoir créé une release :

1. Vérifiez que le workflow a réussi (onglet **Actions**)
2. Consultez les résultats du scan Docker Scout
3. Corrigez les vulnérabilités critiques identifiées
4. Créez une nouvelle release pour les corrections

---

## Raccourcis Utiles

- Releases : `https://github.com/VOTRE_USERNAME/votre-repo/releases`
- Actions : `https://github.com/VOTRE_USERNAME/votre-repo/actions`
- Security : `https://github.com/VOTRE_USERNAME/votre-repo/security`
- Docker Hub : `https://hub.docker.com/orgs/bluewhale`
- Docker Scout : `https://scout.docker.com`
