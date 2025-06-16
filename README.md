# gitWorkflow

# 🧩 Git Workflow – Guide Complet 

## 📁 Structure des Branches

| Branche      | Description                                    |
|--------------|------------------------------------------------|
| `main`       | Code **en production** (stable, déployé)      |
| `develop`    | Code **intégré en cours de développement**    |
| `feature/*`  | Développement de **nouvelles fonctionnalités** |
| `fix/*`      | Correction de **bug non critique** en dev     |
| `hotfix/*`   | Correction de **bug urgent** en production    |
| `release/*`  | *(optionnel)* Préparation d'une **version**   |

## 🛡️ Protection des Branches

**Important :** Les branches `main` et `develop` sont protégées et ne peuvent pas être modifiées directement via `git push` ou `git merge`. Toutes les modifications doivent passer par des Pull Requests (PR) sur GitHub.

---

### Configuration GitHub recommandée :
- ✅ Exiger une review de PR avant merge
- ✅ Invalider les anciennes reviews si nouveaux commits
- ✅ Bloquer le merge si les tests/CI échouent
- ✅ Forcer la mise à jour avant merge

---

## 🧠 Règles de Fusion (Clean Commit History)

| Source | Cible | Type | Méthode recommandée |
|--------|-------|------|-------------------|
| `feature/*` | `develop` | Squash | ✅ PR avec "Squash and Merge" |
| `fix/*` | `develop` | Squash | ✅ PR avec "Squash and Merge" |
| `hotfix/*` | `main` | Squash | ✅ PR avec "Squash and Merge" (URGENT) |
| `hotfix/*` | `develop` | Squash ou Cherry-pick | 🔁 PR "Squash and Merge" ou cherry-pick après fusion |
| `develop` | `main` | Fast-forward | ✅ PR classique (Create a merge commit) |

### 🔥 Spécificité Hotfix
**Les hotfix doivent aller sur DEUX branches :**
1. **Main d'abord** → Fix immédiat en production
2. **Develop ensuite** → Éviter la régression future



---

## 🚀 Configuration Initiale

### Créer la branche develop à partir de main

```bash
git checkout -b develop main
git push -u origin develop
```

### Recréer develop à partir de main ⚠️
**Attention :** Cette commande remplace complètement l'ancienne develop

```bash
git checkout main
git pull origin main
git checkout -B develop
git push -f origin develop
```

---

## 🔁 Cycle Complet d'une Feature (`feature/*`)

### 🚧 Créer et développer une feature

```bash
# 1. Se baser sur develop
git checkout develop
git pull origin develop

# 2. Créer une branche feature
git checkout -b feature/theme-happy

# 3. Développer et commit
git add .
git commit -m "feat: ajout du thème happy"
git push origin feature/theme-happy
```

### 🔄 Fusionner la feature dans develop via PR

1. **Créer une Pull Request** sur GitHub :
   
   - Source : `feature/theme-happy`
   - Cible : `develop`
   - Type : **Squash and Merge**

2. **Après fusion de la PR**, nettoyer localement :
   
```bash
# Mettre à jour develop
git checkout develop
git pull origin develop

# Supprimer la branche feature locale
git branch -d feature/theme-happy

# La branche remote est automatiquement supprimée par GitHub si configuré
```

### 🔄 Fusionner le fix dans develop via PR

1. **Créer une Pull Request** sur GitHub :
   
   - Source : `fix/form-validation`
   - Cible : `develop`
   - Type : **Squash and Merge**

2. **Après fusion de la PR**
   
```bash
# Mettre à jour develop
git checkout develop
git pull origin develop

# Supprimer la branche fix
git branch -d fix/form-validation
```

---

## 🐛 Cycle Complet d'un Fix (`fix/*`)

### 🔧 Corriger un bug en développement

```bash
# 1. Se baser sur develop
git checkout develop
git pull origin develop

# 2. Créer une branche fix
git checkout -b fix/form-validation

# 3. Corriger le bug
git add .
git commit -m "fix: validation du formulaire"
git push origin fix/form-validation
```

### 🔄 Fusionner le fix dans develop via PR

1. **Créer une Pull Request** sur GitHub :
   
   - Source : `fix/form-validation`
   - Cible : `develop`
   - Type : **Squash and Merge**

2. **Après fusion de la PR** :
   
```bash
# Mettre à jour develop
git checkout develop
git pull origin develop

# Supprimer la branche fix
git branch -d fix/form-validation
```

---

## 🔥 Cycle Complet d'un Hotfix (`hotfix/*`) - DOUBLE APPLICATION

### ⚡ Corriger un bug urgent en production

```bash
# 1. Se baser sur main (production)
git checkout main
git pull origin main

# 2. Créer une branche hotfix
git checkout -b hotfix/header-crash

# 3. Corriger le bug urgent
git add .
git commit -m "hotfix: crash header sur mobile"
git push origin hotfix/header-crash
```

### 🔄 ÉTAPE 1 : Appliquer le hotfix dans main (URGENT)

1. **Créer une Pull Request** sur GitHub :
   
   - Source : `hotfix/header-crash`
   - Cible : `main`
   - Type : **Squash and Merge**
   - **Priorité : URGENT** 🚨

2. **Après fusion dans main** :
   
```bash
git checkout main
git pull origin main
# 🚀 Déploiement immédiat en production
```

### 🔄 ÉTAPE 2 : Appliquer le hotfix dans develop

**Option A : Via Pull Request (recommandé si la branche existe encore)**

1. Créer une PR de `hotfix/header-crash` vers `develop`
2. Type : **Squash and Merge**

**Option B : Cherry-pick manuel (si la branche hotfix est supprimée)**

```bash
# 1. Récupérer le SHA du commit de hotfix dans main
git log --oneline main -n 5
# Exemple : abc1234 hotfix: crash header sur mobile

# 2. Appliquer dans develop
git checkout develop
git pull origin develop
git cherry-pick abc1234
git push origin develop
```

### 🧹 Nettoyage final

```bash
# Supprimer la branche hotfix locale
git branch -d hotfix/header-crash
```

---

## 📦 Déploiement en Production

1. **Créer une Pull Request** sur GitHub :
   
   - Source : `develop`
   - Cible : `main`
   - Type : **Create a merge commit** (pour conserver l'historique)

2. **Après fusion**, tagger la version (optionnel) :
   
```bash
git checkout main
git pull origin main

# Tag pour marquer la version
git tag -a v1.2.0 -m "Release version 1.2.0"
git push origin v1.2.0
```

---

## 🍒 Cherry-pick : Appliquer un Commit Spécifique

Le `cherry-pick` permet de répliquer un commit spécifique d'une branche dans une autre, contrairement au merge qui amène tout l'historique.

### Utilisation typique

```bash
# Voir l'historique des commits
git log --oneline

# Exemple : abc1234 hotfix: bug header
# Appliquer ce commit dans develop
git checkout develop
git cherry-pick abc1234
git push origin develop
```

### Cas d'usage courants

- ✅ Appliquer un hotfix de `main` vers `develop`
- ✅ Récupérer un commit spécifique d'une feature
- ✅ Appliquer un fix critique sans merger toute la branche

---

## 📝 Bonnes Pratiques

### Messages de Commit

```bash
# Format recommandé (Conventional Commits)
feat: nouvelle fonctionnalité
fix: correction de bug
hotfix: correction urgente
docs: mise à jour documentation
style: formatage du code
refactor: refactorisation
test: ajout de tests
chore: tâches de maintenance
```

### Nettoyage des Branches (PowerShell)

```powershell
# Supprimer les branches locales fusionnées
$branches = git for-each-ref --format='%(refname:short)' refs/heads/ |
    Where-Object { $_ -notin @('main', 'develop') }

foreach ($branch in $branches) {
    $mergedInMain = git merge-base --is-ancestor $branch main
    $mergedInDevelop = git merge-base --is-ancestor $branch develop

    if ($LASTEXITCODE -eq 0 -and $mergedInDevelop -eq $true) {
        Write-Host "Deleting merged branch: $branch"
        git branch -d $branch
    }
}

# Supprimer les références aux branches supprimées sur le remote
git remote prune origin
```mote prune origin
```

### Nettoyage des Branches (Bash)

```bash
for branch in $(git for-each-ref --format='%(refname:short)' refs/heads/ | grep -vE '^(main|develop)$'); do
  git merge-base --is-ancestor "$branch" main && \
  git merge-base --is-ancestor "$branch" develop
  if [ $? -eq 0 ]; then
    echo "Deleting merged branch: $branch"
    git branch -d "$branch"
  fi
done

# Supprimer les références aux branches supprimées sur le remote
git remote prune origin
```

### Synchronisation

```bash
# Toujours pull avant de créer une nouvelle branche
git checkout develop
git pull origin develop

# Toujours pull avant de fusionner (pour les cherry-picks)
git checkout develop
git pull origin develop
```

---

## 🎯 Résumé des Commandes Essentielles

| Action | Commande |
|--------|----------|
| Créer une feature | `git checkout -b feature/nom develop` |
| Créer un fix | `git checkout -b fix/nom develop` |
| Créer un hotfix | `git checkout -b hotfix/nom main` |
| Cherry-pick | `git cherry-pick <sha>` |
| Supprimer branche locale | `git branch -d nom` |
| Nettoyer références remote | `git remote prune origin` |
| Voir l'historique | `git log --oneline` |
| Supprimer localement et distant | `git branch -d <nom_de_branche> && git push origin --delete <nom_de_branche>`|

## 🔄 Workflow avec Pull Requests - Résumé

### Processus Standard

1. **Développement** : Créer une branche → Développer → Push
2. **Integration** : Créer PR → Review → Merge via GitHub
3. **Nettoyage** : Pull les changements → Supprimer branche locale

### Processus Hotfix (Spécial)

1. **Développement** : Créer hotfix → Corriger → Push
2. **Production** : PR vers `main` → Merge URGENT → Déploiement
3. **Develop** : PR vers `develop` OU Cherry-pick
4. **Nettoyage** : Supprimer branche locale

## ⚡ Règles d'Or

- 🚫 **Jamais de `git push` direct** sur `main` ou `develop`
- ✅ **Toujours passer par les Pull Requests**
- 🔄 **Hotfix = Double application** (`main` + `develop`)
- 🧹 **Nettoyer ses branches** après fusion
- 📝 **Messages de commit clairs** (Conventional Commits)

---
