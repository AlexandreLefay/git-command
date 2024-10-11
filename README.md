# Setting Up SSH Key and Adopting a Git Rebase Workflow

Dans cette documentation, je vais expliquer comment configurer une clé SSH pour GitHub et comment adopter un workflow basé sur **Git rebase**. L'objectif est de travailler efficacement sur une branche personnelle, rebasing régulièrement cette branche sur `main` ou `dev`, tout en gardant un historique propre avec des commits squashés.

---

## 🔐 **1. Générer une clé SSH pour GitHub**

Avant toute chose, il est important de configurer une clé SSH pour éviter d'avoir à entrer son mot de passe à chaque fois et sécuriser les connexions.

### Étapes pour créer une clé SSH :

1. **Générer une nouvelle clé SSH** :
   
   Utilise cette commande pour générer une clé de type **ED25519** (plus rapide et plus sécurisée que RSA) :

   ```bash
   ssh-keygen -t ed25519 -C "ton_email@example.com"
   ```

2. **Ajouter la clé SSH à l'agent SSH** :

   Ensuite, il faut ajouter la clé SSH générée à l'agent pour qu'elle soit utilisée automatiquement :

   ```bash
   eval $(ssh-agent -s)
   ssh-add ~/.ssh/id_ed25519
   ```

---

## 📋 **2. Afficher et copier la clé SSH publique**

Une fois la clé générée, il faut l’ajouter à GitHub. Pour cela, on doit afficher la clé publique et la copier :

```bash
cat ~/.ssh/id_ed25519.pub
```

Cette commande affiche ta clé publique dans le terminal. Il te suffit de la copier.

> **Astuce :** Sur Windows, tu peux utiliser cette commande pour copier directement dans le presse-papier :
> ```bash
> cat ~/.ssh/id_ed25519.pub | clip
> ```

---

## 🔗 **3. Ajouter la clé publique à GitHub**

Maintenant que tu as copié ta clé publique, il faut l’ajouter à GitHub :
1. Va sur [GitHub SSH settings](https://github.com/settings/keys).
2. Clique sur **New SSH Key**.
3. Colle la clé dans le champ **Key** et donne-lui un nom.

---

## ✅ **4. Tester la connexion SSH**

Pour vérifier que tout est correctement configuré, tu peux tester la connexion SSH avec GitHub en utilisant la commande suivante :

```bash
ssh -T git@github.com
```

Si tout est en place, tu devrais voir un message du type :

```bash
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

# 🚀 **Adoption du workflow Git rebase**

Maintenant que l’accès SSH est configuré, parlons du workflow que je veux adopter en utilisant **Git rebase**.

L'objectif est de travailler sur une branche personnelle, puis de la **rebase** régulièrement sur la branche `main` ou `dev`. Cela permet d'intégrer les dernières modifications de la branche principale tout en gardant un historique de commits propre.

### 💡 Pourquoi utiliser `git rebase` ?

- **Historique propre** : Avec `rebase`, je garde mon historique linéaire, ce qui est beaucoup plus lisible que des merges avec des branches multiples.
- **Squash des commits** : Avant de pousser mes changements, je préfère souvent "squasher" (combiner) mes commits pour n’en garder qu’un seul ou quelques-uns significatifs. Cela permet d'éviter des commits intermédiaires sans intérêt.

### 🚧 **Mon workflow Git Rebase :**

1. **Rebase interactif pour squasher les commits** :

   Avant de pousser les changements sur ma branche personnelle, je fais un rebase interactif sur les X derniers commits pour les fusionner en un seul (ou un nombre réduit) :

   ```bash
   git rebase -i HEAD~15
   ```

   Cela me permet de **squasher** tous les petits commits en un seul plus propre. Je choisis `squash` ou `fixup` pour les commits que je veux fusionner avec le précédent.

2. **Amender le dernier commit si nécessaire** :

   Si je veux changer le dernier commit (par exemple, améliorer le message ou ajouter des fichiers), j'utilise la commande suivante :

   ```bash
   git commit --amend
   ```

3. **Rebase de ma branche sur `main` ou `dev`** :

   Une fois que mon historique est propre, je veux intégrer les dernières modifications de `main` ou `dev` dans ma branche perso. Pour cela, je fais un rebase de ma branche :

   ```bash
   git fetch origin
   git rebase origin/main
   ```

   Cela va replacer mes commits au-dessus de la branche `main` tout en prenant en compte les derniers changements.

4. **Force-push de ma branche** :

   Après un rebase, il est nécessaire de **force-push** ma branche perso, car l'historique a été modifié. Le `force-push` remplace l’historique de la branche distante avec l'historique local modifié :

   ```bash
   git push origin ma_branche --force
   ```

   > **Pourquoi `--force` ?**  
   > Lorsque tu réécris l’historique (par exemple avec `rebase` ou `amend`), ton dépôt local n'est plus en synchronisation avec le dépôt distant. Un `push` normal échouerait, car Git détecterait un historique différent. C'est pourquoi on utilise `--force` pour écraser l'historique distant avec celui que tu as localement.

---

### ⚠️ **Attention avec le `--force` :**

- Le `force-push` doit être utilisé avec précaution, notamment si tu travailles en équipe, car il réécrit l’historique partagé. Assure-toi que personne n’a encore récupéré tes commits avant de faire un rebase ou de pousser avec `--force`.

---

## 🎉 **Résumé du workflow**

1. Je travaille sur ma branche perso.
2. Je fais un **rebase interactif** pour squasher mes commits et garder un historique propre.
3. Je rebase ma branche sur `main` ou `dev` pour intégrer les dernières modifications.
4. Je **force-push** ma branche perso après un rebase pour mettre à jour le dépôt distant.

Avec ce workflow, je garde un historique clair et linéaire, sans commits inutiles, tout en synchronisant régulièrement mon travail avec la branche principale.

