# TDP — Training Description Protocol

**Version** : 0.1-draft  
**Licence spec** : [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/)  
**Licence outils** : Apache 2.0  
**Statut** : Draft — contributions bienvenues

---

## Qu'est-ce que TDP ?

TDP est un format ouvert, lisible par les humains et parsable par les machines, pour décrire des plans d'entraînement structurés. Il couvre la musculation en priorité et est conçu pour s'étendre à d'autres sports.

TDP résout un problème concret : chaque application de suivi d'entraînement (Strong, Hevy, Whoop, Garmin…) a son propre format propriétaire. Les athlètes et les coachs ne peuvent pas échanger leurs programmes. TDP est le format intermédiaire universel.

## Principes

- **Fragmentable** — un fichier TDP peut être une séance unique, un bloc de 4 semaines, ou un plan complet de 12 semaines. Chaque fragment est autonome et importable seul.
- **Niveaux de conformité** — les apps déclarent ce qu'elles supportent (`minimal` / `standard` / `full`) et ignorent le reste sans erreur.
- **Référentiel d'exercices ouvert** — basé sur [free-exercise-db](https://github.com/yuhonas/free-exercise-db) (Unlicense), étendu par la couche `tdp_ext` pour les équivalences et contraintes matériel.
- **Substituabilité** — un moteur de substitution résout automatiquement les exercices selon l'inventaire matériel déclaré et les contraintes physiques (blessures).
- **Compatible SWF** — les blocs cardio peuvent pointer vers des fichiers [Structured Workout Format](https://structuredworkoutformat.dev) via `load_type: swf_ref`.

## Structure du dépôt

```
tdp-spec/
├── schemas/
│   ├── exercise-library.schema.yaml   # Couche 1 : référentiel exercices
│   ├── fragment.schema.yaml           # Couche 2 : tous les types de fragments
│   └── conformance.yaml              # Règles d'import/export et niveaux
├── examples/
│   ├── exercise-library.example.yaml
│   ├── session.example.yaml
│   └── plan-ppl-12w.example.yaml
└── README.md
```

## Types de fragments

| `fragment_type` | Contenu | Conformance minimale requise |
|---|---|---|
| `set` | Une série (reps, charge, tempo…) | minimal |
| `exercise_block` | Un exercice + ses séries | minimal |
| `session` | Une séance complète | minimal |
| `mesocycle` | Un bloc de semaines | standard |
| `plan` | Plan complet avec macrocycle | full |

## Champs clés

### `load_type`
`kg_absolute` · `lbs_absolute` · `percentage_1rm` · `rpe` · `bodyweight` · `band_light/medium/heavy` · `swf_ref`

### `tempo`
Format ECCO 4 chiffres : **E**xcentrique / pause-**C**as / **C**oncentrique / pause-haut.  
`X` = explosif. Exemples : `"3010"`, `"20X0"`, `"3111"`.

### `progression_model`
`linear_volume` · `double_progression` · `rpe_autoregulation` · `wave_loading` · `percentage_based` · `custom`

### `type` de set
`warmup` · `working` · `drop_set` · `rest_pause` · `myo_rep` · `amrap` · `failure`

## Relation avec free-exercise-db

TDP étend free-exercise-db (Unlicense / domaine public) via le champ `tdp_ext` sur chaque entrée. Les champs natifs de free-exercise-db sont préservés sans modification. La couche `tdp_ext` ajoute : `equipment_required` (normalisé), `laterality`, `movement_pattern`, `joint_stress`, et les `equivalence_groups`.

## Contribuer

TDP est un effort communautaire. Les contributions sont les bienvenues via pull request :

- Nouveaux exercices dans le référentiel
- Nouveaux `equivalence_groups`
- Nouveaux `sport_tags` et `movement_pattern`
- Implémentations de référence (validateurs, convertisseurs)
- Retours d'expérience d'intégration

Toute contribution au dépôt spec est soumise à CC0 (domaine public).  
Les outils et implémentations de référence sont sous Apache 2.0.

## Contributeurs

| Contributeur | Rôle | Notes |
|---|---|---|
| [@vous](https://github.com/) | Initiateur, product owner | Conception du besoin, orientations architecturales, décisions de design |
| [Claude Sonnet 4.6](https://www.anthropic.com/claude) (Anthropic) | Co-auteur de la spec | Schémas YAML, architecture des fragments, référentiel d'équivalences, conformance spec |

TDP est né d'une collaboration humain–IA. Les décisions structurantes (choix du format, modèle de fragmentabilité, stratégie de licence, alignement avec free-exercise-db et SWF) ont été prises conjointement. Les schémas formels et la documentation ont été rédigés par Claude Sonnet 4.6.

Nous pensons que citer les contributeurs IA au même titre que les contributeurs humains est une bonne pratique, cohérente avec les valeurs d'un projet ouvert.

---

## Statut et roadmap

- [x] v0.1 : Musculation — schémas, conformance, substituabilité
- [ ] v0.2 : Outils CLI (validateur, convertisseur SWF↔TDP)
- [ ] v0.3 : Extension endurance / crossfit
- [ ] v1.0 : Stabilisation de l'API — plus de breaking changes sur les champs marqués `stable`