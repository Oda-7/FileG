# gitWorkflow

# 🧩 Git Workflow – Guide Complet pour Projet Solo

## 📁 Structure des Branches

| Branche      | Description                                    |
|--------------|------------------------------------------------|
| `main`       | Code **en production** (stable, déployé)      |
| `develop`    | Code **intégré en cours de développement**    |
| `feature/*`  | Développement de **nouvelles fonctionnalités** |
| `fix/*`      | Correction de **bug non critique** en dev     |
| `hotfix/*`   | Correction de **bug urgent** en production    |
| `release/*`  | *(optionnel)* Préparation d'une **version**   |

---

## 🧠 Règles de Fusion (Clean Commit History)

| Source       | Cible    | Type                  | Méthode recommandée               |
|--------------|----------|-----------------------|------------------------------------|
| `feature/*`  | develop  | Squash                | ✅ PR avec "Squash and Merge" ou `merge --squash` |
| `fix/*`      | develop  | Squash                | ✅ PR avec "Squash and Merge" ou `merge --squash` |
| `hotfix/*`   | main     | Squash                | ✅ PR "Squash and Merge" ou `merge --squash` |
| `hotfix/*`   | develop  | Squash ou Cherry-pick | 🔁 `merge --squash` ou `git cherry-pick` |
| `develop`    | main     | Fast-forward          | `git merge --ff` ou PR classique   |

---

## 🚀 Configuration Initiale

### Créer la branche `develop` à partir de `main`

```bash
git checkout -b develop main
git push -u origin develop
```

### Recréer `develop` à partir de `main` (⚠️ Attention : remplace complètement l'ancienne develop)

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

### 🔄 Fusionner la feature dans `develop`

```bash
# 4. Fusionner avec squash (historique propre)
git checkout develop
git pull origin develop
git merge --squash feature/theme-happy
git commit -m "feat: ajout du thème happy"
git push origin develop

# 5. Supprimer la branche feature
git branch -d feature/theme-happy
git push origin --delete feature/theme-happy
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

# 4. Fusionner dans develop
git checkout develop
git pull origin develop
git merge --squash fix/form-validation
git commit -m "fix: validation du formulaire"
git push origin develop

# 5. Supprimer la branche fix
git branch -d fix/form-validation
git push origin --delete fix/form-validation
```

---

## 🔥 Cycle Complet d'un Hotfix (`hotfix/*`)

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

### 🔄 Appliquer le hotfix dans `main`

```bash
# 4. Fusionner dans main avec squash
git checkout main
git pull origin main
git merge --squash hotfix/header-crash
git commit -m "hotfix: crash header sur mobile"
git push origin main
```

### 🔄 Appliquer le hotfix dans `develop`

```bash
# 5. Appliquer aussi dans develop (cherry-pick ou squash)
git checkout develop
git pull origin develop

# Option A : Cherry-pick (récupère le commit exact)
git cherry-pick <sha-du-commit-hotfix> # commit ajouter et push dans main
git push origin develop

# Option B : Merge squash
git merge --squash hotfix/header-crash
git commit -m "hotfix: crash header sur mobile"
git push origin develop

# 6. Supprimer la branche hotfix
git branch -d hotfix/header-crash
git push origin --delete hotfix/header-crash
```

---

## 📦 Déploiement en Production

### 🚀 Fusionner `develop` dans `main`

```bash
# Quand develop est stable et prêt
git checkout main
git pull origin main
git merge develop  # ou git merge --ff develop
git push origin main

# Tag optionnel pour marquer la version
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

- Appliquer un hotfix de `main` vers `develop`
- Récupérer un commit spécifique d'une feature
- Appliquer un fix critique sans merger toute la branche

---

## 📝 Bonnes Pratiques

### Messages de Commit

```bash
# Format recommandé
feat: nouvelle fonctionnalité
fix: correction de bug
hotfix: correction urgente
docs: mise à jour documentation
style: formatage du code
refactor: refactorisation
test: ajout de tests
```

### Nettoyage des Branches

```bash
# Supprimer les branches locales fusionnées
git branch --merged | grep -v "\*\|main\|develop" | xargs -n 1 git branch -d

# Supprimer les références aux branches supprimées sur le remote
git remote prune origin
```

### Synchronisation

```bash
# Toujours pull avant de créer une nouvelle branche
git checkout develop
git pull origin develop

# Toujours pull avant de fusionner
git checkout develop
git pull origin develop
git merge --squash feature/ma-feature
```

---

## 🎯 Résumé des Commandes Essentielles

| Action | Commande |
|--------|----------|
| Créer une feature | `git checkout -b feature/nom develop` |
| Créer un fix | `git checkout -b fix/nom develop` |
| Créer un hotfix | `git checkout -b hotfix/nom main` |
| Squash merge | `git merge --squash branche` |
| Cherry-pick | `git cherry-pick <sha>` |
| Supprimer branche | `git branch -d nom && git push origin --delete nom` |
| Deploy production | `git checkout main && git merge develop` |
