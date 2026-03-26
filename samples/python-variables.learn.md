---
lang: fr
estimated_time: 15min
tags: [python, variables, basics, programming]
---

# Les variables en Python

Une variable est l'un des concepts fondamentaux de la programmation. Elle permet de **nommer** une valeur pour la réutiliser, la modifier et lui donner du sens.

## Qu'est-ce qu'une variable ?

En Python, une variable est une **référence** vers un objet en mémoire — pas une case mémoire comme en C ou Java.

Déclarer une variable est aussi simple qu'écrire :

```python
age = 25
prenom = "Alice"
actif = True
```

La variable `age` "pointe" vers l'objet entier `25`. Si on réassigne `age = 30`, on ne modifie pas l'objet `25` — on crée un nouvel objet `30` et on déplace la référence.

> [!tip]
> Choisissez des noms de variables **descriptifs** : `nombre_etudiants` est bien plus lisible que `n` ou `x`.

```quiz
? Comment déclare-t-on une variable en Python ?
[x] age = 25
[ ] int age = 25
[ ] var age = 25
[ ] let age = 25
```

## Règles de nommage

| Règle | Exemple valide | Exemple invalide |
|-------|----------------|------------------|
| Lettres, chiffres, `_` | `ma_variable` | `ma-variable` |
| Ne commence pas par un chiffre | `score1` | `1score` |
| Sensible à la casse | `Age` ≠ `age` | — |
| Pas de mots réservés | `total` | `for`, `if`, `class` |

> [!warning]
> `Age` et `age` sont **deux variables différentes** en Python. Cette sensibilité à la casse est source d'erreurs fréquentes chez les débutants.

```quiz
? Quel nom de variable est INVALIDE en Python ?
[ ] ma_variable
[ ] score1
[x] 1score
[ ] total
```

## Affecter et réaffecter

```example title="Affecter et modifier une variable"
# Première affectation
score = 0
print(score)   # → 0

# Réaffectation
score = 42
print(score)   # → 42

# On peut aussi s'appuyer sur la valeur précédente
score = score + 10
print(score)   # → 52
```

En Python, il n'y a pas de mot-clé `var`, `let` ou `const` — on affecte directement.

## Inférence de type

Python est un langage à **typage dynamique** : le type d'une variable est déterminé automatiquement à partir de la valeur affectée, sans déclaration explicite.

```python
x = 42          # int
x = "bonjour"   # maintenant str — Python accepte ce changement
x = [1, 2, 3]   # maintenant list
```

Pour connaître le type d'une variable, on utilise `type()` :

```python
age = 25
print(type(age))   # → <class 'int'>
```

> [!note]
> Python 3.5+ supporte les **annotations de type** (`age: int = 25`) qui documentent l'intention sans forcer un type à l'exécution. Elles sont utilisées par les outils d'analyse statique comme `mypy`.

```quiz scored:true
? Quel est le type de `3.14` en Python ?
[ ] int
[x] float
[ ] str
[ ] bool
```

## Résumé

```summary
- Une variable associe un **nom** à une **valeur** en mémoire
- En Python, pas de déclaration de type — le type est inféré dynamiquement
- Réaffectation libre : `x = 42` puis `x = "bonjour"` est valide
- Noms sensibles à la casse : `Age` ≠ `age`
- Préférez des noms descriptifs : `nombre_etudiants` plutôt que `n`
```

---

*Pour aller plus loin, testez vos connaissances avec le quiz complet :*

!quiz ./check-python-variables.quiz.md
