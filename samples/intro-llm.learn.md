---
title: "Introduction aux LLMs — Comment fonctionnent les grands modèles de langage ?"
type: lesson
lang: fr
domain: ia
level: beginner
estimated_time: 20min
tags: [llm, ia, transformer, nlp]
prereqs: []
math: false
---

# Introduction aux LLMs — Comment fonctionnent les grands modèles de langage ?

## Qu'est-ce qu'un LLM ?

Un **Large Language Model** (LLM), ou grand modèle de langage, est un système d'intelligence artificielle entraîné sur d'immenses quantités de texte pour comprendre et générer du langage naturel.

Des exemples connus : GPT-4, Claude, Gemini, Llama, Mistral.

```concept id:llm-definition
Un LLM est un réseau de neurones profond entraîné à **prédire le prochain token** dans une séquence de texte. C'est cette tâche apparemment simple qui, à très grande échelle, donne naissance à des capacités émergentes remarquables : raisonnement, traduction, code, résumé…
```

> [!note]
> Le terme "grand" fait référence au nombre de **paramètres** du modèle — des poids numériques ajustés pendant l'entraînement. Les LLMs modernes en comptent des milliards, voire des billions.

---

## Les tokens : l'unité de base

Les LLMs ne lisent pas les mots directement. Ils découpent le texte en **tokens** — des fragments qui peuvent être des mots entiers, des morceaux de mots, ou des caractères de ponctuation.

```example title:"Tokenisation d'une phrase"
Phrase : "Les LLMs sont fascinants !"
Tokens : ["Les", " LL", "Ms", " sont", " fascinants", " !"]

→ 6 tokens pour 5 mots.
Chaque token est converti en un vecteur numérique (embedding).
```

> [!tip]
> En pratique, l'anglais est tokenisé plus efficacement que le français. Le mot "fascinants" peut être découpé en plusieurs tokens alors que son équivalent anglais "fascinating" n'en formerait qu'un seul.

---

## L'architecture Transformer

Tous les LLMs modernes reposent sur l'architecture **Transformer**, introduite en 2017 dans l'article *"Attention is All You Need"*.

```concept id:transformer
Le Transformer traite les tokens en **parallèle** (contrairement aux RNNs séquentiels) grâce à un mécanisme central : **l'attention**.

L'attention permet au modèle de mesurer l'importance relative de chaque token par rapport aux autres dans la séquence — peu importe leur distance.
```

Les deux composants clés d'un Transformer :

| Composant | Rôle |
|-----------|------|
| **Self-Attention** | Chaque token "regarde" tous les autres pour construire sa représentation contextuelle |
| **Feed-Forward Network** | Transforme chaque représentation indépendamment via des couches denses |

Ces deux blocs sont empilés des dizaines (voire des centaines) de fois pour former un LLM.

---

## L'entraînement : apprendre à prédire

```concept id:pretraining
La phase de **pré-entraînement** consiste à faire prédire au modèle le token suivant dans des milliards de phrases issues d'internet, de livres, de code…

Objectif : `P(token_suivant | contexte_précédent)` — maximiser la probabilité du bon token.

Après des milliers de milliards d'exemples, le modèle développe une compréhension profonde de la langue et du monde.
```

```example title:"Prédiction de token"
Contexte : "La capitale de la France est"
Token probable : " Paris" (très haute probabilité)
Token moins probable : " Lyon" (faible probabilité)
Token improbable : " banane" (probabilité quasi nulle)
```

> [!warning]
> Un LLM ne "comprend" pas au sens humain — il apprend des **distributions statistiques** sur des textes. Ses erreurs ("hallucinations") surviennent quand il génère des tokens plausibles mais factuellement faux.

---

## Du pré-entraînement à l'assistant : le fine-tuning

Un LLM brut prédit des tokens mais ne sait pas "répondre" à des questions. Pour créer un assistant, on ajoute deux étapes :

```concept id:rlhf
**Instruction tuning** : on ré-entraîne le modèle sur des exemples de dialogues (question → réponse idéale).

**RLHF** (Reinforcement Learning from Human Feedback) : des humains notent les réponses, et le modèle apprend à maximiser ces scores. C'est ce qui rend le modèle utile, inoffensif et honnête.
```

---

## Le contexte : la "mémoire de travail"

Les LLMs traitent le texte dans une **fenêtre de contexte** — la quantité de tokens qu'ils peuvent "voir" simultanément.

```example title:"Fenêtre de contexte"
GPT-3    →  4 096 tokens  (~3 000 mots)
GPT-4    → 128 000 tokens (~96 000 mots)
Claude 3 → 200 000 tokens (~150 000 mots)

Au-delà de cette limite, le modèle "oublie" ce qui est en dehors.
```

> [!tip]
> Contrairement à un humain, un LLM n'a **pas de mémoire persistante** entre les conversations. Chaque session repart de zéro — à moins qu'un système externe (RAG, mémoire applicative) ne lui réinjecte du contexte.

---

## Exercice

```exercise hint:"Pensez à la définition du pré-entraînement"
Pourquoi un LLM peut-il parfois générer des informations fausses présentées avec confiance (hallucinations) ?

**Solution:**
Parce qu'un LLM est entraîné à **maximiser la probabilité du token suivant**, pas à vérifier la vérité factuelle. Il génère le token statistiquement le plus plausible dans le contexte donné — ce qui peut produire des affirmations convaincantes mais incorrectes, surtout sur des sujets rares ou mal représentés dans ses données d'entraînement.
```

---

## Résumé

```summary
- Un **LLM** est un réseau de neurones entraîné à prédire des tokens sur des milliards de textes
- Il repose sur l'architecture **Transformer** et son mécanisme d'**attention**
- Le **pré-entraînement** lui donne une compréhension générale du langage et du monde
- Le **fine-tuning + RLHF** le transforme en assistant capable de suivre des instructions
- La **fenêtre de contexte** est sa mémoire de travail — limitée et non persistante
- Les **hallucinations** découlent de la nature statistique de la génération de tokens
```

!quiz ./intro-llm-quiz.quiz.md
