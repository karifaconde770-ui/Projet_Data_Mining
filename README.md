# Projet_Data_Mining
# Segmentation Client Mobile Money — Guinée

**Groupe 2**

Projet de data science : segmentation de la clientèle d'un opérateur Mobile Money en Guinée, du prétraitement des transactions jusqu'aux recommandations marketing, via clustering K-means et CHA.

## Objectif

Construire une segmentation actionnable de la clientèle à partir de l'historique de transactions (dépôt, retrait, transfert, paiement de facture, achat de crédit) afin de personnaliser les offres et campagnes marketing par profil client.

## Structure du projet

```
Data Minig/
├── data/                          # Données brutes et artefacts générés
│   ├── transactions_mobile_money.csv       # 50 000 transactions, 5 000 clients (simulées)
│   ├── client_features_raw.csv             # Dataset client après feature engineering
│   ├── client_features_anonymized.csv      # Features client sans client_id (livrable externe)
│   ├── cluster_profile_mean.csv            # Profil moyen par segment
│   ├── segment_summary.csv                 # Résumé des segments (nom, effectif, KPI)
│   └── results_summary.json                # Métriques du pipeline (K, silhouette, ARI, ...)
├── figures/                       # Graphiques exportés (coude, silhouette, ACP, clusters, dendrogramme)
├── notebook/
│   └── segmentation_mobile_money.ipynb     # Notebook complet, exécuté (analyse de bout en bout)
├── rapport/
│   └── rapport_executif_segmentation.docx  # Rapport exécutif (2 pages)
├── presentation/
│   └── presentation_comite_direction.pptx  # Présentation comité de direction (10 slides)
└── scripts/                       # Scripts source, reproductibles (seed=42)
    ├── generate_data.py            # Génération des données simulées
    ├── analysis.py                 # Pipeline complet (feature engineering → clustering → segments)
    ├── build_notebook.py           # Construction du notebook à partir du pipeline
    ├── build_report.py             # Génération du rapport Word
    └── build_ppt.py                # Génération de la présentation PowerPoint
```

## Méthodologie

1. **Feature engineering client** : RFM enrichi (récence, fréquence, montant total/moyen, volatilité, fréquence hebdomadaire, répartition par type de transaction) — 12 variables par client.
2. **Traitement des valeurs aberrantes** : winsorisation 1%/99%, documentée, aucun client exclu.
3. **Transformation & normalisation** : log1p sur variables monétaires/comptage (distributions à queue lourde) + StandardScaler (Z-score).
4. **ACP** : exploration de la structure, visualisation 2D.
5. **Clustering K-means** : choix de K par coude + silhouette, contraint à une plage business exploitable (3 à 7 segments) pour éviter les solutions dégénérées (2 méga-groupes ou sur-fragmentation).
6. **Classification Hiérarchique Ascendante (Ward)** : validation croisée via indice de Rand ajusté.
7. **Caractérisation & nommage** : profils par segment, règles métier, personas.
8. **Recommandations marketing** : offre, canal, message clé et KPI par segment.

## Reproduire l'analyse

```bash
cd scripts
python generate_data.py       # génère data/transactions_mobile_money.csv
python analysis.py            # exécute le pipeline complet, exporte data/ et figures/
python build_notebook.py      # construit notebook/segmentation_mobile_money.ipynb
python -m nbconvert --to notebook --execute --inplace ../notebook/segmentation_mobile_money.ipynb
python build_report.py        # génère rapport/rapport_executif_segmentation.docx
python build_ppt.py           # génère presentation/presentation_comite_direction.pptx
```

## Prérequis

```bash
pip install pandas numpy scipy scikit-learn matplotlib seaborn nbformat nbconvert ipykernel python-docx python-pptx openpyxl
```

## Confidentialité

Les livrables externes (rapport, présentation, `client_features_anonymized.csv`) ne contiennent aucun `client_id`.
