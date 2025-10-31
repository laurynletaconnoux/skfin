# Project Notes from course

## Chapter 18 : Trading Around Earnings Announcements: Volatility, Risk, and Regulatory Filings

### Earnings announcement premium

Savor and Wilson (2016) highlight several key points: the earnings announcement premium is 9.9% annually. 
They also suggest that firms making these announcements are considered “risky,” which necessitates compensation for the associated risk. 

Furthermore, the study indicates that firm earnings contain information about market cash-flow risk, which has implications for aggregate risk. Our emphasis will be on the first point.

### Regulatory Filings

The regulatory filings and the earning conference calls take place typically on the same day, so that all the market-moving information is disclosed to the market at the same time.

---
## Chapter 19 : Measuring financial sentiment in 10-K filings : dictionary and model-based approaches

---

### 🎯 Objectif
Étudier comment mesurer le **sentiment financier** des rapports 10-K et 10-Q des entreprises,  
et utiliser ce sentiment comme **facteur prédictif** des rendements boursiers.

Deux approches :
1. **Rule-based sentiment (basée sur dictionnaire)**
2. **Learning-based sentiment (modèle linéaire)**

---

### 🧾 19.1 — Rule-based sentiment (Loughran & McDonald, 2011)

#### Principe
- Comptage de mots positifs et négatifs dans les rapports 10-K.  
- Utilisation de dictionnaires financiers spécifiques :  
  - *Positive*, *Negative*, *Uncertainty*, *Litigious*, *Strong/Weak modal* words.  
- Exemple de mots :
  - Négatifs : *loss, impairment, volatility, uncertainty*
  - Positifs : *growth, opportunity, improvement*

#### Méthode
- Transformation en **bag-of-words**.  
- Calcul de la proportion de chaque catégorie :  
  $$\text{Fin-Neg} = \frac{N_\text{Negative}}{N_\text{Words}}, \quad
  \text{Fin-Pos} = \frac{N_\text{Positive}}{N_\text{Words}}
  $$
- Pondération possible :
  - **Proportionnelle**
  - **tf-idf** (term frequency – inverse document frequency)

#### Résultats
| Variable | Moyenne | Interprétation |
|-----------|----------|----------------|
| Fin-Neg  | 1.39 % | Les rapports contiennent en moyenne 1.4 % de mots négatifs |
| Fin-Pos  | 0.75 % | Environ la moitié moins de mots positifs |
| Fin-Unc  | 1.20 % | Mesure l’incertitude |
| Fin-Lit  | 1.10 % | Mots liés à des litiges |

- Les **mots négatifs** sont les plus prédictifs :  
  ils annoncent des **rendements futurs plus faibles**.  
- Le t-stat du coefficient associé à `Fin-Neg` ≈ **−3**, significatif même après contrôle par :
  - taille,
  - ratio book-to-market,
  - volatilité,
  - rotation.

---

### 🧠 19.2 — Learning-based sentiment (Jegadeesh & Wu, 2013)

#### Principe
Remplacer les dictionnaires fixes par un apprentissage statistique des **poids des mots**.

$$
r_{d,t+4} = a + \sum_{v \in LM} b_v \frac{\text{count}_{d,v}}{\text{length}_d} + e
$$

- \( $b_v$ > 0 \) → mot associé à un rendement positif.
- \( $b_v$ < 0 \) → mot associé à un rendement négatif.

#### Out-of-sample scoring
$$
\text{Score}_d = \sum_v \frac{(b_v - \bar{b})}{\sqrt{\text{Var}(b_v)}} \frac{\text{count}_{d,v}}{\text{length}_d}
$$
Puis on teste la significativité de \( \beta \) dans :
$$
r_{d,t+5 \to t+w} = \alpha + \beta \cdot \text{Score}_d + \varepsilon
$$

#### Résultats
- Les mots les plus positifs :  
  *ingenuity, optimistic, progress, opportunity, transparency*  
- Les mots les plus négatifs :  
  *imperil, turbulent, setback, dismal, unfortunate*  

💡 Le **Word Power (WP)** score prédit les rendements sur 4 jours avec puissance,  
même après contrôle des facteurs de risque classiques.

---

### 📊 19.3 — Application empirique sur les 10-K / 10-Q

#### Données
Utilisation du **Loughran-McDonald dataset** via `load_10X_summaries()`.

#### Mesure du sentiment
$$
\text{sentiment} = \frac{N_\text{Positive} - N_\text{Negative}}{N_\text{Words}}
$$

#### Construction du prédicteur
1. Calcul de la **variation de sentiment** entre deux filings consécutifs.  
2. Conservation de la valeur pendant un mois (21 jours ouvrés).  
3. **Centrage et normalisation** pour obtenir un signal long/short neutre en risque.

#### Résultats de la stratégie
- **Sharpe ratio ≈ 0.96**
- Forte performance entre 2002 et 2008.
- Principaux contributeurs au PnL :  
  - **Apple (AAPL)**  
  - **Goldman Sachs (GS)**  

#### Analyse temporelle
- Le pic de prédictivité se situe à **J+1 / J+2** après le filing.  
- Effet éphémère (retour à la normale ensuite).

---
### ⚙️ Variantes testées
| Variable | Description | Sharpe Ratio |
|-----------|--------------|---------------|
| `-N_Negative / N_Words` | Ton pessimiste | **1.05** |
| `(N_Positive - N_Negative)/N_Words` | Sentiment net | 0.96 |
| `-N_WeakModal / N_Words` | Mots d’incertitude | 0.89 |
| `N_Constraining / N_Words` | Mots restrictifs | 0.90 |
| `N_Uncertainty / N_Words` | Mots d’ambiguïté | 0.33 |

💬 Les signaux basés sur les **mots négatifs ou restrictifs** sont les plus robustes.

---

### 🧾 Synthèse

| Approche | Principe | Résultat clé |
|-----------|-----------|---------------|
| **Rule-based** | Comptage de mots à partir du dictionnaire LM | Sentiment négatif → rendements futurs plus faibles |
| **Learning-based** | Apprentissage de poids optimaux des mots | Meilleure performance out-of-sample |
| **Application 10-K/10-Q** | Variation du ton du discours | Stratégie long/short, Sharpe ≈ 1 |

---

###  Conclusion
Les mots ont un **pouvoir prédictif** sur les marchés.  
Les filings 10-K/10-Q révèlent bien plus que des chiffres :  
le **ton**, la **prudence** et la **confiance** des dirigeants  
peuvent être traduits en signaux quantitatifs exploitables.

---
## Chapitre 20 — Out-of-Sample Predictability of the S&P 500: A Critical Assessment

---
### 🎯 Objectif
Évaluer si les variables macro-financières peuvent **prédire les rendements futurs** du S&P 500,  
en particulier hors échantillon (out-of-sample).

---

### 20.1 Timing the Market

#### Méthode
Welch & Goyal (2008) comparent :
- **Régression conditionnelle** : \( R_{t+1} = a + bX_t + \varepsilon_t \)
- **Régression inconditionnelle** : rendement moyen historique

Une variable est prédictive si la première fait mieux que la seconde hors échantillon.

#### Variables testées
- Dividend-price ratio (d/p)
- Dividend yield (d/y)
- Percent equity issuing (equis)

#### Résultats
- Très peu de pouvoir prédictif réel.
- Les performances dépendent des périodes (ex. choc pétrolier 1974).
- Les résultats in-sample ne se maintiennent pas out-of-sample.

---

### 20.2 Réponse de Campbell & Thompson (2008)

#### “Sign restrictions”
- Fixer le coefficient à 0 si le signe estimé est contraire à la théorie.
- Fixer la prévision à 0 si le rendement prévu est négatif.

#### Résumé
| Période | Résultat |
|----------|-----------|
| 1970s–1980s | Dividend yield significatif |
| 1990s | Relation affaiblie |
| Out-of-sample | Faible et instable |

---

### 20.3 Data & Implementation

- Données **Amit Goyal** ≈ **Ken French** (corrélation 0.99)
- Variables : `d12, e12, b/m, tbl, AAA, lty, ntis, Rfree, infl, ltr, corpr, svar, csp`
- Target : `Mkt-RF`
- Modèles : Ridge / RidgeCV + TimeSeriesSplit + StandardScaler
- Fonction objectif : maximisation du **Sharpe ratio**

---

### 20.4 Résultats du Backtest

| Composante | Sharpe Ratio | Interprétation |
|-------------|---------------|----------------|
| Total | 0.53 | Signal faible mais exploitable |
| Tilt | 0.53 | Contribution structurelle |
| Timing | 0.31 | Faible valeur ajoutée |

- Résultats robustes au choix de α (Ridge)  
- RidgeCV stabilise davantage le modèle (Sharpe ≈ 0.68)

---

### 📊 Conclusion

- Pouvoir prédictif **très limité hors échantillon**
- Les modèles linéaires de type Ridge captent un **signal faible mais stable**
- Le PnL vient surtout du **tilt structurel**, pas du timing actif

---
## Chapitre 21 — Politics and the Stock Market

---
### 🎯 Objectif
Étudier la relation entre la **politique américaine** (présidence et congrès) et la **performance du marché boursier**, à travers deux puzzles :
- **Presidential Puzzle** (Santa-Clara & Valkanov, 2003)
- **Government Puzzle** (Papamichalis, Ryu & Wilson, 2024)

---

### 21.1 The Presidential Puzzle

#### Résultats clés
- Rendements excédentaires **plus élevés sous présidents démocrates** :
  - +9 % (value-weighted), +16 % (equal-weighted)
- Différence robuste, non expliquée par :
  - le cycle économique,
  - les taux d’intérêt,
  - la volatilité.
- Le phénomène reste un **puzzle** économique inexpliqué.

---

### 21.2 The Government Puzzle

#### Hypothèse
Les performances de marché dépendent aussi de la **cohérence du gouvernement** :
- **United Government** = même parti contrôle Présidence, Sénat et Chambre.
- **Divided Government** = partis différents au pouvoir.

#### Résultats
| Configuration | Performance moyenne |
|----------------|--------------------|
| United + Démocrate | 📈 Forte performance (haut Sharpe, hauts rendements) |
| Divided + Républicain | 📉 Faible performance |
| United + Républicain | Moyenne |
| Divided + Démocrate | Modérée |

Effet plus marqué pour les **small caps (SMB)**.

---

### 21.3 Empirical analysis (1927–2022)

#### Données
- Source : `load_us_politics_dates()` et `Mkt-RF`
- Variables : `democratic_president`, `united_government`

#### Résultats principaux
| Indicateur | Démocrate | Républicain | United Gov | Divided Gov |
|-------------|------------|--------------|--------------|---------------|
| Sharpe ratio | 0.72 | 0.20 | 0.51 | 0.35 |
| Rendement annualisé | 12 % | 6 % | 10 % | 6 % |
| Volatilité annualisée | 17 % | 18 % | 20 % | 13 % |

---

### 21.4 Interprétation
- Les marchés réagissent positivement à la **stabilité politique**.
- Les périodes de gouvernement divisé sont liées à :
  - une incertitude accrue,
  - des blocages politiques,
  - et une performance plus faible.
- Le “Presidential Puzzle” reste inexpliqué économiquement, mais empiriquement robuste.

---

### 📊 Conclusion

> Les marchés américains ont historiquement mieux performé sous présidences démocrates et gouvernements unifiés.  
> Ce résultat, robuste empiriquement mais non théoriquement justifié, demeure un **puzzle politique et financier.** 

---
## Chapitre 22 — Market Reactions to Scheduled Macroeconomic Announcements

---
### 🎯 Objectif
Étudier comment les marchés réagissent aux **annonces macroéconomiques planifiées**
(CPI, PPI, emploi, FOMC), et si ces journées concentrent une part disproportionnée
des rendements boursiers.

---

### 22.1 Main Results (Savor & Wilson, 2013)

#### Trois faits empiriques :
1. Rendements des actions US ↑ les jours d’annonce macroéconomique.
2. Rendements des T-Bills ↓ ces jours-là.
3. Les "surprises" d’annonce ne prédisent pas les rendements.

→ Les investisseurs exigent une **prime de risque anticipée** pour détenir des actifs risqués
durant les jours d’annonce.

#### Données
- 157 CPI, 467 PPI, 621 emploi, 279 FOMC (1958–2009)

#### Résultats
| Variable | Announcement day | Non-announcement day | Différence |
|-----------|------------------|-----------------------|-------------|
| Stock excess return | 11.4 bps | 1.1 bps | **+10.3 bps** |
| T-Bill return | 1.5 bps | 2.9 bps | **–1.4 bps** |

→ Sharpe annualisé : **1.8 vs 0.18**

---

### 22.2 The FOMC Effect

#### Rôle
Le **FOMC** décide de la politique monétaire US et influence :
taux d’intérêt, change, matières premières, et prix d’actifs.

#### Données
- Environ 8 à 12 réunions FOMC par an (1999–2025)
- Analyse des rendements journaliers autour de ces dates

---

### 22.3 Empirical Evidence

#### Résultats clés
| Portefeuille | Sharpe ratio |
|---------------|--------------|
| ALL | 0.40 |
| Not FOMC | 0.30 |
| FOMC only | **2.57** |

→ La majorité des gains cumulés du marché US proviennent **uniquement des jours FOMC**.

#### Volatilité et Sharpe
- Volatilité légèrement ↑ sur jours FOMC
- Sharpe ratios très supérieurs :
  - Mkt-RF : 2.6 vs 0.3
  - SMB : 1.9 vs 0.0
  - HML : 0.0 vs –0.5

#### Robustesse
Les résultats persistent même après exclusion des outliers (3σ clipping).

---

### 🧩 Interprétation

| Observation | Explication |
|--------------|-------------|
| Actions ↑ | Prime de risque anticipée liée à l’incertitude |
| T-Bills ↓ | Précaution → “flight to safety” |
| Sharpe ratio FOMC ↑↑ | Concentration des rendements |
| Pas de lien avec surprise | Anticipation > contenu de l’annonce |

---

### 📊 Conclusion

- Les marchés offrent une **prime de risque pour les jours d’annonce macro**.
- Les **jours FOMC concentrent la performance de long terme** des actions.
- L’information en soi compte moins que **le risque perçu avant sa publication**.

> _La prime de risque macroéconomique est avant tout une prime d’incertitude._

---
## 📘 Chapitre 23 – *Text Representation and Sentiment Analysis of FOMC Statements: From Bag-of-Words to Deep Learning Embeddings*

---

### 🌐 Contexte général
Ce chapitre présente l’évolution des méthodes de représentation du texte appliquées aux **communiqués de la Federal Open Market Committee (FOMC)**.  
L’objectif est de transformer les textes en **représentations numériques** (vecteurs) exploitables pour des analyses quantitatives et de machine learning.  
Ces représentations permettent d’analyser le **ton**, la **structure** et l’**évolution temporelle** du discours monétaire de la Fed.

---

### 🧩 23.1 – Chargement et préparation des données
Les communiqués sont chargés sous forme de jeu de données contenant :
- Le **texte** complet du communiqué,
- La **date de publication**,
- Des **métadonnées** (type de décision, contexte économique, etc.).

Certaines dates clés (ex. 2008, 2010, 2020) sont utilisées pour illustrer les changements de ton et de thématique au fil du temps.

---

### 💬 23.2 – Bag-of-Words et analyse de sentiment

#### 🔹 Principe du Bag-of-Words (BOW)
- Chaque texte est converti en un **vecteur de fréquences de mots**.
- L’ordre des mots et la grammaire sont ignorés : seul le **comptage** importe.
- Chaque dimension du vecteur correspond à un mot du dictionnaire.

#### 🔹 Analyse de sentiment
- Le ton des communiqués est évalué à l’aide du **lexique financier de Loughran–McDonald**.
- Deux scores de sentiment sont définis :

  \[
  \text{sentiment}_1 = \frac{\#positifs - \#négatifs}{\#positifs + \#négatifs}
  \quad ; \quad
  \text{sentiment}_2 = \frac{\#positifs - \#négatifs}{\#mots\ totaux}
  \]

- Ces indicateurs montrent que :
  - Les **pics négatifs** correspondent aux crises (2008, COVID-19, etc.).
  - Le ton devient plus **positif** en période de stabilité économique.

---

### 📈 23.3 – TF-IDF : pondération contextuelle

#### 🔹 Principe
Le **TF-IDF (Term Frequency – Inverse Document Frequency)** améliore la méthode Bag-of-Words en pondérant les mots selon leur importance :
- Le **TF** mesure la fréquence d’un mot dans un document.
- L’**IDF** pénalise les mots très fréquents dans tous les documents.
  
Formule simplifiée :

\[
\text{tfidf}_{i,j} = \text{tf}_{i,j} \times \log\left(\frac{N}{df_j}\right)
\]

Ainsi, un mot fréquent dans un texte mais rare ailleurs aura un poids plus élevé.

#### 🔹 Résultats
- Environ **4 300 termes** sont retenus après filtrage.
- Les termes les plus importants sont : *“securities”, “growth”, “rate”, “labor market”, “monetary policy”*.
- Une transformation logarithmique (\(x \mapsto \log(1+x)\)) réduit l’effet des mots surpondérés.
- Le vocabulaire dominant reflète la politique monétaire et les priorités économiques de la Fed.

---

### 🧮 23.3.1 – Analyse en Composantes Principales (PCA)

### 🔹 Objectif
Réduire la dimension de la matrice TF-IDF tout en conservant l’information essentielle.  
La décomposition en valeurs singulières (SVD) permet d’obtenir :

\[
X \approx U_n \times \text{Diag}(s_n) \times W_n^T
\]

#### 🔹 Interprétation
- **PC0** : associée au marché du travail (*employment, labor market*).  
- **PC1** : liée à la croissance économique (*growth, activity, output*).  
- **Évolution temporelle** :
  - 1999–2009 → accent sur la croissance.  
  - 2010–2020 → recentrage sur l’emploi.  
  - 2021–2023 → ton plus neutre et moins explicable par ces axes.

Cette analyse révèle les thèmes dominants des discours et leur évolution au fil du temps.

---

### 🔍 23.3.2 – Clustering K-Means

#### 🔹 Objectif
Identifier automatiquement des **groupes de communiqués** partageant un vocabulaire similaire.

#### 🔹 Méthode et résultats
- Application d’un **K-Means** à 6 clusters sur la matrice TF-IDF.
- Chaque cluster regroupe des documents liés à une thématique :
  - *Financial stability*, *price stability*, *labor market conditions*, etc.
- L’erreur de reconstruction (mesurée avec la norme de Frobenius) est proche de celle de la PCA :
  - PCA : 11.19  
  - K-Means : 11.48  
- Ces regroupements révèlent des **régimes de communication** : avant/après crises, périodes de volatilité, etc.

---

### 🧩 23.3.3 – Non-Negative Matrix Factorization (NMF)

#### 🔹 Objectif
Extraire des **thèmes latents positifs** sans interprétation négative : chaque communiqué est une **combinaison additive** de thèmes.

\[
X \approx W H^T
\]

#### 🔹 Résultats
- Erreur d’approximation :  
  PCA = 11.19 ; K-Means = 11.48 ; NMF = 11.23  
- Les thèmes identifiés incluent :
  - Croissance et emploi,  
  - Inflation et stabilité des prix,  
  - Politique monétaire accommodante.  
- Les thèmes NMF sont temporellement concentrés (chaque période correspond à un thème dominant).

---

### 🤖 23.4 – Deep Learning Embeddings (Sentence Transformers)

#### 🔹 Contexte
Les méthodes précédentes ne capturent ni la **syntaxe** ni la **sémantique**.  
Les modèles de **type BERT** permettent une représentation **contextuelle** : le même mot peut avoir une signification différente selon la phrase.

#### 🔹 Méthode
- Utilisation du modèle **all-distilroberta-v1** (82 millions de paramètres, 768 dimensions).  
- Chaque texte est transformé en **vecteur dense** capturant son sens global.  
- Deux phrases ayant un sens proche ont des vecteurs voisins dans l’espace d’embedding.

#### 🔹 Intérêt
Les embeddings profonds offrent une représentation fine du ton et du contenu des communiqués, sans dépendre d’un lexique externe.

---

### 🌌 23.5 – UMAP : visualisation non linéaire

#### 🔹 Objectif
Réduire la dimension des embeddings (TF-IDF ou BERT) pour visualiser les **similarités entre textes**.

#### 🔹 Méthode
L’algorithme **UMAP (Uniform Manifold Approximation and Projection)** préserve à la fois :
- La **structure locale** (proximité entre documents similaires),
- Et la **structure globale** (grandes tendances du corpus).

#### 🔹 Résultats
- Les points (documents) sont colorés par année :
  - 1999–2007 : ton stable et neutre.  
  - 2008–2010 : forte concentration → crise financière.  
  - 2020–2021 : cluster spécifique lié à la pandémie.  
  - 2022–2025 : dispersion accrue → incertitude post-pandémie et inflation.  
- UMAP permet de **visualiser l’évolution sémantique** du discours monétaire de la Fed.

---

### 🧠 Synthèse comparative

| Méthode | Type | Avantages | Limites |
|----------|------|------------|----------|
| **PCA** | Linéaire | Réduction claire, axes interprétables | Ignore les non-linéarités |
| **K-Means** | Clustering | Regroupement thématique automatique | Sensible au choix de *k* |
| **NMF** | Factorisation | Thèmes lisibles et additifs | Clustering temporel marqué |
| **Sentence Transformers** | Deep learning | Capture le sens et le contexte | Peu interprétable |
| **UMAP** | Visualisation | Représentation sémantique claire | Paramétrage délicat |

---

### 🌟 Conclusion générale
Ce chapitre illustre la progression des approches de **représentation textuelle** :
> Du comptage brut (Bag-of-Words) à la **compréhension sémantique** (Transformers).

Chaque étape ajoute un niveau d’intelligence :
1. **Bag-of-Words** → fréquence brute.  
2. **TF-IDF** → pondération contextuelle.  
3. **PCA / NMF / K-Means** → extraction de thèmes et structures.  
4. **BERT / UMAP** → représentation sémantique profonde et visualisation des régimes discursifs.

🎯 **Enjeu principal :**  
Mieux comprendre l’évolution du ton et des priorités de la politique monétaire américaine à travers les communiqués du FOMC.

# 📗 Chapitre 24 – *Supervised Learning on FOMC Statements: Predicting Rate Decisions and Market Reactions*

## 🎯 Objectif général
Ce chapitre applique des méthodes d’**apprentissage supervisé** sur les communiqués de la **Federal Open Market Committee (FOMC)** pour :
- prédire les **décisions de taux d’intérêt** (hausse, baisse, statu quo),
- modéliser les **réactions de marché** aux annonces,
- et comprendre le **langage monétaire** de la Fed via les mots et expressions utilisés.

L’analyse combine plusieurs représentations textuelles : **TF-IDF** et **Sentence Transformers (embeddings profonds)**, intégrées à des modèles linéaires régularisés de type **Elastic Net**.

---

## 🧩 24.1 – Supervised Learning : vector representation + Elastic Net

### 🔹 Objectif
Associer chaque communiqué à la **décision du FOMC** : hausse, baisse ou statu quo.  
Les modèles visent à détecter les signaux linguistiques précurseurs de ces décisions.

### 🔹 Préparation des données
Les dates de modification du taux directeur sont classées en trois groupes :
- **fomc_change_up** → hausse de taux  
- **fomc_change_dw** → baisse de taux  
- **no change** → aucune modification  

Des événements particuliers sont aussi identifiés :
- **QE1–QE3**, **Twist**, **Corona**, etc.

---

### 🔹 Modélisation TF-IDF + Elastic Net
Les textes sont vectorisés avec un **TF-IDF** (1 à 3 n-grammes, 500 mots max, suppression des stopwords).  
Le modèle supervisé est une **régression logistique** avec pénalisation **Elastic Net**, qui combine :
- L1 (Lasso) → sélection de variables,
- L2 (Ridge) → stabilité des coefficients.

Les prédictions indiquent :
- `+1` → hausse de taux,
- `-1` → baisse de taux.

### 🔹 Interprétation des coefficients
Les coefficients les plus marquants sont visualisés :

- **Coefficients positifs** → mots corrélés à une hausse de taux  
  (*inflation, growth, labor market, policy normalization, stability, etc.*)  
- **Coefficients négatifs** → mots associés à une baisse de taux  
  (*crisis, uncertainty, slowdown, volatility, financial stress, etc.*)

➡️ Ces termes reflètent les priorités macroéconomiques perçues par la Fed à chaque période.

---

### 🔹 Taux implicite et visualisation
Le modèle produit une série temporelle du **taux implicite prédit**.  
Cette série est comparée aux décisions réelles :
- Hausses → barres orange,  
- Baisses → barres vertes,  
- Taux implicite → courbe bleue.

Les périodes de crise (2008, 2020) ressortent clairement, avec des prédictions extrêmes liées aux événements économiques majeurs.

---

### 🔹 Lexique positif et négatif
Deux ensembles de mots sont extraits :
- **Positive words** : annonciateurs de resserrement monétaire,  
- **Negative words** : annonciateurs d’assouplissement.

Ces lexiques permettent une lecture qualitative du discours de la Fed.

---

## 🔬 24.1.1 – Comparison with Sentence Transformer Embeddings

### 🔹 Objectif
Comparer la performance du modèle TF-IDF à celle d’un modèle utilisant des **Sentence Transformers** (embeddings contextuels).

### 🔹 Méthode
- Utilisation du modèle **all-distilroberta-v1**, qui encode chaque communiqué en un vecteur dense (768 dimensions).  
- Entraînement d’une **ElasticNet** sur ces embeddings pour prédire les décisions de taux.

### 🔹 Résultats
- Les prédictions TF-IDF et SBERT sont **corrélées à 0.68**.  
- Les deux représentations identifient des régimes similaires de politique monétaire.
- Le graphique montre des courbes très proches, confirmant que les embeddings profonds capturent des signaux économiques analogues à ceux du TF-IDF, mais avec davantage de contexte sémantique.

---

## 💹 24.2 – Sentiment in FOMC Statements: Supervised Learning

### 🔹 Objectif
Étendre le modèle supervisé pour **lier le ton du communiqué à la réaction du marché**.  
On cherche à prédire le **rendement du marché (Mkt-RF)** le jour de la publication du communiqué, en utilisant le texte comme variable explicative.

---

### 🔹 Données et pipeline
1. Les **rendements de marché** sont chargés depuis la base Fama-French (`F-F_Research_Data_Factors_daily`).
2. Des **dates spéciales** (crises majeures, interventions exceptionnelles) sont isolées pour éviter les distorsions.
3. Le pipeline combine :
   - **TF-IDF** vectorizer (1–3-grammes, 500 termes, stopwords anglais),
   - **ElasticNet (α = 0.0075)** pour la régression linéaire.

---

### 🔹 Résultats de la régression
Les coefficients estimés permettent d’identifier les mots les plus liés aux réactions de marché.

- **Coefficients positifs (hausse du marché)** :
  *quarters, financial markets, year, economic growth, markets, credit, effects, economy, balance, ongoing*

- **Coefficients négatifs (baisse du marché)** :
  *long, employment, monetary, spending, developments, strong, monetary policy, subdued, closely, sustained*

➡️ Le ton des communiqués influence donc significativement la réaction du marché :
- Les messages optimistes (croissance, stabilité) génèrent des rendements positifs.
- Les messages prudents ou restrictifs entraînent des rendements négatifs.

---

### 🔹 Lexique de sentiment
À partir des coefficients, on construit deux lexiques :
- **Positive lexicon** → termes signalant un ton favorable au marché,
- **Negative lexicon** → termes associés à un ton restrictif ou pessimiste.

Ces lexiques constituent une base de **sentiment analysis financière** applicable à d’autres contextes (autres banques centrales, communiqués de résultats, etc.).

---

### 🔹 Visualisation et interprétation
Les prédictions sont ordonnées par intensité, et les textes les plus extrêmes (positifs/négatifs) sont affichés.  
L’analyse qualitative montre que les périodes de politique monétaire accommodante sont associées à un langage plus positif, tandis que les périodes de resserrement utilisent un ton plus prudent.

---

## 🧠 Synthèse du chapitre 24

| Thème | Approche | Objectif | Résultat clé |
|-------|-----------|-----------|---------------|
| **Prédiction des décisions de taux** | TF-IDF + ElasticNet | Relier vocabulaire et décisions du FOMC | Identification de mots “hawkish” et “dovish” |
| **Comparaison TF-IDF / SBERT** | Embeddings contextuels | Tester les modèles profonds | Corrélation 0.68, résultats cohérents |
| **Réaction du marché** | TF-IDF + ElasticNet | Prédire les rendements de marché à partir du ton | Identification de lexiques positifs/négatifs |
| **Lexiques interprétables** | Extraction de coefficients | Comprendre le ton monétaire | Sentiment monétaire mesurable et explicable |

---

## 🌟 Conclusion générale
Ce chapitre démontre la puissance des méthodes d’**apprentissage supervisé appliquées aux textes économiques** :
- Les **communiqués du FOMC** contiennent des signaux prédictifs des décisions et des réactions de marché.  
- Les **modèles linéaires régularisés** offrent une interprétation claire du rôle des mots dans le ton monétaire.  
- Les **Sentence Transformers** améliorent la compréhension contextuelle, sans perte de cohérence avec les approches classiques.

🎯 **En résumé :**  
L’analyse du langage de la Fed par apprentissage supervisé permet de relier **les mots aux marchés**, et de mesurer la **dimension informationnelle** des communiqués monétaires.
