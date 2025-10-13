# gitWorkflow

# 🧩 Git Workflow – Guide Complet 

## 🎯 Résumé des Commandes Essentielles

| Action | Commande |
|--------|----------|
| voir tous les fichier qui seront commités| `git status`|
| Lister la liste des fichiers trackés| `git ls-files --cached`|
| Créer une feature | `git checkout -b feature/nom develop` |
| Créer un fix | `git checkout -b fix/nom develop` |
| Créer un hotfix | `git checkout -b hotfix/nom main` |
| Cherry-pick | `git cherry-pick <sha>` |
| Supprimer branche locale | `git branch -d nom` |
| Nettoyer références remote | `git remote prune origin` |
| Voir l'historique | `git log --oneline` |
| Supprimer localement et distant | `git branch -d <nom_de_branche>; git push origin --delete <nom_de_branche>`|
| Supprime toute les branches local qui sont inexistante sur le distant | `git fetch -p`|
| Compare les branches distantes avec les branches locals (": gone" signifie que la branche distante n'existe plus) | `git branch -vv`|
| Forcer la branche local à être identique à la branche distante |`git reset --hard origin/feature/theme-happy`|
| Pendant un merge, revenir à l'état avant le merge/pull | `git merge --abort`|
| Réinitialise la branche local au dernier commit | `git reset --hard HEAD` |
| Remplace complètement ton état local avec la branche 'feature' | `git reset --hard origin/feature/<nom de la branche>` |
| Ramène-moi le contenu de <branch> dans ce répertoire, mais reste sur la branche actuelle. (-- : sépare les noms de fichiers ou répertoires des options ou de la branche. Ici, c’est obligatoire pour que Git comprenne qu’on veut prendre des fichiers depuis <branch>, '.' : le répertoire courant) | `git checkout <branche> -- .` |


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

## 🔄 ÉTAPE 2 : Synchroniser le hotfix dans develop

Vous avez **4 options** pour appliquer le hotfix dans develop. Choisissez selon votre contexte et niveau d'expertise.

---

### **Option A : Pull Request avec Squash** ⭐ *Recommandée*

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

### **Option B : Cherry-pick manuel** 🚀 *Rapide*

**Prérequis :** Connaître le SHA du commit à appliquer

**Processus :**
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

#### ✅ Avantages
- **Très rapide** (pas besoin de créer une PR)
- Fonctionne **même si la branche hotfix est supprimée**
- Contrôle précis sur les commits à appliquer
- Idéal pour 1 ou 2 commits spécifiques
- Historique clair dans develop (on voit exactement quel commit vient de main)
- Pas besoin d'accès à l'interface GitHub

#### ❌ Inconvénients
- Crée de nouveaux SHA (commits dupliqués entre main et develop)
- Pas de code review automatique
- Plus de travail manuel (trouver le SHA, taper les commandes)
- Peut causer des conflits de merge futurs si main et develop divergent beaucoup
- Nécessite de comprendre ce qu'est un SHA

#### 🎯 Quand l'utiliser ?
- ✅ La branche hotfix a déjà été supprimée
- ✅ Vous devez synchroniser rapidement (urgence)
- ✅ Le hotfix ne contient que 1-2 commits
- ✅ Vous êtes à l'aise avec la ligne de commande

---

### **Option C : Rebase simple** ⚡ *Avancé - Dangereux*

**Prérequis :** Maîtrise excellente de Git rebase

**Processus :**
```bash
git checkout develop
git pull origin develop
git rebase main
git push --force origin develop  # ⚠️ FORCE PUSH REQUIS
```

#### ✅ Avantages
- Historique **parfaitement linéaire** (pas de merge commits)
- Pas de commits de merge parasites
- Les SHA de main sont préservés dans develop
- Simple à exécuter (3 commandes seulement)
- Résultat "propre" visuellement dans l'historique

#### ❌ Inconvénients
- **⚠️ DANGER** : Réécrit complètement l'historique de develop
- Nécessite un `git push --force` (très dangereux sur branche partagée)
- **Très complexe** en cas de conflits multiples à résoudre
- Peut créer des problèmes graves pour toute l'équipe si develop est partagé
- Si mal utilisé, peut **perdre des commits** de l'équipe
- Nécessite que TOUTE l'équipe fasse `git pull --rebase` ensuite
- Impossible à annuler une fois pushé

#### 🎯 Quand l'utiliser ?
- ✅ Vous êtes **seul** sur develop (pas d'équipe qui travaille dessus)
- ✅ Vous maîtrisez **parfaitement** `git rebase`
- ✅ develop n'a pas divergé de main (peu de commits différents)
- ❌ **JAMAIS** sur une branche partagée avec une équipe

> **🚨 ATTENTION CRITIQUE** : Ne PAS utiliser cette option si vous travaillez en équipe ou si vous n'êtes pas 100% sûr de ce que vous faites !

---

### **Option D : Rebase avec branche temporaire** 🛡️ *Sécurisé + Avancé*

**Prérequis :** Bonne compréhension de Git rebase

**Processus :**
```bash
# 1. S'assurer que develop est à jour
git checkout develop
git pull origin develop

# 2. Créer une branche temporaire depuis develop
git checkout -b temp-hotfix-sync

# 3. Rebase main sur la branche temporaire
git rebase main

# 4. Vérifier que tout est correct
git log --oneline --graph

# 5. Fast-forward develop avec les nouveaux commits
git checkout develop
git merge temp-hotfix-sync

# 6. Pousser develop
git push origin develop

# 7. Nettoyer la branche temporaire
git branch -d temp-hotfix-sync
```

#### ✅ Avantages
- Historique **parfaitement linéaire** (comme Option C)
- **Plus sécurisé** que le rebase direct (develop n'est jamais touchée avant validation)
- Permet de **tester avant** de modifier develop
- Les SHA de main sont préservés dans develop
- **Possibilité d'annuler** sans conséquence (supprimer temp-hotfix-sync)
- Pas de force push nécessaire (si pas de conflits)

#### ❌ Inconvénients
- **Plus complexe** (7 étapes au lieu de 3)
- Temps d'exécution plus long
- Nécessite une **très bonne compréhension** du rebase
- Gestion des conflits peut être difficile
- Plus de commandes à mémoriser et à exécuter
- Risque d'erreur dans l'ordre des étapes

#### 🎯 Quand l'utiliser ?
- ✅ Vous voulez un historique linéaire **mais en sécurité**
- ✅ Vous voulez pouvoir tester/valider avant de toucher develop
- ✅ Vous maîtrisez git rebase mais voulez une sécurité supplémentaire
- ✅ Vous travaillez en équipe mais êtes le seul à gérer les hotfixes

---

## 📊 Tableau comparatif détaillé

| Critère | Option A<br>(PR Squash) | Option B<br>(Cherry-pick) | Option C<br>(Rebase simple) | Option D<br>(Rebase safe) |
|---------|-------------------------|---------------------------|----------------------------|---------------------------|
| **Simplicité** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ | ⭐ |
| **Rapidité** | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |
| **Sécurité** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐ |
| **Historique propre** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Traçabilité** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Collaboration équipe** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐ | ⭐⭐⭐ |
| **Annulation possible** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐ |
| **Besoin GitHub** | ✅ Oui | ❌ Non | ❌ Non | ❌ Non |
| **Force push requis** | ❌ Non | ❌ Non | ✅ Oui | ❌ Non |
| **Niveau requis** | 🎓 Débutant | 👨‍💻 Intermédiaire | 🚀 Expert | 🔧 Avancé |

---

## 💡 Recommandation par profil et contexte

### 🎓 **Étudiants / Débutants**
**→ Option A (PR Squash)**
- La plus sûre et standard
- Vous apprendrez les bonnes pratiques
- Impossible de casser le projet

### 👨‍💻 **Développeurs confirmés**
**→ Option B (Cherry-pick)**
- Rapide et efficace
- Bon compromis sécurité/rapidité
- Fonctionne dans tous les cas

### 🚀 **Experts Git (solo)**
**→ Option C (Rebase simple)**
- Historique parfait
- Mais seulement si vous êtes seul
- JAMAIS en équipe

### 👥 **Équipe expérimentée**
**→ Option D (Rebase safe)**
- Historique linéaire + sécurité
- Pour équipes Git avancées
- Compromis idéal

### ⏰ **Situation d'urgence**
**→ Option B (Cherry-pick)**
- Le plus rapide à exécuter
- Pas besoin de PR
- Réversible facilement

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
