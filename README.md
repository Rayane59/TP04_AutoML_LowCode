# TP04 - AutoML : explorer les plateformes AutoML et MLOps low-code

Projet de detection de fraude (suite du TP2). Compare un modele de reference,
le reglage manuel du TP2 et une recherche AutoML locale (FLAML), puis montre
qu'un AutoML ne detecte pas une fuite de donnees.

Environnement : Python 3.12, FLAML 2.7.0 (pas de plan B necessaire).

## Reproduire
    python -m venv .venv ; .venv\Scripts\Activate.ps1
    pip install -r requirements.txt
    python donnees.py
    mlflow server --host 127.0.0.1 --port 5000 --backend-store-uri sqlite:///mlflow.db --default-artifact-root ./mlartifacts
    python src/references.py
    python src/automl.py 10 ; python src/automl.py 30 ; python src/automl.py 60 ; python src/automl.py 120
    python src/rendement.py
    python src/inspecter.py reports/flaml_60s.log
    python src/fuite.py

Voir `reports/comparatif.md` pour le tableau comparatif.
