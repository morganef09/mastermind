# Consignes du projet

- Lire `README.md` avant de modifier le jeu. Il décrit les règles, le contrat de test et les exemples attendus.
- Conserver l’apparence actuelle et les règles du jeu. Ne les modifier que si la demande le précise clairement.
- L’interface visible et `window.codeSecretTest` doivent partager le même état et le même calcul de score. Ne pas créer un moteur parallèle pour les tests.
- Respecter le contrat API 0.2 décrit dans `README.md` : activation uniquement par `?test=1`, noms de couleurs canoniques, validation et priorités d’erreur, états copiés en JSON, aucune propriété `secret` dans l’état public.
- Ne pas ajouter de service serveur, de clé d’accès ou de données personnelles.
- En cas de conflit ou d’ambiguïté entre une demande et les règles documentées, la signaler avant de modifier le comportement concerné.
- Limiter chaque changement à la demande en cours. Après une modification, examiner les fichiers touchés et rapporter précisément les vérifications réellement effectuées, leurs résultats et leurs limites.
- Mettre à jour `README.md` quand une règle, une décision du projet, le contrat ou les résultats de vérification changent. Ne jamais présenter un test prévu comme un test exécuté.

