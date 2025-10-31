# Atelier-DevOps-Niveau01

1. Créer un fichier 'index.html' avec un contenu de base.
2. Créer un dossier '.github' contenant lui même un dossier workflows
3. Créer un fichier 'afficher.yml' qui sera notre premier workflow

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

4. Créer un fichier 'linter.yml' qui sera notre second workflow
 
