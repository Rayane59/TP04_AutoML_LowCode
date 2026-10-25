# Comparatif des approches - projet fraude (TP04)

Mesures locales : Python 3.12, FLAML 2.7.0, scikit-learn 1.9.1, jeu v1 (5000 transactions),
meme decoupage que le TP2 (25 % de test, `random_state=42`).
Documentation consultee le **2026-10-25**.

| Approche | F1 test | Temps humain | Temps machine | Cout estime (source, date) | Explicabilite | Export / reversibilite |
|---|---|---|---|---|---|---|
| Logistique (ref.) | 0.7407 | ~5 min (script fourni) | 0.13 s | 0 (poste local) | Maximale (coefficients) | Total |
| Manuel (TP2) | 0.8669 | Eleve : plusieurs essais compares a la main au TP2 | 3.34 s (un seul fit RF 500/16) | 0 (poste local) | Moyenne/Bonne (importances) | Total |
| FLAML 60 s | 0.8409 | Faible (une commande) | 60.3 s (114 essais) | 0 (poste local, electricite negligeable) | Moyenne/Basse (journal des essais, pas d'explication native) | Total (modele sklearn/lightgbm/xgboost local) |
| Vertex AI AutoML | non mesure | Faible (interface) | non mesure | Entrainement tabulaire : 21,252 $ par node-hour ([page tarifs Vertex AI](https://cloud.google.com/vertex-ai/pricing), 2026-10-25). Facturation au budget en node-hours, donc une recherche courte n'est pas facturee a la seconde (minimum a verifier) | Bonne (evaluation et importance des variables dans la console, a verifier) | Faible / verrouillage probable (export a verifier dans la doc) |
| SageMaker Autopilot | non mesure | Faible (Canvas ou API) | non mesure | Pas de tarif propre : on paie le calcul et le stockage utilises ([doc Autopilot](https://docs.aws.amazon.com/sagemaker/latest/dg/autopilot-automate-model-development.html), 2026-10-25). Montant exact via la page SageMaker pricing | Bonne : rapports d'importance des variables et de performance, notebooks generes ([doc Autopilot](https://docs.aws.amazon.com/sagemaker/latest/dg/autopilot-automate-model-development.html)) | Moyenne : artefacts et endpoint generes, notebooks reutilisables ; portabilite hors AWS a verifier |
| Azure Automated ML | non mesure | Faible (studio ou SDK) | non mesure | Pas de frais propres a Azure ML, seulement le calcul facture a la seconde ([tarifs Azure ML](https://azure.microsoft.com/en-us/pricing/details/machine-learning-services/), 2026-10-25). Pas trouve de mention officielle specifique a l'AutoML | Bonne : importance des variables ([doc automated ML](https://learn.microsoft.com/en-us/azure/machine-learning/concept-automated-ml)) | Bonne : modele `.pkl` et conversion ONNX documentes ([doc automated ML](https://learn.microsoft.com/en-us/azure/machine-learning/concept-automated-ml)) |

## Resultats FLAML mesures (budget -> F1 test)

| Budget | 10 s | 30 s | 60 s | 120 s |
|---|---|---|---|---|
| F1 test | 0.8441 | 0.8409 | 0.8409 | 0.8582 |
| Famille retenue | lgbm | lgbm | lgbm | xgboost |

## Commentaire (5 lignes)

1. **Logistique** : a garder comme reference et comme modele de secours quand l'explicabilite prime (F1 0.7407, sensiblement plus bas ici).
2. **Reglage manuel** : sur ce jeu (4 variables, 5000 lignes) il bat l'AutoML (0.8669 contre 0.8409 a 60 s) ; il se justifie quand l'equipe connait deja une bonne famille de modeles.
3. **FLAML en local** : utile pour explorer vite plusieurs familles et obtenir un journal auditable, mais le gain n'est pas garanti : 10 s donnent presque autant que 60 s, seul 120 s s'en approche (0.8582).
4. **Plateformes managees** : pertinentes pour une equipe sans infrastructure, avec gros volumes, besoin de deploiement et de rapports integres ; le cout se calcule en heures de calcul et le risque principal est le verrouillage.
5. **Dans tous les cas** : l'AutoML ne detecte pas une fuite de donnees (bloc D, F1 de 1.0 a 0.0) ; la comprehension des variables reste a la charge de l'equipe.

## Limites

- F1 des plateformes cloud : non mesure (aucun compte cloud, conformement au sujet).
- Les scores FLAML dependent de la machine (le budget est un temps) et d'un seul decoupage de test.
- Les points marques "a verifier" n'ont pas pu etre confirmes dans les pages consultees.
