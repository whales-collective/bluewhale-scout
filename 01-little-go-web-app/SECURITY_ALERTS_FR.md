# Alertes de sécurité GitHub via Docker Scout SARIF

## Comment ça fonctionne

Le workflow CI/CD intègre déjà le scan de vulnérabilités Docker Scout et publie les résultats sous forme de rapport SARIF sur GitHub. Cela active la fonctionnalité native **Code Scanning Alerts** de GitHub pour suivre les vulnérabilités et vous notifier — aucune modification du workflow n'est nécessaire.

### Étapes du workflow concernées

```
Run Docker Scout vulnerability scan
        │
        ▼
  scout-results.sarif
        │
        ▼
Upload Scout SARIF report  (github/codeql-action/upload-sarif@v4)
        │
        ▼
  Onglet Security GitHub
```

## Configuration du workflow

Le workflow est configuré pour **faire échouer le job** lorsque Docker Scout détecte des vulnérabilités de sévérité Critical ou High. Cela déclenche automatiquement un email de notification d'échec de workflow GitHub.

```yaml
- name: Run Docker Scout vulnerability scan
  uses: docker/scout-action@v1.18.0
  with:
    command: cves
    image: ...
    sarif-file: scout-results.sarif
    write-comment: false
    exit-code: true                  # fait échouer le step si des CVEs sont trouvées
    only-severities: critical,high   # uniquement pour les sévérités Critical et High

- name: Upload Scout SARIF report
  uses: github/codeql-action/upload-sarif@v4
  if: always()                       # s'exécute même si le step de scan échoue
  with:
    sarif_file: scout-results.sarif
    category: docker-scout
```

### Paramètres clés

| Paramètre | Valeur | Effet |
|---|---|---|
| `exit-code` | `true` | Fait échouer le step si des CVEs sont trouvées |
| `only-severities` | `critical,high` | Filtre sur Critical et High uniquement |

### Notification par email en cas d'échec

Lorsque le step de scan échoue, GitHub envoie un email de notification d'échec de workflow à toute personne qui surveille le dépôt avec les notifications **"Failed workflows"** activées :

```
Profile > Settings > Notifications > GitHub Actions
```

Activez **"Send notifications for failed workflows only"** pour recevoir des alertes ciblées.

## Activer les alertes de sécurité GitHub

### 1. Activer le Code Scanning sur votre dépôt

Accédez à votre dépôt sur GitHub :

```
Settings > Advanced Security > Code security > Code scanning
```

Assurez-vous que **Code scanning** est activé.

### 2. Configurer les notifications par email

GitHub envoie automatiquement des notifications par email lorsque :
- Une **nouvelle vulnérabilité** est détectée, absente du scan précédent
- La sévérité d'une alerte existante change
- Une alerte est ignorée ou résolue

Pour configurer les destinataires des notifications :

```
Settings > Notifications
```

Sous **"Code scanning alerts"**, choisissez :
- **Your activity only** — uniquement si vous êtes à l'origine de la vulnérabilité
- **All activity** — pour toute nouvelle alerte sur le dépôt

### 3. Gérer les alertes dans l'onglet Security

Après chaque exécution du workflow, les résultats apparaissent ici :

```
Repository > Security > Code scanning
```

Chaque alerte contient :
- L'identifiant CVE et la sévérité (Critical / High / Medium / Low)
- Le package et la version concernés
- Une description et des conseils de remédiation
- Un lien vers la base NVD / advisory database

## Cycle de vie des alertes

| Événement | Notification envoyée |
|---|---|
| Nouveau CVE détecté (absent du scan précédent) | Oui |
| CVE déjà présent dans le scan précédent | Non |
| Alerte ignorée manuellement | Non |
| CVE précédemment ignoré qui réapparaît | Oui |

## Notifications pour toute l'équipe

Pour notifier l'ensemble de l'équipe, ajoutez tous les collaborateurs dans :

```
Settings > Collaborators and teams
```

Chaque membre peut ensuite configurer ses propres préférences de notification individuellement.

## Liens utiles

- [Documentation GitHub Code Scanning](https://docs.github.com/fr/code-security/code-scanning)
- [Gérer les alertes de code scanning](https://docs.github.com/fr/code-security/code-scanning/managing-code-scanning-alerts)
- [Sortie SARIF de Docker Scout](https://docs.docker.com/scout/integrations/ci/github-actions/)
