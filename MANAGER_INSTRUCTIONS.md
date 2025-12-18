# Instructions pour le Manager - Démonstration du Problème de Checkout

## Problème Identifié

Dans `ArmoniK.Core/.github/workflows/build.yml`, **tous les checkouts** utilisent:
```yaml
- uses: actions/checkout@v4
  with:
    ref: ${{ github.ref }}
```

Cela empêche les workflows de fonctionner correctement sur les pull requests.

## Preuve du Problème

Ce repository contient une démonstration qui montre **côte à côte** :
1. ❌ Le comportement **AVEC** `ref: ${{ github.ref }}` (problématique)
2. ✅ Le comportement **SANS** `ref` (correct)

## Comment Tester

### Étape 1: Pusher ce Repository
```bash
cd testcheckout
git commit -m "Add demonstration of checkout issue"
git push origin main
```

### Étape 2: Créer une Pull Request de Test
```bash
# Créer une nouvelle branche
git checkout -b test-pr

# Modifier le fichier de test
echo "PR modification test" >> test-file.txt

# Commit et push
git add test-file.txt
git commit -m "Test PR modification"
git push origin test-pr
```

### Étape 3: Ouvrir la PR sur GitHub
- Aller sur https://github.com/ereali-aneo/testcheckout
- Créer une Pull Request de `test-pr` vers `main`

### Étape 4: Observer les Résultats
- Aller dans l'onglet "Actions" de la PR
- Ouvrir le workflow "Demo - Checkout Issue with ref"
- **Comparer les deux jobs :**
  - `checkout-with-explicit-ref` (avec ref - PROBLÉMATIQUE)
  - `checkout-without-ref` (sans ref - CORRECT)

## Ce que Vous Verrez

### Job AVEC `ref: ${{ github.ref }}` :
- Peut échouer ou montrer un comportement incorrect
- Ne récupère pas le bon code sur les PRs
- `github.ref` = `refs/pull/X/merge` (référence incorrecte)

### Job SANS `ref` :
- ✅ Fonctionne correctement
- GitHub Actions gère automatiquement le merge commit
- Récupère le code PR + main mergé correctement

### Job "Explanation" :
- Affiche un résumé complet du problème
- Explique pourquoi le comportement est différent

## Solution pour ArmoniK.Core

**Supprimer toutes les lignes `ref: ${{ github.ref }}` dans `.github/workflows/build.yml`**

Remplacer:
```yaml
- uses: actions/checkout@v4
  with:
    ref: ${{ github.ref }}
    submodules: true
```

Par:
```yaml
- uses: actions/checkout@v4
  with:
    submodules: true
```

## Sécurité

Cette démonstration prouve aussi que:
- Les secrets GitHub ne sont **PAS** exposés aux fork PRs
- C'est un comportement sécurisé par défaut de GitHub Actions
- Les fork PRs utilisent les runners GitHub (pas nos machines)

## Questions?

Si vous avez besoin de plus de détails, vous pouvez :
1. Lire le README.md de ce repository
2. Consulter la documentation GitHub Actions : https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request
3. Examiner les logs détaillés des jobs dans GitHub Actions
