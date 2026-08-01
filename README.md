# DOCUMENTATION TECHNIQUE: Audit & Clean Data Lampadaires Togo

### Pipelines d'Ingestion SQL, Nettoyage de Données de Terrain et Analyse d'Électrification Rurale

---

## 1. Présentation du Projet
Ce projet simule le flux complet d'ingénierie et d'analyse de données d'un inventaire d'éclairage public (lampadaires solaires/réseau) au Togo. Le traitement couvre la collecte brute via formulaire terrain (KoboToolbox / Excel), le stockage initial dans une base de données relationnelle SQLite, le nettoyage rigoureux et l'harmonisation sous Python (Pandas), puis la restitution d'indicateurs clés de performance (KPI) pour les décideurs publics.

---

## 2. Structure de la Base de Données
La base de données SQLite `togo_energie.db` est initialement composée de deux tables:
* **`regions_togo`** : Table de référence des 6 régions administratives du Togo et leurs chefs-lieux (Savanes, Kara, Centrale, Plateaux, Maritime, Grand Lomé).
* **`lampadaires_brut`** : Table de données brutes contenant les anomalies de collecte terrain.
* **`lampadaires_propres` & `kpi_pannes_territoire`** : Tables nettoyées et agrégées produites post-traitement.

---

## 3. Pipeline de Nettoyage et Traitement de Données
Le script Python applique un protocole d'audit et de correction structuré en 5 étapes:

| # | Anomalie Détectée | Méthode de Correction / Règle Métier |
| :-: | :--- | :--- |
| **1** | Doublons stricts (ex: LAMP-005 répété) | Déduplication basée sur l'identifiant unique `id_lampadaire`. |
| **2** | Incohérences de casse et typographie (ex: savanes, tone) | Normalisation avec `strip()`, passage en Title Case et correction d'accentuation (ex: Tône). |
| **3** | Unités parasites & texte ('60W', 'missing') | Extraction numérique de la puissance, conversion en entiers et imputation des valeurs manquantes par la médiane (60 W). |
| **4** | Valeurs manquantes du statut fonctionnel (NaN) | Catégorisation explicite sous l'étiquette "À vérifier" pour contrôle qualité ultérieur. |
| **5** | Coordonnées GPS aberrantes (ex: Lat -1.0 dans le golfe de Guinée) | Filtrage géographique strict selon les bornes nationales togolaises (Lat: 6.0° à 11.2° N, Lon: 0.0° à 2.0° E). Isolation des anomalies pour maintenance. |

---

## 4. Indicateurs Clés de Performance (KPIs)

| PARC ANALYSÉ | PUISSANCE TOTALE | TAUX DE PANNE NATIONAL |
| :---: | :---: | :---: |
| **7 Unités** | **500 W** | **14.3%** |

---

## 5. Synthèse Stratégique & Insight Analyste

### Key Business & Operational Insight

1. **Vulnérabilité Critique dans la Région des Savanes (Préfecture de Tône) :**  
   Bien que le taux de panne national s'élève à 14.3%, l'analyse désagrégée par territoire révèle une forte disparité géographique: la préfecture de Tône (Région des Savanes) concentre un taux de panne critique de 50.0% (1 lampadaire hors service sur 2), alors que les préfectures de Kozah, Tchaoudjo, Zio et Ogou affichent un taux de panne opérationnel de 0.0%.

2. **Alerte Qualité Géospatiale & Audit Logistique :**  
   L'audit de données a mis en évidence une aberration spatiale majeure (LAMP-007 localisé à la latitude -1.0, soit en plein océan Atlantique). Cela indique soit un mauvais paramétrage des terminaux KoboToolbox des enquêteurs, soit une saisie manuelle erronée.

### Recommandation Actionnable pour les Chefs de Projet
* **Priorisation de la maintenance :** Déployer immédiatement une équipe d'intervention technique dans la zone de Tône (Dapaong) pour réduire le déficit d'éclairage.
* **Standardisation de la collecte :** Activer le verrouillage GPS avec contrôle d'étendue géographique (*geofencing*) sur les formulaires de collecte pour éliminer les erreurs de coordonnées à la source.

---

## 6. Guide d'Exécution

1. Lancer l'initialisation de la base SQLite et la création des tables de référence.
2. Exécuter le script d'extraction SQL `pd.read_sql_query()`
3. Appliquer le pipeline de nettoyage Pandas.
4. Exécuter la sauvegarde des tables propres `lampadaires_propres` et `kpi_pannes_territoire` dans la base SQLite.
5. Générer le graphique de restitution avec Matplotlib / Seaborn.
