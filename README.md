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