├── README.md          : Présentation du projet.
│
├── app                : Application Streamlit : présentation du projet et démonstrations des modèles.
│   ├── app.py         : Point d'entrée de l'application.
│   ├── presentation.py, demo.py, absa.py, classique.py, graphiques.py, ui.py, config.py
│   ├── requirements.txt  : Bibliothèques et versions nécessaires pour lancer l'application.
│   └── README.md     : Guide détaillé de l'application.
│
├── data               : Données du projet.
│   ├── trustpilot_reviews_2005_HuggingFace.csv     : Corpus brut Hugging Face (123 181 avis).
│   ├── trust_pilot_reviews_data_2022_06_kaggle.csv : Corpus brut Kaggle (3 698 avis), corpus de contrôle.
│   ├── df_nlp.csv, df_2000.csv                     : Corpus de travail et échantillon stratifié de 2 000 avis.
│   ├── df_pilote_600.csv, df_reste_1400.csv        : Corpus pilote (GOLD) et reste (SILVER).
│   ├── annotation_2000_gold_plus_LLM_18_aspects_final.xlsx : Annotations GOLD et SILVER (18 aspects, un sentiment par aspect).
│   └── train_1600.csv, test_400.csv                : Découpage final entraînement / test.
│
├── figures           
│
├── models             : Modèles classiques et objets sauvegardés (.joblib)
│                         TF-IDF, liste des aspects, LR, SVM, XGBoost, recherches d'hyperparamètres.
│
├── notebooks          : Notebooks Jupyter, dans l'ordre du projet.
    ├── 01_comparaison_et_choix_datasets.ipynb   : Kaggle et Hugging Face comparés, choix du corpus.
    ├── 02_exploration_huggingface.ipynb         : Exploration du corpus retenu et de ses biais.
    ├── Constitution_corpus_2000avis.ipynb       : Échantillonnage, pilote, contrôle des annotations.
    ├── ML_Trustpilot_aspects.ipynb              : Tâche 1 avec TF-IDF : baselines, validation croisée, optimisation.
    ├── ML_Trustpilot_sentiments.ipynb           : Tâche 2 avec TF-IDF.
    ├── DeepL_Trustpilot_.ipynb                  : Deux modèles DistilBERT, un par tâche, et pipeline final.
    ├── distilbert_aspects_final                 : Modèle DistilBERT final des aspects.
    └── distilbert_sentiment_final               : Modèle DistilBERT final du sentiment.


# Trustpilot Aspect Intelligence : application Streamlit

Cette application a pour but de présenter le projet et de proposer deux démonstrations en direct à partir des modèles enregistrés dans nos notbooks. Aucun modèle n'est réentraîné : l'application lit les fichiers `.joblib` du dossier `models` ainsi que les deux dossiers DistilBERT du dossier `notebooks`.


## 1. Où placer le dossier app

Le dossier `app` est placé dans le dossier `Python` du projet, à côté des dossiers `data`, `models` et `notebooks` :

```
Part 2\Python\
    .venv\          l'environnement virtuel (créé à l'étape 2 ci-dessous)
    app\            ce dossier
    data\           test_400.csv, les deux fichiers .xlsx d'annotation, les deux corpus bruts .csv
    models\         tfidf_aspects.joblib, tfidf_sentiments.joblib, liste_aspects.joblib,
                    modele_{lr,lr_opt,svm,xgb}_{aspects,sentiments}.joblib, mapping_sentiment.joblib
    notebooks\      distilbert_aspects_final\ et distilbert_sentiment_final\
```

Contenu du dossier `app` :

| Fichier | Rôle |
|---|---|
| app.py | Point d'entrée : barre latérale, sommaire et boutons Précédente et Suivante |
| presentation.py | Diapositives, une fonction par section et un onglet par diapositive |
| demo.py | Démonstrations (un avis, un fichier) et exploration des données |
| absa.py | le pipeline DistilBERT et mise en forme des résultats |
| classique.py | le pipeline TF-IDF + modèle classique (LR, LR optimisé, SVM, XGBoost) |
| graphiques.py | Graphiques interactifs (Plotly) |
| ui.py | Couleurs, styles et blocs HTML pour les diapositives (cartes, tableaux, page de titre) |
| config.py | Chemins d'accès aux modèles et aux données |
| requirements.txt | Bibliothèques et leurs versions |
| exemple_avis_fictifs.csv | Petit fichier contenant des avis fictifs pour tester la démo « un seul fichier » |
| assets\ | Images d’arrière-plan pour la page de titre et les lignes de séparation |
| .streamlit\config.toml | Thème (couleurs du projet) et paramètres Streamlit |

garder la meme organisation des dossier, sinon modifier `config.py`


## 2. Configuration de l’environnement (à effectuer une seule fois)

l'opération s’effectue dans le terminal JupyterLab : menu « File » > « New » > « Terminal ». terminal PowerShell.

Étape 1. se placer dans dossier Python du projet (remplacez `D:\...` par le début du chemin d’accès réel ; comme il contient des espaces, veillez à conserver les guillemets) :

```powershell
cd “D:\...\Projet fil rouge-Trustpilot Aspect Intelligence\Part 2\Python”
```

Étape 2. Vérifiez la version de Python, puis créez un environnement virtuel `.venv` :

```powershell
python --version
python -m venv .venv
```

La version attendue est Python 3.12 (cad celle utilisée par JupyterLab Desktop). Si le dossier `.venv` existe déjà et fonctionne correctement, ignorer cette étape.

Étape 3. Activez l’environnement. À ce stade, `(.venv)` s’affiche en début de ligne :

```powershell
.\.venv\Scripts\Activate.ps1
```

Si PowerShell refuse d'exécuter ce script, il faut autoriser son exécution uniquement dans cette fenêtre ; ensuite relancer l'activation de l'environnement :

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```


Étape 4. Mettre pip à jour, puis installer toutes les bibliothèques de `requirements.txt` :

```powershell
python -m pip install --upgrade pip
python -m pip install -r app\requirements.txt
```

L'option `-r` demande à pip de lire le fichier et d'installer chaque ligne, dans la version indiquée. L'installation de torch est la plus longue (plusieurs centaines de Mo).

| Bibliothèque | Version | À quoi elle sert |
|---|---|---|
| streamlit | 1.66.0 | l'interface de l'application |
| plotly | 6.9.0 | les graphiques interactifs |
| pandas, numpy | 2.2 à 3 ; 1.26 à 3 | les tableaux et les calculs |
| openpyxl | 3.1 à 4 | la lecture des fichiers .xlsx d'annotation et l'export Excel |
| joblib | 1.3 à 2 | la relecture des fichiers .joblib |
| scikit-learn | 1.9.1 | TF-IDF, régression logistique, SVM linéaire, OneVsRestClassifier |
| xgboost | 3.3.0 | la relecture des deux modèles XGBoost (modele_xgb_*.joblib), issu des notebooks |
| torch | 2.2 à 3 | le calcul de DistilBERT (version processeur sous Windows) |
| transformers | 4.40 à 5 | le tokenizer et le modèle DistilBERT (from_pretrained) |
| safetensors | 0.4 et plus | la lecture des poids model.safetensors |

Étape 5. Vérifier l'installation (le terminal doit afficher OK) :

```powershell
python -c "import streamlit, plotly, sklearn, xgboost, torch, transformers; print('OK')"
```

Ce que chaque partie demande :

- les modèles classiques : `tfidf_aspects.joblib`, `liste_aspects.joblib`, `tfidf_sentiments.joblib` et les deux fichiers du modèle choisi, avec joblib et scikit-learn ;
- XGBoost : en plus, la bibliothèque xgboost et `mapping_sentiment.joblib` (le modèle prédit des codes 0 à 3, traduits en mixed, negative, neutral, positive) ;
- DistilBERT : torch, transformers et safetensors, et dans chaque dossier `distilbert_*_final` les fichiers `config.json`, `model.safetensors`, `tokenizer.json` et `tokenizer_config.json`.


## 3. Lancement (à chaque fois)

Dans le terminal PowerShell de JupyterLab :

```powershell
cd "D:\...\Projet fil rouge-Trustpilot Aspect Intelligence\Part 2\Python"
.\.venv\Scripts\Activate.ps1
cd app
python -m streamlit run app.py
```

Le navigateur s'ouvre sur http://localhost:8501. Le terminal doit rester ouvert pendant la présentation ; Ctrl + C l'arrête.

Le `cd app` compte : Streamlit lit son thème dans `.streamlit\config.toml` du dossier courant.


## 4. Les pages

Le sommaire de la barre latérale suit l'ordre des etapes. Chaque partie se parcourt par ses onglets, de gauche à droite.

| Page | Contenu |
|---|---|
| Accueil | la couverture |
| Introduction | démarche, le besoin, le pipeline en deux tâches, les cinq étapes |
| 01 · Exploration | les deux corpus, le plafond de 20 avis, le vocabulaire, et l'onglet « Explorer les corpus » |
| 02 · Corpus annoté | échantillon, ontologie, règles d'annotation, GOLD et SILVER, déséquilibre, et l'onglet « Explorer les données » |
| 03 · Modèles classiques | protocole, baselines, optimisation de C et de la LR, coefficients, sentiment |
| 04 · DistilBERT | architecture, entraînement, résultats par tâche, par aspect, GOLD et SILVER |
| Démo · un avis | un avis analysé par deux modèles au choix (DistilBERT ou un des quatre modèles classiques) |
| Démo · un fichier | annotation d'un fichier d'avis, synthèse par aspect et export |
| 05 · Analyse critique | usage métier, limites et suites |
| Conclusion | synthèse et chiffres clés |
| Annexes | scores des six configurations, scores des 18 aspects, sources |

pas de calcul a rafire, les chiffres des slides sont issus des calculs notebooks.

Les graphiques sont interactifs : survoler pour lire une valeur, clic-glisser pour zoomer, double-clic pour revenir, clic sur la légende pour masquer une série, icône appareil photo pour exporter en PNG.

Widgets sur les slides :

| Slide | Widget |
|---|---|
| Coefficients (03) | choix de l'aspect affiché : les trois, packaging, delivery ou trustworthiness |
| Sentiment (03) et Sentiment (04) | case « Afficher en % de la classe réelle » pour la matrice de confusion |
| Par aspect (04) | tri des 18 aspects : nombre d'avis, gain de DistilBERT ou F1 de DistilBERT |
| Six configurations (annexe) | tâche (aspects ou sentiment) et choix des modèles comparés |
| Explorer les corpus (01) | corpus, valeurs manquantes, statistiques, répartition des notes et d'une autre colonne |
| Explorer les données (02) | annotations GOLD et SILVER ou jeu de test, filtres par aspect, sentiment et origine, tableau croisé |



## 5. Démo · un avis

L'avis peut être saisi (dont celui des notebooks) ou tiré du jeu de test, avec son annotation.

Les listes « Modèle 1 » et « Modèle 2 » choisissent les deux modèles comparés, parmi cinq : DistilBERT et les quatre configurations classiques. On peut donc comparer DistilBERT à un modèle classique, ou deux modèles classiques entre eux (par exemple LR initiale et SVM linéaire). Le bouton Analyser est désactivé si le même modèle est choisi deux fois.

| Choix | Fichiers lus |
|---|---|
| DistilBERT | notebooks\distilbert_aspects_final et notebooks\distilbert_sentiment_final |
| LR optimisée | modele_lr_opt_aspects.joblib, modele_lr_opt_sentiments.joblib |
| LR initiale | modele_lr_aspects.joblib, modele_lr_sentiments.joblib |
| SVM linéaire | modele_svm_aspects.joblib, modele_svm_sentiments.joblib |
| XGBoost | modele_xgb_aspects.joblib, modele_xgb_sentiments.joblib, mapping_sentiment.joblib |

Les quatre modèles classiques utilisent aussi `tfidf_aspects.joblib`, `liste_aspects.joblib` et `tfidf_sentiments.joblib`. Leur enchaînement est celui de `analyser_avis_ml` dans le notebook de sentiment : TF-IDF des aspects puis modèle d'aspects ; pour chaque aspect détecté, TF-IDF de « aspect + texte » puis modèle de sentiment. Le pipeline DistilBERT suit le même enchaînement avec les deux modèles fine-tunés.

Les colonnes du tableau de chaque pipeline :

- Proba. aspect : la probabilité que l'aspect soit présent, selon le modèle d'aspects ; l'aspect est retenu à partir de 0,50 (réglable dans « Réglages »).
- Sentiment : la classe choisie par le modèle de sentiment pour cet aspect.
- Confiance : la probabilité que le modèle de sentiment donne à la classe choisie, c'est-à-dire la plus élevée des quatre (predict_proba pour les modèles classiques, softmax pour DistilBERT). Avec quatre classes, elle vaut au moins 0,25. Ce n'est pas une probabilité calibrée : 0,95 indique un modèle très sûr de lui, pas une réponse forcément juste. Le SVM ne fournit pas de probabilité : ses cases affichent « — » et sa décision vient de predict, comme dans le notebook.

Le tableau de comparaison place les deux modèles face à l'annotation : ✓ comme l'annotation, X aspect en trop, X non détecté, X sentiment faux.

Deux volets dépliables complètent le résultat :

- les probabilités des 18 aspects pour les deux modèles, avec le seuil en pointillés ;
- « Ce que TF-IDF + … a lu », un volet par modèle classique choisi : les fichiers chargés, les 10 termes de l'avis les plus pondérés par `tfidf_aspects`, et, pour chaque aspect détecté, les termes qui l'ont fait détecter (poids TF-IDF × coefficient du modèle). XGBoost n'a pas de coefficient par terme : ce détail n'existe que pour la LR et le SVM.


## 6. Démo · un fichier

Déposer un CSV ou un Excel, une ligne par avis, en anglais (pour essayer : `exemple_avis_fictifs.csv` ou `data\test_400.csv`, colonne `texte_complet`). Choisir le modèle (DistilBERT ou TF-IDF + LR optimisée), les colonnes du texte, du titre et du regroupement, puis lancer l'analyse.

Le résultat présente quatre onglets : sentiments par aspect, tableau des priorités (solde net, F1 de test du modèle choisi et fiabilité), lecture des avis, et export (classeur Excel de trois feuilles et CSV des couples).


## 7. En cas de problème

| Message ou situation | Solution |
|---|---|
| `python` n'est pas reconnu | utiliser le chemin complet du Python de JupyterLab : `& "C:\Users\<nom>\AppData\Roaming\jupyterlab-desktop\jlab_server\python.exe" -m venv .venv` |
| Activate.ps1 refusé (exécution de scripts désactivée) | `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`, puis relancer l'activation |
| ModuleNotFoundError | l'environnement n'est pas activé, ou l'installation n'est pas terminée : activer `.venv` puis relancer `python -m pip install -r app\requirements.txt` |
| Pipeline indisponible : Fichier absent | vérifier le nom du fichier dans `models` ou le chemin dans `config.py` |
| InconsistentVersionWarning de scikit-learn | les .joblib ont été écrits avec une autre version : dans le notebook, `import sklearn; print(sklearn.__version__)`, puis dans le terminal `python -m pip install scikit-learn==<cette version>` |
| Premier clic sur Analyser un peu long | chargement des deux modèles DistilBERT, une seule fois ; les clics suivants sont rapides |
| Port 8501 déjà utilisé | `python -m streamlit run app.py --server.port 8502` |
| La page ne se met pas à jour après une modification du code | touche R dans la page, ou rafraîchir le navigateur (F5) |
