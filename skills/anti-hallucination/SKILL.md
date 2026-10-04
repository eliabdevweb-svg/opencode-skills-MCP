---
name: anti-hallucination
description: "Réduction des hallucinations selon les guides officiels Anthropic : permission de ne pas savoir, citations verbatim, vérification par citations, restriction de connaissance externe, chain-of-thought, Best-of-N, raffinage itératif. Use when asserting facts, citing sources, statistics, dates or version numbers, answering questions on recent or niche topics, reviewing an AI-generated claim, or when the user says hallucination, invente, cite une source, certitude, verifier une affirmation, je ne sais pas ou c'est faux."
version: 1.0.0
tags: [reliability, hallucination, verification, citations, grounding, rag]
---

# Anti-hallucination

Protocole de fiabilité appliqué avant toute affirmation factuelle.
Source : [Reduce hallucinations](https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations) — documentation officielle Claude.

> « Les hallucinations sont difficiles à anticiper, difficiles à détecter, et la mauvaise
> réponse ressemble souvent exactement à une bonne. »

## Procédure avant d'affirmer

Avant chaque affirmation factuelle, s'arrêter et vérifier :

1. **Est-ce que je le sais vraiment, ou est-ce que je fais du pattern-matching ?**
   Si pattern-matching → nuancer ou décliner.
2. **Suis-je dans une catégorie à risque ?** Noms propres, chiffres exacts, dates,
   numéros de version, sujets de niche, événements postérieurs à la date d'entraînement.
   Si oui → lever le seuil avant d'affirmer.
3. **Puis-je citer une source vérifiable, ou suis-je sur le point d'en inventer une ?**
   Si c'est la seconde → **ne pas citer**.

Si une affirmation antérieure s'avère faise, **la corriger spontanément** au lieu de
s'obstiner.

## Les 7 techniques Anthropic

### 1. Permission de ne pas savoir

Donner explicitement le droit d'admettre l'incertitude réduit fortement les faits inventés.

> « Si tu n'as pas assez d'information pour évaluer un point avec confiance, écris :
> *je n'ai pas assez d'information pour répondre avec certitude*. »

### 2. Citations verbatim pour l'ancrage (docs > 20 000 tokens)

Demander d'abord d'extraire les citations **mot pour mot**, puis de raisonner dessus.
L'extraction verbatim ancre la réponse dans le texte réel.

```text
1. Extrais d'abord, mot pour mot, les passages pertinents du document.
2. Raisonne uniquement à partir de ces extraits.
3. Toute affirmation non couverte par un extrait est à retirer.
```

### 3. Vérification par citations (rétractation obligatoire)

Rendre chaque affirmation auditable, puis la rejeter faute de preuve.

```text
Rédige [X] en n'utilisant que les documents fournis.
Ensuite, pour CHAQUE affirmation, trouve une citation directe qui la soutient.
Si tu ne trouves pas de citation, retire l'affirmation et marque l'emplacement
par des crochets vides [].
```

### 4. Restriction de connaissance externe

```text
N'utilise QUE les informations des documents fournis.
N'utilise AUCUNE connaissance générale, même si tu sembles connaître le sujet.
```

### 5. Vérification par chaîne de pensée

Demander le raisonnement étape par étape **avant** la réponse finale révèle les
logiques fausses et les hypothèses implicites.

### 6. Best-of-N

Exécuter le même prompt plusieurs fois et comparer. Les incohérences entre sorties
signalent une hallucination.

### 7. Raffinage itératif

Reprendre la sortie comme entrée d'un prompt de suivi et lui demander de vérifier ou
d'étayer chaque point — attrape les contradictions.

## Patterns de sortie

**Préférer** — formulation calibrée qui laisse vérifier :

- « Vérifiez contre la documentation actuelle / les release notes / la source. »
- « Deux hypothèses me viennent : A ou B. Sans plus de contexte, je ne peux trancher. »
- « Je ne suis pas certain de ce point. »

**Éviter** — fausse précision, autorité non sourcée, ou flou qui laisse entendre
la certitude :

- « Je suis assez sûr que c'est la version 3.8. » — un flou sur une affirmation
  précise laisse quand même entendre un savoir qu'on n'a pas.
- « Selon une étude de 2024… » — n'invoquer aucune étude qu'on ne peut nommer.
- « Oui, je suis certain. » — quand on ne peut pas vérifier.
- Chiffres précis (populations, marchés, revenus, statistiques de niche) sans source.

## Table de correspondance → outils

| Technique | Outil à mobiliser |
|---|---|
| Ancrage dans une doc réelle | MCP **Context7** (docs versionnées) |
| Restriction de connaissance externe | MCP **Fetch** / recherche web |
| Vérification empirique | MCP **Playwright** — constater au lieu de supposer |
| Best-of-N / raisonnement structuré | MCP **sequential-thinking** |
| Standard vérifiable | Skills `testing`, `security`, `coding-standards`, `documentation` |

## Limites

Ces techniques **réduisent** fortement les hallucinations mais ne les **éliminent pas**.
Toujours valider l'information critique, surtout pour les décisions à fort enjeu.
