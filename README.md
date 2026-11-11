# Code secret en couleurs

Jeu de Mastermind autonome, exécuté dans le navigateur à partir de `index.html`. L’interface et l’API de test utilisent le même moteur de partie.

## Règles du jeu

- Le code contient quatre couleurs choisies parmi rouge, bleu, vert, jaune, orange et violet.
- Une couleur peut apparaître plusieurs fois.
- Le joueur dispose de dix propositions.
- Un pion noir signifie bonne couleur et bonne position. Un pion blanc signifie bonne couleur, mais mauvaise position. Une occurrence n’est comptée qu’une fois.
- Quatre pions noirs font gagner, même à la dixième proposition. Sans victoire après dix propositions, la partie est perdue et l’interface révèle le code.
- Dans l’interface, une proposition est évaluée dès que ses quatre encoches sont remplies.

La palette visible et les règles de jeu restent définies par l’interface existante. Le contrat de l’API est décrit dans la spécification « API commune de test — Code secret en couleurs », contrat 0.2, règles `code-couleurs-1.0`.

## API de test

L’API est disponible uniquement avec le paramètre `test=1`, par exemple `index.html?test=1` ou l’URL publiée suivie de `?test=1`. Elle est alors exposée sous `window.codeSecretTest`. Sans ce paramètre, cette propriété n’est pas créée.

```js
const api = window.codeSecretTest;
console.log(api.getConfig());

await api.startGame({ secret: ['rouge', 'rouge', 'bleu', 'vert'] });
const state = await api.submitGuess(['rouge', 'bleu', 'rouge', 'rouge']);
console.log(state.lastResult); // { wellPlaced: 1, misplaced: 2 }
```

`getConfig()` et `getState()` sont synchrones. `startGame()` et `submitGuess()` renvoient des promesses. Attendre chaque appel de mutation avant le suivant.

### Configuration

`getConfig()` retourne les valeurs suivantes :

```js
{
  apiVersion: '0.2',
  rulesVersion: 'code-couleurs-1.0',
  secretLength: 4,
  maxAttempts: 10,
  allowDuplicates: true,
  colors: ['rouge', 'bleu', 'vert', 'jaune', 'orange', 'violet']
}
```

Les noms sont en minuscules et doivent correspondre exactement aux identifiants ci-dessus. Le code et les propositions acceptent les répétitions.

### Opérations

- `getConfig()` retourne la configuration fixe.
- `startGame()` démarre une partie avec un code aléatoire. `startGame({})` fait la même chose.
- `startGame({ secret: [...] })` démarre une partie avec le code fourni. Les options ne peuvent contenir que la clé `secret`.
- `submitGuess([...])` soumet quatre couleurs. Une proposition valide met à jour l’historique et les indices du plateau visible.
- `getState()` retourne une copie de l’état courant. L’objet n’expose jamais le code secret.

Les états possibles sont `not-started`, `playing`, `won` et `lost`. L’état contient `status`, `attempts`, `history`, `lastGuess`, `lastResult` et `lastError`. Une entrée d’historique a cette forme :

```js
{
  guess: ['rouge', 'bleu', 'rouge', 'vert'],
  result: { wellPlaced: 2, misplaced: 2 }
}
```

Les refus de `submitGuess()` ne consomment pas d’essai : l’appel résout vers l’état avec `lastError` renseigné. Les codes prévus sont `game-not-started`, `game-finished`, `invalid-type`, `invalid-length` et `invalid-color`. Les options invalides de `startGame()` rejettent la promesse avec une erreur portant un code, notamment `invalid-options`, `invalid-type`, `invalid-length` ou `invalid-color`. Un démarrage invalide laisse la partie et l’interface précédentes inchangées.

## Cas de contrôle du contrat

Ces cas donnent des résultats reproductibles avec `startGame({ secret: [...] })` :

| Code secret | Proposition | Bien placées | Mal placées |
| --- | --- | ---: | ---: |
| rouge, rouge, bleu, vert | rouge, bleu, rouge, rouge | 1 | 2 |
| rouge, rouge, bleu, vert | rouge, rouge, bleu, vert | 4 | 0 |
| rouge, rouge, rouge, rouge | bleu, bleu, bleu, bleu | 0 | 0 |

Le contrat prévoit aussi une victoire au dixième essai, une défaite après dix essais sans code exact, le refus d’une proposition invalide sans modifier le compteur et le redémarrage depuis un état final.

## Lancer le jeu

Ouvrir `index.html` dans un navigateur. Pour le publier sur GitHub Pages, publier le contenu du dépôt puis ouvrir l’URL fournie par GitHub. Ajouter `?test=1` à cette URL pour utiliser l’API de test.

## Vérification

Aucun résultat de test manuel ou automatisé n’est consigné ici pour la version actuelle. Après un essai, noter la version ou le commit, le comportement vérifié, le résultat attendu, le résultat observé et les limites rencontrées.

