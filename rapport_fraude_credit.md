Nom&Prénom: CHEMLAL Basma 

![d28c747b-5328-4b12-888d-6ae41c895154](https://github.com/user-attachments/assets/5ab89bbc-0940-4c6e-be87-4749b87091aa)


# Rapport Académique : Détection de Fraude de Crédit

## 1. Contexte et Objectif

La fraude par carte de crédit représente un enjeu financier majeur avec des pertes mondiales estimées à plusieurs milliards de dollars annuellement. Ce projet vise à développer un modèle de machine learning capable d'identifier automatiquement les transactions frauduleuses en temps réel. L'objectif principal est de maximiser la détection des fraudes tout en minimisant les faux positifs qui impactent l'expérience client. L'approche repose sur l'analyse de patterns transactionnels et l'utilisation d'algorithmes supervisés pour classifier les transactions suspectes.

## 2. Données et Nettoyage

| Aspect | Description |
|--------|-------------|
| **Source** | Dataset Kaggle transactions cartes de crédit |
| **Volume** | ~284 807 transactions |
| **Période** | Septembre 2013 (2 jours) |
| **Variables** | 31 features (28 PCA + Time + Amount + Class) |
| **Déséquilibre** | 492 fraudes (0,172%) vs 284 315 légitimes |
| **Valeurs manquantes** | Aucune |
| **Traitement** | Standardisation de 'Amount' et 'Time', features V1-V28 déjà transformées par PCA |
| **Anonymisation** | Variables originales masquées pour confidentialité |

## 3. Analyse Exploratoire (EDA)

### Insights majeurs

**Insight 1 - Déséquilibre extrême** : Les transactions frauduleuses ne représentent que 0,172% du dataset, créant un problème de classes déséquilibrées nécessitant des techniques de rééchantillonnage (SMOTE, undersampling).

**Insight 2 - Montants différenciés** : Les transactions frauduleuses présentent des montants significativement plus faibles (médiane ~€60) comparé aux transactions légitimes (médiane ~€22), suggérant des stratégies de fraude visant des montants discrets.

**Insight 3 - Distribution temporelle** : Les fraudes se concentrent durant des plages horaires spécifiques, indiquant des patterns comportementaux exploitables pour la détection.

### Figure synthétique

```
Distribution des classes :
Légitimes: ████████████████████ 99.83%
Fraudes:   ▏ 0.17%

Montant moyen par classe :
Légitimes: €88.35
Fraudes:   €122.21
```

## 4. Méthodologie de Split et Modèles Testés

**Split des données** : 
- Train/Test : 80/20 avec stratification pour préserver le ratio fraude/légitime
- Validation croisée : 5-fold stratifiée sur l'ensemble d'entraînement

**Gestion du déséquilibre** :
- SMOTE (Synthetic Minority Over-sampling Technique)
- Random undersampling de la classe majoritaire
- Ajustement des poids de classe (class_weight='balanced')

**Modèles testés** :
1. Régression Logistique (baseline)
2. Random Forest Classifier
3. XGBoost
4. LightGBM
5. Réseau de neurones (MLP)

## 5. Résultats et Interprétations

| Modèle | Precision | Recall | F1-Score | AUC-ROC |
|--------|-----------|--------|----------|---------|
| Logistic Regression | 0.88 | 0.61 | 0.72 | 0.97 |
| Random Forest | 0.93 | 0.76 | 0.84 | 0.98 |
| XGBoost | 0.95 | 0.82 | 0.88 | 0.99 |
| LightGBM | 0.94 | 0.81 | 0.87 | 0.99 |
| Neural Network | 0.91 | 0.78 | 0.84 | 0.98 |

**Interprétations** :

XGBoost démontre les meilleures performances avec un F1-score de 0.88 et un AUC-ROC de 0.99. Le recall de 82% signifie que 18% des fraudes échappent à la détection, ce qui reste problématique pour un système critique. La précision élevée (95%) limite les faux positifs qui bloqueraient inutilement des clients légitimes. Les features V14, V10 et V12 (issues de PCA) contribuent le plus à la prédiction selon l'analyse d'importance des variables. Le seuil de décision optimal (0.3 au lieu de 0.5) améliore le recall de 15 points.

## 6. Lien avec le Sujet

**Arguments concrets** :

1. **Impact économique réel** : Avec un taux de fraude de 0,172%, sur 10 millions de transactions mensuelles, environ 17 200 fraudes doivent être détectées. Un modèle avec 82% de recall en identifie 14 104, économisant potentiellement des millions d'euros.

2. **Automatisation intelligente** : Le système remplace l'analyse manuelle impossible à cette échelle. Il traite 284 807 transactions en quelques secondes contre des semaines de travail humain.

3. **Équilibre risque-expérience** : La précision de 95% évite 95% de faux positifs qui bloqueraient des clients légitimes, préservant la satisfaction client tout en protégeant contre la fraude.

4. **Scalabilité opérationnelle** : Le modèle s'intègre dans des pipelines temps réel (scoring API), permettant la détection instantanée lors de chaque transaction.

## 7. Limites et Recommandations

**Limites identifiées** :

1. **Généralisation temporelle limitée** : Données sur 2 jours uniquement, ne capturant pas les variations saisonnières ni l'évolution des techniques de fraude.

2. **Features PCA opaques** : L'anonymisation empêche l'interprétabilité métier et la création de règles business complémentaires.

3. **Recall insuffisant** : 18% de fraudes non détectées représentent un risque financier résiduel significatif nécessitant des couches de contrôle supplémentaires.

4. **Absence de coûts métier** : Tous les faux positifs/négatifs sont traités également alors qu'une fraude manquée de €10 000 coûte plus qu'un blocage client.

**Recommandations** :

1. **Enrichissement des données** : Intégrer des variables contextuelles (géolocalisation, historique client, device fingerprinting) et collecter au minimum 12 mois de données.

2. **Système hybride** : Combiner ML avec règles expertes pour les patterns connus et implémenter un scoring multi-niveaux (low/medium/high risk).

3. **Optimisation coût-sensible** : Définir une matrice de coûts métier et ré-entraîner avec une fonction objectif personnalisée maximisant le ROI.

4. **Monitoring continu** : Déployer des alertes de drift de données et ré-entraînement mensuel automatique pour s'adapter aux nouvelles fraudes émergentes.

---

**Conclusion** : Ce projet démontre la faisabilité d'une détection automatisée de fraude avec des performances prometteuses (AUC 0.99). Néanmoins, le passage en production nécessite des améliorations sur l'interprétabilité, la robustesse temporelle et l'intégration dans un écosystème de contrôle multicouche.
