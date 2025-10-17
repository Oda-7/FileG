# gitWorkflow

# 🧩 Git Workflow – Guide Complet 

## 🎯 Résumé des Commandes Essentielles

| Action | Commande |
|--------|----------|
| Initialiser un projet | `git init`|
| Indexé le projet / Indexé le fichier / Indexer les fichier .ts du dossier | `git add . / git add <file> / git add Dossier/\*.ts` |
| Voir tous les fichier qui seront commités| `git status`|
| Créer un commit et ajouter un message de commit | `git commit -m "message du commit"` |
| Modifier le dernier commit avant de push pour y ajouter des changement | `git add . ` `git commit --amend` |
| Lister la liste des fichiers suivie | `git ls-files --cached`|
| Mettre les modification d'une branche de coté, rajouter le flag -u pour les fichier non suivie | `git stash -u save "description"` | 
| Pousser sur le dépot distant (creation de la branche, '-u' tracking de la branch ajouter), une fois le suivie de branche ajouter il suffit de faire la commande git push ou git pull  | `git push -u origin <branch>` |
| Récupère et fusionne les changement distant | `git pull origin <branch>` |
| Restaure notre fichier au dernier commit s'il est suivie | `git restore <file>` |
| Voir les branches locales et distantes | `git branch -a` |
| Créer une branche | `git checkout -b <branch>` |
| Changer de branche | `git switch <branch>` |
| Supprimer branche locale | `git branch -d <name-branch>` |
| Compare les branches distantes avec les branches locals (": gone" signifie que la branche distante n'existe plus) | `git branch -vv`|
| Forcer la branche local à être identique à la branche distante |`git reset --hard origin/feature/theme-happy`|
| Récupère la mise a jour des branche distante et supprime les branche supprimer sur github | `git fetch -p`|
| Nettoyer les branche supprimer sur github | `git remote prune origin` |
| Supprimer localement et distant | `git branch -d <nom_de_branche>; git push origin --delete <nom_de_branche>`|
| Voir l'historique | `git log` |
| Voir l'historique en couleur, avec la date,chaque commit sera sur une ligne | `git log --graph --all --decorate --pretty=format:'%C(yellow)%h%C(reset) %C(green)%ad%C(reset) | %C(red)%d%C(reset) %s %C(bold blue)<%an>%C(reset)' --date=short` |
| Annuler un commit déjà poussé sans réecrire l'historique et permet au autres utilisateur d'avoir les modification lors du prochain pull ou fetch | `git revert <hash-commit>` |
| Pendant un merge, revenir à l'état avant le merge/pull | `git merge --abort`|
| Réinitialise la branche local au dernier commit | `git reset --hard HEAD` |
| Remplace complètement ton état local avec la branche distante | `git reset --hard origin/feature/<nom de la branche>` |
| Prend tous les fichiers commit sur la branche et les ajoute sur la branche courante ( -- : obligatoire pour que git comprenne que l'on veut prendre les fichier de la branch, . : le réperertoire courante ) |  `git checkout <branche> -- .` |


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

### **📝 Bonnes Pratiques**

## Types de message de Commit

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

```Bash
git commit -m" <type> ( <portée facultative> ): <description> " \
  -m" <corps facultatif> " \
  -m" <pied de page facultatif> "
```

Exemple : 

```Bash

git commit -m"feat!: remove ticket list endpoint" \
  -m"refers to JIRA-1337" \
  -m"BREAKING CHANGE: ticket endpoints no longer supports list all entities."
```

---

### **🛡️ Protection des Branches**

**Important :** Les branches `main` et `develop` sont protégées et ne peuvent pas être modifiées directement via `git push` ou `git merge`. Toutes les modifications doivent passer par des Pull Requests (PR) sur GitHub.

### Configuration GitHub recommandée :
- ✅ Exiger une review de PR avant merge
- ✅ Invalider les anciennes reviews si nouveaux commits
- ✅ Bloquer le merge si les tests/CI échouent
- ✅ Forcer la mise à jour avant merge

<img width="1143" height="248" alt="image" src="https://github.com/user-attachments/assets/ab5754d8-3f39-499b-ab58-de4d6bb4ec05" />
<img width="784" height="845" alt="image" src="https://github.com/user-attachments/assets/972fc650-85fd-4056-bf34-09347221ac95" />

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
   
   - Source : `feature/`
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

## 🔥 Cycle Complet d'un Hotfix (`hotfix/*`) - TRIPLE OPTION
## ⚡ Corriger un bug urgent en production

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

## 🔄 ÉTAPE 2 : Synchroniser le hotfix dans develop

---

### **Pull Request avec Squash** ⭐ *Recommandée*

**Prérequis :** La branche hotfix existe encore

**Processus :**
1. Créer une PR de `hotfix/header-crash` vers `develop`
2. Type : **Squash and Merge**
3. Valider et merger

#### ✅ Avantages
- Process standardisé (cohérent avec les autres merges)
- Code review possible avant intégration dans develop
- Historique propre (1 seul commit dans develop)
- Traçabilité complète via l'interface GitHub
- Suppression automatique de la branche hotfix (si configuré)
- Pas de risque de casser develop

#### ❌ Inconvénients
- Nécessite que la branche hotfix existe encore (non supprimée)
- Prend plus de temps (processus de PR + review)
- Crée un nouveau SHA (commit dupliqué entre main et develop)
- Nécessite une connexion internet et accès à GitHub

#### 🎯 Quand l'utiliser ?
- ✅ Vous êtes débutant ou en apprentissage
- ✅ La branche hotfix existe encore
- ✅ Vous voulez une review du code
- ✅ Vous privilégiez la sécurité et la traçabilité

---


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
