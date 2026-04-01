# Guide : Créer une Nouvelle Release

Ce guide explique comment créer et publier une nouvelle release de votre image Docker. La GitHub Action se charge automatiquement de builder, publier et scanner l'image.

## Prérequis

- Votre repository GitHub est correctement configuré (voir `GITHUB_SETUP_FR.md`)
- Vous avez poussé votre code sur la branche principale (`main` ou `master`)
- Vous avez accès au repository en ligne de commande (Git configuré)

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

## Flux Complet de Publication d'une Release

Le processus est simple : créer un tag Git avec la version et le pousser. Le reste est automatique !

```
git tag → git push → GitHub Action → Build → Push Docker Hub → Scan → Créer Release
```

---

## Étape 1 : Créer un Tag Git Local

Depuis votre terminal, dans le répertoire du projet, créez un tag annoté :

```bash
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"
```

**Remplacez `v0.0.1` par votre numéro de version.**

### Vérifier le Tag Créé

```bash
git tag
```

Vous devriez voir `v0.0.1` dans la liste des tags locaux.

---

## Étape 2 : Pousser le Tag vers GitHub

C'est le moment crucial ! Pousser le tag déclenche la GitHub Action.

```bash
git push origin v0.0.1
```

C'est tout ! La GitHub Action démarre automatiquement.

### Vérifier que le Tag est Poussé

```bash
git ls-remote --tags origin
```

Vous devriez voir votre tag dans la liste des tags distants.

---

## Étape 3 : Vérifier le Déclenchement de la GitHub Action

Après 5-10 secondes :

1. Allez sur votre repository GitHub : `https://github.com/VOTRE_USERNAME/votre-repo`
2. Cliquez sur l'onglet **Actions**
3. Vous verrez le workflow : `Build, Push and Scan with Docker Scout` en cours d'exécution

### Suivre la Progression

Cliquez sur le workflow pour voir les étapes :

1. **Checkout code** : Récupère le code du tag
2. **Set up Docker Buildx** : Prépare l'outil de build
3. **Login to Docker Hub** : Authentifie avec Docker Hub
4. **Extract version from tag** : Extrait la version du tag (ex: `0.0.1`)
5. **Build and push Docker image** : 
   - Construit l'image Docker
   - La publie sur Docker Hub avec le tag de version
   - La publie avec le tag `latest`
6. **Run Docker Scout vulnerability scan** : Scanne l'image pour les vulnérabilités
7. **Upload Scout SARIF report** : Envoie le rapport vers GitHub
8. **Create GitHub Release** : **Crée automatiquement la release GitHub avec description**

### Durée Estimée

Le workflow prend généralement **2 à 5 minutes** selon la taille de l'image et la vitesse de votre connexion.

---

## Étape 4 : Vérifier les Résultats

La GitHub Action crée automatiquement la release. Vous n'avez rien à faire !

### Sur GitHub

1. Allez sur votre repository → **Releases**
2. Vous verrez la nouvelle release créée automatiquement avec :
   - Le titre : `Release v0.0.1`
   - Une description générée automatiquement avec le lien Docker Hub
   - Les tags Docker publiés

### Sur Docker Hub

1. Allez sur https://hub.docker.com/r/bluewhale/little-go-web-app/tags
2. Vous devriez voir les nouveaux tags :
   - La version créée (ex: `0.0.1`)
   - Le tag `latest` mis à jour

### Sur GitHub Security

1. Allez sur votre repository → **Security** → **Code scanning alerts**
2. Vous verrez le rapport Docker Scout SARIF avec les résultats du scan

---

## Consulter les Résultats du Scan

### 1. Dans GitHub Security

- Allez sur votre repository → **Security** → **Code scanning alerts**
- Vous verrez les vulnérabilités identifiées par Docker Scout
- Cliquez sur une vulnérabilité pour voir les détails

### 2. Sur la Console du Workflow

- Dans le workflow GitHub Actions, la dernière étape affiche le rapport en texte

### 3. Sur Docker Scout (optionnel)

- Visitez https://scout.docker.com pour une analyse détaillée

---

## Exemple Complet : De la Modification au Déploiement

Voici un exemple complet si vous avez des changements à faire avant la release :

```bash
# 1. Faire vos modifications du code
# (éditer fichiers, tester, etc.)

# 2. Commiter les changements
git add .
git commit -m "Prepare release v0.0.1"

# 3. Pousser sur la branche principale
git push origin main  # ou master

# 4. Créer le tag
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# 5. Pousser le tag (DÉCLENCHE LA GITHUB ACTION)
git push origin v0.0.1

# C'est tout ! Attendez que le workflow se termine (~2-5 minutes)
```

---

## Commandes Rapides de Référence

### Créer et Pousser une Release en Une Ligne

```bash
git tag -a v0.0.1 -m "Release v0.0.1" && git push origin v0.0.1
```

### Créer plusieurs versions rapidement

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

### Supprimer un Tag (En Cas d'Erreur)

```bash
# Supprimer le tag localement
git tag -d v0.0.1

# Supprimer le tag sur GitHub (et la release associée)
git push origin --delete v0.0.1
```

---

## Dépannage

### Le workflow ne démarre pas après le push du tag

- Vérifiez que le tag commence par `v` (ex: `v0.0.1`, `v1.0.0`)
- Vérifiez que vous avez bien poussé le tag : `git push origin vX.X.X`
- Attendez 10-15 secondes et rechargez la page **Actions**
- Vérifiez le tag est visible sur GitHub : `https://github.com/VOTRE_USERNAME/votre-repo/tags`

### Erreur : "denied: requested access to the resource is denied"

- Vérifiez les secrets Docker Hub : `DOCKER_HUB_USERNAME` et `DOCKER_HUB_PASSWORD`
- Confirmez que votre utilisateur Docker Hub a accès à l'organisation `bluewhale`

### Erreur : "manifest not found"

- Vérifiez que le `Dockerfile` existe au chemin `./01-little-go-web-app/`
- Confirmez que tous les fichiers requis par le `Dockerfile` sont présents

### L'image n'apparaît pas sur Docker Hub

- Attendez 1-2 minutes (le push peut être lent)
- Vérifiez le log du workflow pour les erreurs
- Visitez : `https://hub.docker.com/r/bluewhale/little-go-web-app`

### La release n'apparaît pas sur GitHub

- Attendez 1-2 minutes
- Allez sur : `https://github.com/VOTRE_USERNAME/votre-repo/releases`
- Si elle n'apparaît pas, vérifiez que le workflow s'est complété sans erreur (onglet **Actions**)

### Le tag ne remonte pas sur GitHub

```bash
# Vérifier que le tag local existe
git tag

# Pousser le tag spécifique
git push origin v0.0.1

# Ou pousser tous les tags à la fois
git push origin --tags
```

---

## Résumé du Processus Automatisé

| Étape | Action | Type | Résultat |
|-------|--------|------|----------|
| 1 | Créer tag Git | ❌ Manuel | Tag créé localement |
| 2 | Pousser tag | ❌ Manuel | Tag poussé, workflow déclenché |
| 3 | Build image Docker | ✅ Automatique | Image construite |
| 4 | Push Docker Hub | ✅ Automatique | Image publiée |
| 5 | Scan Docker Scout | ✅ Automatique | Rapport généré |
| 6 | Upload SARIF | ✅ Automatique | Résultats visibles |
| 7 | Créer Release GitHub | ✅ Automatique | Release créée avec description |

**Vous ne devez faire que les étapes 1 et 2. Le reste est entièrement automatique !**

---

## Prochaines Étapes

Après avoir créé une release :

1. Vérifiez que le workflow a réussi (onglet **Actions**)
2. Consultez les résultats du scan Docker Scout (**Security** → **Code scanning alerts**)
3. Vérifiez que l'image est publiée sur Docker Hub
4. Vérifiez que la release GitHub a été créée (**Releases**)
5. Corrigez les vulnérabilités critiques identifiées
6. Créez une nouvelle release pour les corrections

---

## Raccourcis Utiles

- Tags : `https://github.com/VOTRE_USERNAME/votre-repo/tags`
- Releases : `https://github.com/VOTRE_USERNAME/votre-repo/releases`
- Actions : `https://github.com/VOTRE_USERNAME/votre-repo/actions`
- Security : `https://github.com/VOTRE_USERNAME/votre-repo/security`
- Docker Hub : `https://hub.docker.com/r/bluewhale/little-go-web-app`
- Docker Scout : `https://scout.docker.com`
