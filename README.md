# Atelier-DevOps-Niveau01

1. Créer un fichier 'index.html' avec un contenu de base.
2. Créer un dossier '.github' contenant lui même un dossier workflows.
3. Créer un fichier 'afficher.yml' qui sera notre premier workflow.

_Un workflow, aussi appelé flux de travaux ou flux opérationnel, désigne une suite de tâches ou d'opérations qui doivent être réalisées_
  
```yml
name: Afficher des trucs

on:
  push:
    branches: [ "main" ]  # se déclenche à chaque push sur la branche main

env:
    WOLOLO: blabla

jobs:

  afficher-texte:
    runs-on: ubuntu-latest
    steps:
      - name: Afficher un message simple
        run: echo "Hello DevOps 🚀"

      - name: Afficher plusieurs lignes
        run: |
          echo "=== Début du script ==="
          echo "Étape 1 : préparation"
          echo "Étape 2 : déploiement"
          echo "=== Fin du script ==="

      - name: Texte colore
        run: |
            echo -e "\033[32mSuccès !\033[0m"
            echo -e "\033[31mErreur !\033[0m"

      - name: Messages spéciaux
        run: |
            echo "::notice::Ceci est une note"
            echo "::warning::Attention, vérifie la configuration"
            echo "::error::Une erreur s’est produite"

      - name: Afficher des variables d'environnement
        env:
          MON_MESSAGE: "Je vais afficher une variable d'environnement"
        run: echo "$MON_MESSAGE"

      - name: Afficher coucou si WOLOLO est défini
        if: env.WOLOLO == 'blabla'
        run: echo "coucou"
```

4. Créer un fichier 'linter.yml' qui sera notre second workflow pour linter le html
5. Installer un linter HTML qui à besoin de node :

```bash
npm install htmlhint --save-dev
```

6. Ajouter un .gitignore pour ne pas remonter n'importe quoi dans le repo.

7. Ajouter un fichier de configuration du linter nommé '.htmlhintrc'.

8. Regarder la documentation du linter.

9. Ecrire le second workflow 'linter.yml'.

```yml
name: Lints

on: [push, pull_request]

jobs:
  lint-html:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout du code
        uses: actions/checkout@v4

      - name: Installer Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Installer les dépendances
        run: npm ci

      - name: Lancer le lint HTML
        run: npx htmlhint "**/*.html"
```

10. Créer un troisième workflow 'test.yml'

11. Créer un dossier test pour mettre les tests

12. Créer un premier fichier de tests nommé example.test.js

```js
// tests/example.test.js
test('1 + 1 should equal 2', () => {
  expect(1 + 1).toBe(2);
});
```

13. Installer jest.js

```
npm install jest
```

14. Ajouter un fichier de configuration du testeur nommé 'jest.config'.

```js
// jest.config.js
export default {
  testEnvironment: "node",
};
```

15. Ajouter ce script dans 'package.json'  

```json
"scripts": {
    "test":"jest"
  }
```

16. Créer un quatrième workflow 'deployer.yml'

17. On va déployer sur github pages

* ✅ Étapes pour activer le déploiement via GitHub Actions
* Va dans ton dépôt → Settings → Pages
* Dans Build and deployment, ouvre le menu déroulant Source
* Choisis “GitHub Actions” au lieu de “Deploy from a branch”
* Clique sur Save


17. Contenu :

```yml
# ======================================================================================
# Nom du workflow tel qu’il apparaîtra dans l’onglet “Actions” de GitHub
# ======================================================================================
name: Deploy static site to GitHub Pages

# ======================================================================================
# SECTION : Déclencheur du workflow
# Ici, le workflow sera exécuté automatiquement à chaque "push" sur la branche "main".
# Autrement dit, dès que tu fais un "git push origin main", GitHub lancera ce workflow.
# ======================================================================================
on:
  push:
    branches: ["main"]  # La liste des branches concernées par le déclenchement

# ======================================================================================
# SECTION : Permissions du workflow
# Ces permissions contrôlent ce que le workflow est autorisé à faire.
# - contents: read → il peut lire le contenu du dépôt (pour copier les fichiers).
# - pages: write → il peut écrire sur GitHub Pages (pour déployer le site).
# - id-token: write → nécessaire pour authentifier le déploiement de manière sécurisée.
# ======================================================================================
permissions:
  contents: read
  pages: write
  id-token: write

# ======================================================================================
# SECTION : Concurrence
# Ce bloc permet d’éviter que plusieurs déploiements s’exécutent en même temps.
# - group: "pages" → nom symbolique du groupe (ici “pages” pour GitHub Pages)
# - cancel-in-progress: true → si un nouveau déploiement démarre, il annule le précédent.
# Cela évite les conflits ou les déploiements multiples inutiles.
# ======================================================================================
concurrency:
  group: "pages"
  cancel-in-progress: true

# ======================================================================================
# SECTION : Définition des jobs (tâches)
# Un “job” est un ensemble d’étapes exécutées sur un environnement donné.
# Ici, on n’a qu’un seul job nommé “deploy”.
# ======================================================================================
jobs:
  deploy:  # Nom du job

    # ------------------------------------------------------------------------------
    # Environnement cible
    # - name: github-pages → l’environnement logique associé au déploiement
    # - url: référence à l’URL de ton site générée automatiquement par GitHub Pages
    #   (elle sera visible dans la console une fois le déploiement terminé)
    # ------------------------------------------------------------------------------
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}

    # ------------------------------------------------------------------------------
    # Système sur lequel le job va tourner
    # “ubuntu-latest” = une machine virtuelle Linux Ubuntu, maintenue par GitHub
    # ------------------------------------------------------------------------------
    runs-on: ubuntu-latest

    # ------------------------------------------------------------------------------
    # SECTION : Steps (étapes du job)
    # Chaque étape est exécutée dans l’ordre, sur la même machine virtuelle.
    # ------------------------------------------------------------------------------
    steps:
      # ============================================================================
      # ÉTAPE 1 : Récupérer le code du dépôt
      # ----------------------------------------------------------------------------
      # uses: actions/checkout@v4 → action officielle de GitHub qui clone ton dépôt
      # dans l’environnement de travail du workflow (la VM Ubuntu).
      # Sans cette étape, GitHub n’aurait aucun fichier à déployer.
      # ============================================================================
      - name: Checkout code
        uses: actions/checkout@v4

      # ============================================================================
      # ÉTAPE 2 : Préparer les fichiers à déployer
      # ----------------------------------------------------------------------------
      # uses: actions/upload-pages-artifact@v3 → action officielle qui crée un “artifact”.
      # Un artifact est un paquet temporaire (zip) contenant les fichiers à publier.
      #
      # with:
      #   path: . → indique le dossier à inclure dans le déploiement.
      #   Ici, le point (.) signifie la racine du dépôt.
      #   Comme ton index.html est directement à la racine, c’est parfait.
      #
      # Si ton site se trouvait dans un sous-dossier (ex: "dist" ou "build"),
      # tu mettrais "path: ./dist" ou "path: ./build"
      # ============================================================================
      - name: Upload static files
        uses: actions/upload-pages-artifact@v3
        with:
          path: .  # Ton fichier index.html et les éventuels CSS/JS sont à la racine

      # ============================================================================
      # ÉTAPE 3 : Déploiement sur GitHub Pages
      # ----------------------------------------------------------------------------
      # uses: actions/deploy-pages@v4 → action officielle qui récupère l’artifact
      # créé à l’étape précédente et le publie automatiquement sur GitHub Pages.
      #
      # id: deployment → identifiant de l’étape pour y faire référence plus haut
      # (dans environment.url: ${{ steps.deployment.outputs.page_url }})
      #
      # Une fois exécutée :
      # - GitHub Pages génère ton site
      # - l’URL de ton site est affichée dans la console
      # - tu peux le visiter à :
      #   https://<ton-username>.github.io/<nom-du-repo>/
      # ============================================================================
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

18. https://wololobzh.github.io/Atelier-DevOps-Niveau01/