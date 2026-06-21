# 🔌 La Fracture Verte
### Inégalités de l'infrastructure de recharge électrique à Paris

> **EN —** A data study testing whether Paris's public EV-charging network (Belib') is distributed equitably across the city's 20 districts, or whether it mirrors existing income inequality. Built entirely on live Paris Open Data + INSEE data, with statistical hypothesis testing (Spearman correlation) and an interactive Plotly dashboard. A companion Power BI report is included.

---

## 🧪 Thèse

> Le réseau public de bornes Belib', financé par une concession municipale de la Ville de Paris, est-il distribué équitablement entre arrondissements ? Ou reproduit-il les inégalités de revenu déjà présentes dans la ville ?

Trois hypothèses sont testées statistiquement pour y répondre.

## 📐 Hypothèses testées

| # | Hypothèse | Métrique | Test |
|---|-----------|----------|------|
| **H1** | Les arrondissements riches ont une meilleure couverture | Bornes / 10 000 hab. vs revenu médian | Corrélation de Spearman |
| **H2** | Les bornes rapides (>22 kW) sont concentrées chez les riches | % bornes rapides vs revenu médian | Corrélation de Spearman |
| **H3** | Double pénalité : moins de bornes ET plus de pannes | Couverture vs % hors service | Corrélation de Spearman |

## 🗂️ Sources de données

| Dataset | Source | Accès |
|---|---|---|
| Belib' — données statiques (localisation, puissance) | Paris Open Data | API REST v2.1 (live, sans fichier statique) |
| Belib' — disponibilité temps réel | Paris Open Data | API REST v2.1 |
| GeoJSON des arrondissements | Paris Open Data | API REST v2.1 |
| Population par arrondissement (2020) | INSEE — Recensement | Données publiques |
| Revenu médian par UC (2021) | INSEE — FiLoSoFi | Données publiques |

Toutes les données Belib' sont récupérées **en direct via l'API**, avec gestion de la pagination — aucune dépendance à un export figé.

## 📊 Résultats

**Réseau analysé : 1 907 bornes Belib' sur les 20 arrondissements parisiens.**

| Hypothèse | ρ de Spearman | p-value | Verdict |
|---|---|---|---|
| H1 — Couverture | **+0,841** | < 0,0001 | ✅ Confirmée — corrélation forte |
| H2 — Qualité (bornes rapides) | **+0,513** | 0,021 | ✅ Confirmée — corrélation modérée |
| H3 — Double pénalité (pannes) | +0,15 à +0,21 | 0,37 – 0,54 | ❌ Non confirmée — signal insuffisant (18 bornes en panne sur ~1 926) |

### La fracture verte, en chiffres

| | 8ᵉ arrondissement 💚 | 19ᵉ arrondissement 🔴 | Écart |
|---|---|---|---|
| Bornes / 10 000 hab. | 34,1 | 4,5 | **×7,5** |
| % bornes rapides | 12 % | 0 % | — |
| Revenu médian | 46 900 €/an | 24 400 €/an | +22 500 €/an |

Un habitant du 8ᵉ a accès à **7,5 fois plus** de bornes par habitant qu'un habitant du 19ᵉ — et aucune de ces rares bornes n'y est rapide.

### Analyse temporelle du déploiement

88 % du réseau (1 689 bornes) a été déployé en une seule fois en 2021, au lancement de la concession publique, avec le même biais de revenu que la situation actuelle (ρ = +0,841). Les extensions post-2021 (12 %, 218 bornes) sont statistiquement neutres (ρ = +0,094, p = 0,69) : elles ne creusent pas l'écart, mais ne le comblent pas non plus. **L'inégalité a été programmée dès le lancement, pas le fruit d'une dérive progressive.**

⚠️ *Limite identifiée :* le champ `date_mise_en_service` semble en partie refléter la date de dernière mise à jour du dataset plutôt que la date réelle de déploiement. L'analyse temporelle est donc indicative et présentée comme telle dans le notebook, pas comme une preuve définitive.

## 📁 Contenu du dépôt

```
.
├── EV_Inequality_Paris.ipynb          # Notebook complet : collecte API, nettoyage, tests stats, visualisations
├── Analyse_des_bornes_belib.pbit      # Dashboard Power BI complémentaire
├── Belib_EV_Inequality_Paris.xlsx     # Données exportées (voir détail ci-dessous)
└── README.md
```

Le fichier `.xlsx` contient les données nettoyées en sortie de notebook, sur 4 feuilles :

| Feuille | Contenu |
|---|---|
| `Analyse_Arrondissement` | Table agrégée par arrondissement (couverture, % rapide, revenu, quartile, disponibilité) — base des tests d'hypothèses |
| `Bornes_Brutes` | 1 907 bornes individuelles (coordonnées, puissance, statut, adresse) |
| `Disponibilite_TempReel` | Relevé temps réel de disponibilité par borne |
| `Resultats_Stats` | Tableau récapitulatif des tests de Spearman (H1, H2) |

## 🛠️ Stack technique

- **Collecte** : `requests` (API Paris Open Data REST v2.1, pagination automatique)
- **Traitement** : `pandas`, `numpy`
- **Statistiques** : `scipy.stats` (corrélation de Spearman)
- **Visualisation** : `plotly` (cartes choroplèthes, scatter plots interactifs), `matplotlib`, `seaborn`
- **Environnement** : Google Colab
- **BI complémentaire** : Power BI (.pbit)

## ▶️ Lancer le notebook

```bash
pip install pandas numpy matplotlib seaborn plotly scipy requests
jupyter notebook EV_Inequality_Paris.ipynb
```

Toutes les données sont interrogées en direct depuis l'API — aucune clé ni fichier local n'est nécessaire pour reproduire l'analyse.

## ⚠️ Limites de l'étude

- H3 (double pénalité) repose sur un échantillon temps réel restreint (18 bornes en panne sur ~1 926) — à retester sur une fenêtre d'observation plus longue.
- L'analyse temporelle du déploiement est fragilisée par l'ambiguïté du champ `date_mise_en_service`.
- L'étude est corrélationnelle : elle ne permet pas d'établir un lien de causalité directe entre revenu et implantation des bornes, seulement une association statistique forte.

---

**Auteure :** Hajer — [github.com/hajer5503](https://github.com/hajer5503)
