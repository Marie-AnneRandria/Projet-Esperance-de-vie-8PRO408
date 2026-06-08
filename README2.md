# Projet de session — 8PRO408
## Impact des guerres, des catastrophes naturelles et des épidémies sur l'espérance de vie (2000–2021)

**Cours :** Outils de programmation pour la science des données (8PRO408) — UQAC, Été 2026
**Auteurs :** Abir & [camarade]
**Date de remise :** 14 juin 2026

---

## 1. Résumé du projet

Ce projet croise trois sources de données indépendantes — espérance de vie (Banque mondiale), catastrophes naturelles (EM-DAT) et conflits armés (UCDP) — afin de quantifier **quel type de choc (guerre, catastrophe naturelle, épidémie) pèse le plus sur l'espérance de vie d'un pays**, et de vérifier si le cumul de plusieurs chocs aggrave la situation.

L'approche combine une **analyse exploratoire et statistique** (statistiques descriptives, tests inférentiels) et une **phase de modélisation par apprentissage machine** (régression et classification), pour passer de la simple corrélation à une mesure d'importance des facteurs.

> **Choix structurant :** on travaille sur la fenêtre **2000–2021**, période commune aux trois sources (l'espérance de vie de la Banque mondiale va jusqu'en 2024, mais les catastrophes s'arrêtent en 2021). La granularité d'analyse est le couple **(pays, année)**, identifié par un code **ISO3** unique.
>
> **Atout de la période récente :** en allant jusqu'en 2020–2021, l'analyse capture la **chute d'espérance de vie due au COVID-19** — un choc épidémique mondial directement pertinent pour notre problématique.

---

## 2. Problématique et hypothèses

**Question centrale :** parmi les guerres, les catastrophes naturelles et les épidémies, quel facteur est le plus fortement associé à une baisse de l'espérance de vie, une fois pris en compte le niveau de développement ?

| # | Question | Hypothèse (H1) | Méthode prévue |
|---|----------|----------------|----------------|
| Q1 | Quel facteur impacte le plus l'espérance de vie ? | Les conflits armés ont l'effet le plus marqué, devant les épidémies puis les catastrophes naturelles. | Régression multiple + importance des variables (forêt aléatoire) |
| Q2 | Les pays subissant plusieurs chocs à la fois ont-ils une espérance de vie encore plus basse ? | Le cumul (guerre + catastrophe) abaisse significativement l'espérance de vie vs un seul choc. | Groupes (aucun / un seul / cumul) + ANOVA |
| Q3 | Quels sont les 10 pays avec la plus faible espérance de vie sur la période ? | — (descriptif) | Agrégation + classement + visualisation |
| Q4 | Comment évolue l'espérance de vie mondiale entre 2000 et 2021 ? | Tendance globale à la hausse, interrompue par le choc COVID en 2020. | Série temporelle, moyenne **pondérée par la population** |

> On formule des hypothèses **a priori** pour montrer une démarche scientifique (hypothèse → test → conclusion). Le professeur a précisé que la conclusion n'a pas besoin de les confirmer : c'est la rigueur du raisonnement qui compte.

---

## 3. Jeux de données (vérifiés)

| Source | Fichier | Dimensions | Période | Granularité | Identifiant pays |
|--------|---------|-----------|---------|-------------|------------------|
| Banque mondiale — Espérance de vie (`SP.DYN.LE00.IN`) | `API_SP_DYN_LE00_IN_...csv` | 266 × 71 | 1960–2024 | pays-année | **ISO3** (`Country Code`) |
| EM-DAT — Catastrophes naturelles | `disasters_1900_2021.csv` | 16126 × 45 | 1900–2021 | événement | **ISO3** (`ISO`) |
| UCDP — Conflits armés | `conflicts.csv` | 2752 × 28 | 1946–2024 | conflit-année | nom (`location`) → à convertir |
| OMS (Kaggle) — Variables de contrôle | `life_expectancy.csv` | 2938 × 22 | 2000–2015 | pays-année | nom (`Country`) → à convertir |

**Détails confirmés et points d'attention**

- **Banque mondiale :** CSV avec **4 lignes d'en-tête à ignorer** (`skiprows=4`), format **large** (une colonne par année) → à dépivoter avec `pd.melt`. Le fichier contient des **agrégats** (« World », « Low income », « Arab World »…) qu'il faut **retirer** en gardant uniquement les pays présents dans le fichier `Metadata_Country` avec une `Region` non vide.
- **Catastrophes :** tous les événements sont de groupe `Natural`. Sous-catégories : `Hydrological`, `Meteorological`, `Geophysical`, `Climatological` (= **catastrophes physiques**) et **`Biological` = épidémies** (898 événements sur 2000–2021, COVID compris). `Total Deaths` est renseigné pour ~71 % des lignes.
- **Conflits :** `intensity_level` ∈ {1 = conflit mineur (25–999 morts), 2 = guerre (≥1000 morts)} ; `type_of_conflict` (intra-étatique majoritaire). La colonne `location` contient **151 lignes multi-pays** (ex. « Egypt, Israel ») → à **éclater** (`str.split(',').explode()`) avant conversion en ISO3.
- **OMS (covariables) :** noms de colonnes avec **espaces parasites** (`df.columns.str.strip()`). Valeurs manquantes notables : `Population` 22 %, `Hepatitis B` 19 %, `GDP` 15 %.

---

## 4. Méthodologie — pipeline détaillé

### Étape 0 — Mise en place
- Imports (`pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`, `sklearn`, `country_converter`).
- Graine aléatoire (`np.random.seed(42)`) pour la reproductibilité.

### Étape 1 — Chargement et nettoyage
- **Banque mondiale :** `read_csv(..., skiprows=4)` → `melt` → colonnes `iso3 / year / life_expectancy` → filtre `year ∈ [2000, 2021]` → suppression des agrégats via `Metadata_Country`.
- **Catastrophes :** filtre `Year ∈ [2000, 2021]` → séparation **physiques** (Hydro/Meteo/Geo/Climato) vs **épidémies** (`Biological`).
- **Conflits :** filtre `year ∈ [2000, 2021]` → `explode` des `location` multi-pays.
- **OMS :** `str.strip()` sur les colonnes → quantification des manquants (`isna().mean()`) → imputation justifiée (médiane par pays) ou suppression motivée. Documenter chaque choix.

### Étape 2 — Harmonisation des pays (ISO3)
- Banque mondiale et EM-DAT : **ISO3 déjà disponible** (jointure quasi directe — gros gain).
- Conflits et OMS : conversion des noms en ISO3 avec `country_converter` (`coco.convert(names=..., to='ISO3')`).
- Journaliser les pays non reconnus et quantifier la perte.

### Étape 3 — Agrégation et fusion
- **Catastrophes physiques** → par (iso3, année) : `nb_catastrophes`, `morts_catastrophes`.
- **Épidémies (Biological)** → par (iso3, année) : `nb_epidemies`, `morts_epidemies`.
- **Conflits** → par (iso3, année) : `nb_conflits`, `intensite_max`, indicateur binaire `guerre` (`intensity_level == 2`).
- **Fusion à gauche** sur la base Banque mondiale (clé iso3 + année), absences remplies par **0** (pas d'événement ≠ NaN).
- **Niveau 2 (ML enrichi) :** jointure des covariables OMS (`GDP`, `Schooling`, `Status`, `HIV/AIDS`, `Population`…) sur 2000–2015.

### Étape 4 — Analyse exploratoire (EDA)
- Statistiques descriptives (`describe`), par statut de développement et par région.
- **Visualisations :** distributions, **heatmap de corrélation**, nuages de points (espérance de vie vs morts par choc), boxplots par groupe.
- **Q3 :** top 10 des pays par espérance de vie moyenne (bar chart horizontal).
- **Q4 :** courbe d'évolution mondiale 2000–2021, en **moyenne pondérée par la population**, avec mise en évidence du **creux 2020 (COVID)**.

### Étape 5 — Statistiques inférentielles (SciPy)
- **Q2 :** 4 groupes (aucun choc / catastrophe seule / conflit seul / cumul). Comparaison des moyennes par **ANOVA** (`scipy.stats.f_oneway`) + tests post-hoc ; rapporter p-value et taille d'effet.
- Corrélations de Pearson/Spearman entre chaque facteur et l'espérance de vie, avec p-values.

### Étape 6 — Modélisation par apprentissage machine
Objectif : dépasser la corrélation et **mesurer l'importance relative** des facteurs (réponse rigoureuse à Q1).

**6a. Régression — prédire l'espérance de vie**
- Variables : facteurs de chocs + variables de contrôle (PIB, scolarité, statut).
- Modèles comparés : **régression linéaire** (interprétable) vs **forêt aléatoire** (non linéaire).
- Découpage **train/test** + **validation croisée k-fold** ; métriques **R², RMSE, MAE**.
- **Importance des variables** (coefficients standardisés + `feature_importances_`) → classement des facteurs = réponse à Q1.

**6b. Classification — détecter les pays à faible espérance de vie**
- Cible binaire : espérance de vie < seuil (ex. 1er quartile).
- Modèles : **régression logistique**, **arbre de décision**, **forêt aléatoire**, **k-NN**.
- Gestion du **déséquilibre des classes** (poids de classe — vu en cours #9).
- Évaluation : **matrice de confusion**, **précision / rappel / F1**, **courbe ROC + AUC** ; discussion du **sur-apprentissage** (train vs test).

### Étape 7 — Synthèse et conclusion
- Réponse argumentée aux 4 questions, en confrontant résultats statistiques et ML.
- Limites (§7) et pistes d'amélioration.

---

## 5. Structure cible du notebook

```
0. Contexte, problématique et hypothèses        (markdown)
1. Imports et configuration
2. Chargement des 4 sources
3. Nettoyage
   3.1 Banque mondiale  3.2 Catastrophes  3.3 Conflits  3.4 Covariables OMS
4. Harmonisation des pays (ISO3)
5. Agrégation et fusion -> dataset final
6. Analyse exploratoire (descriptif + visualisations + Q3 + Q4 + COVID)
7. Statistiques inférentielles (Q2 : ANOVA)
8. Modélisation ML (Q1)
   8.1 Régression  8.2 Classification  8.3 Importance des facteurs
9. Conclusion, limites et perspectives
```
Chaque section commence par une **cellule markdown** qui explique l'intention et interprète les résultats. C'est ce qui transforme un script en rapport scientifique.

---

## 6. Couverture des notions du cours

| Notion (cours) | Où elle est utilisée |
|----------------|----------------------|
| NumPy / Pandas — nettoyage, pivot, agrégation, fusion | Étapes 1, 3, 5 |
| Valeurs manquantes / aberrantes | Étape 3 |
| Matplotlib / Seaborn — visualisation | Étape 6 |
| Données temporelles / agrégation | Étapes 5, 6 (Q4) |
| Statistiques inférentielles (SciPy) | Étape 7 (ANOVA, corrélations) |
| Arbre de décision + élagage | Étape 8 |
| Forêt aléatoire (bootstrap, OOB) | Étape 8 |
| Régression linéaire / logistique | Étape 8 |
| k-NN | Étape 8 |
| Train/test, validation croisée | Étape 8 |
| Matrice de confusion, précision/rappel/F1, ROC/AUC | Étape 8b |
| Déséquilibre des classes | Étape 8b |
| Sur-apprentissage | Étape 8b |

> Pas besoin de tout caser de force : une régression **et** une classification bien évaluées suffisent à démontrer la maîtrise. Le tableau sert à choisir consciemment ce qu'on inclut.

---

## 7. Limites et précautions méthodologiques

- **Corrélation ≠ causalité :** on mesure des associations, pas des causes. À expliciter en conclusion.
- **Biais écologique :** l'échelle pays-année masque les disparités internes.
- **Couverture des décès :** `Total Deaths` n'est renseigné que pour ~71 % des catastrophes ; le nombre d'événements est un indicateur plus complet que le nombre de morts.
- **Valeurs manquantes des covariables OMS :** justifier que l'imputation n'introduit pas de biais majeur.
- **Conversion ISO3 :** quelques entités peuvent rester non appariées (conflits/OMS) ; quantifier la perte.
- **Fenêtre temporelle :** conclusions limitées à 2000–2021.

---

## 8. Livrables (à confirmer auprès du professeur sur Discord)

- [ ] Notebook Jupyter exécuté de bout en bout (code + cellules markdown explicatives)
- [ ] Rapport scientifique — **français soigné** (pénalité possible de 20 % sur les fautes)
- [ ] Support de présentation (séances des 10 / 15 / 17 juin)
- [ ] Jeu de données fusionné + scripts reproductibles

---

## 9. Organisation sur la semaine

| Jour | Tâche |
|------|-------|
| J1 | Confirmation des livrables sur Discord + nettoyage des 4 sources |
| J2 | Harmonisation ISO3 + agrégation + fusion (cœur technique) |
| J3 | EDA complète + Q3 + Q4 + COVID + visualisations |
| J4 | Stats inférentielles (Q2) + début modélisation |
| J5 | Modélisation ML (régression + classification) + importance des facteurs |
| J6 | Rédaction du rapport + relecture français |
| J7 | Support de présentation + répétition + marge de sécurité |

---

## 10. Reproductibilité

```
Projet-Esperance-de-vie-8PRO408/
├── data/
│   ├── API_SP_DYN_LE00_IN_DS2_en_csv_v2_265357.csv
│   ├── Metadata_Country_...csv
│   ├── disasters_1900_2021.csv
│   ├── conflicts.csv
│   └── life_expectancy.csv          # OMS, covariables 2000-2015
├── notebook.ipynb
├── rapport.pdf
├── requirements.txt   # pandas, numpy, matplotlib, seaborn, scipy, scikit-learn, country_converter
└── README.md
```

### Option (recommandée, facile) pour aller plus loin
Pour un contrôle homogène sur **toute** la fenêtre 2000–2021 (au lieu de 2000–2015 via l'OMS), télécharger 2 indicateurs Banque mondiale supplémentaires (même format, jointure ISO3) :
- Population totale → `SP.POP.TOTL` (utile pour la moyenne mondiale pondérée)
- PIB par habitant → `NY.GDP.PCAP.CD` (variable de contrôle ML)
