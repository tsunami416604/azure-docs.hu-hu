---
title: Modell értelmezhetősége
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan annak magyarázata, hogy miért a modell lehetővé teszi az Azure Machine Learning SDK használatával készíthet előrejelzéseket. Használat során a tanuláshoz és következtetésekhez tudni, hogy a modell előrejelzéseket tesz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 05/30/2019
ms.openlocfilehash: 94309a019800b560cf6731d84cea324932e3f357
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398536"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Modell-e az Azure Machine Learning szolgáltatással

Ebből a cikkből megismerheti, hogyan annak magyarázata, hogy miért a modell készült-e az előrejelzés az Azure Machine Learning Python SDK különböző e csomagokkal zavartalanul.

Az osztályok és módszerek használata az SDK-ban, kaphat:
+ A szolgáltatás nyers és a visszafejtett funkciók fontossági értékek
+ E nagy mennyiségű, való életből vett adatkészletek során tanuláshoz és következtetésekhez.
+ Interaktív Vizualizációk, melyek a segítségére lehetnek a data- és magyarázatok minták felderítését a képzési időpontban

A fejlesztési ciklus képzési fázisban modell tervezők és értékelők segítségével a modell e kimeneti feltételezéseket ellenőrzése és az érintettekkel való megbízhatósági kapcsolat létrehozása.  Hibakeresési is felhasználják az elemzéseket a modellbe, eszközmodell viselkedésének érvényesítése megegyezik a célokat, és az eltérés kereséséhez.

A machine learninghez **funkciók** is képes előre jelezni egy cél adatpont adatmezőket. Például a hitelkockázatot, kor, a fiók és a fiók életkor datová Pole használhatók. Ebben az esetben életkor, a fiók és a fiók élettartama a **funkciók**. A szolgáltatás fontosság kiderül, hogy hogyan minden adatmező érintett-e a modell-előrejelzéseket. Például kora erősen használhatók az előrejelzéshez fiók és a korszűrő nem befolyásolják az előrejelzési pontosság jelentősen közben. Ez a folyamat lehetővé teszi, hogy az adatszakértők, az eredményül kapott előrejelzéseket ismertetik, hogy az érdekelt felek, hogy mely adatpontok közel a modell legfontosabb.

Ezek az eszközök akkor is azt ismerteti, machine learning-modellek **globálisan az összes adat**, vagy **helyileg, egy adott adatpontot** a legmodernebb technológiák használatával egy könnyen használható és méretezhető módon.

Az e osztályok több SDK-csomagok érhetők el. Ismerje meg, hogyan [SDK-csomagok telepítése az Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), a fő csomag, amely tartalmazza a Microsoft által támogatott funkciók.

* `azureml.contrib.explain.model`, előzetes verzió, és kísérleti funkciók kipróbálható.

* `azureml.train.automl.automlexplainer` csomag automatikus gépi tanulási modelleket értelmezéséhez.

> [!IMPORTANT]
> A tartalom a `contrib` névtér nem teljes mértékben támogatott. A kísérleti funkciók érett lenni, mivel azokat a fő névtér fokozatosan kerül.

## <a name="how-to-interpret-your-model"></a>A modell értelmezése

Az e osztályok és módszerek megértése a modell globális viselkedés vagy adott előrejelzéseket alkalmazhat. Az előbbi neve globális magyarázat, és ez utóbbi nevezzük helyi magyarázata.

A módszerek is csoportosíthatók az módszer e modell független vagy adott modell alapján. Egyes metódusok a céloznia modellek bizonyos típusú. Ha például Alakzatadatok a fa ismertető csak érvényes fa-alapú modell. Egyes metódusok kezeli a modell egy fekete mezőt, például mimic ismertető vagy Alakzatadatok a kernel ismertető. A `explain` csomagot használja ezen különböző megközelítések adatkészletek adatmodell-típusokat, és a használati esetek alapján.

A kimenet a hogyan egy adott modell lehetővé teszi annak előrejelzése, mint például az adatok:
* Globális vagy helyi relatív funkció fontosság

* Globális vagy helyi szolgáltatás és az előrejelzési kapcsolat

### <a name="explainers"></a>Explainers

Nincsenek explainers két készletnyi: Közvetlen Explainers és Meta Explainers az SDK-ban.

__A közvetlen explainers__ integrált kódtárak származnak. Az SDK összes explainers burkolja, így egy közös API-t és a kimeneti formátum szolgáltatnak. Ha járatos több közvetlenül ezek explainers, közvetlenül hívhatók meg azokat a közös API-t és a kimeneti formátum használata helyett. Az SDK-ban elérhető közvetlen explainers listáját a következők:

* **Fatartomány ismertető**: Az Alakzatadatok a fa ismertető, amelynek fő témája a polinom time gyors Alakzatadatok érték költségbecslési algoritmus adott fák és együttesek fák.
* **Részletes ismertető**: Alapján a magyarázat az Alakzatadatok, részletes ismertető "egy nagy sebességű approximációs algoritmus, deep learning-modellek az Alakzatadatok értékek, amelyek az Alakzatadatok NIPS könyvben leírt DeepLIFT épül, amely kapcsolatot is. TensorFlow-modellek és a TensorFlow-háttérrendszer használatával Keras-modellek támogatottak (nincs PyTorch előzetes támogatása) ".
* **Kernel Explainer**: Alakzatadatok a Kernel ismertető kifejezetten súlyozott helyi lineáris regresszió segítségével bármely modellhez tartozó Alakzatadatok értékeket használja.
* **Ismertető utánzására**: Mimic ismertető az elgondolásra épül, globális helyettes modellek alapul. Egy globális helyettes modell egy belsőleg interpretable modell, amely a lehető legpontosabban méretkorlátjai az előrejelzés egy fekete mezőt modell tanítása. Adattudós tudja értelmezni a helyettes modell következtetéseket a fekete dobozhoz modellel kapcsolatos.
* **SÁRGÁSZÖLD ismertető** (`contrib`): SÁRGÁSZÖLD alapozva SÁRGÁSZÖLD ismertető a állapota-a-a legújabb helyi interpretable modell-agnosztikus magyarázatokat (SÁRGÁSZÖLD) algoritmust használja helyi helyettes modellek létrehozásának eljárásait. SÁRGÁSZÖLD ellentétben a globális helyettes modellek összpontosít az egyes előrejelzéseket elmagyarázni helyi helyettes modellek betanítása.
* **HAN szöveg ismertető** (`contrib`): HAN szöveg ismertető szöveges adatok egy adott fekete dobozhoz szöveg modell modell magyarázatok lekérése egy hierarchikus figyelmet hálózatot használ. Az egy adott oktatói modell előre jelzett kimenetek HAN helyettes modell betanításához azt. Globálisan a szöveg forrásgyűjteményébe között képzést, miután egy fine-tune lépést, az adott dokumentum hozzáadtuk a magyarázatokat pontosságának javítása érdekében. HAN egy kétirányú RNN két figyelmet rétegeket, használja a mondat és a word figyelmet. A DNN tanított az oktatói modell, és a egy adott dokumentum finomította, hogy bontsa ki a word importances a figyelmet rétegek. HAN pontosabb, mint SÁRGÁSZÖLD vagy Alakzatadatok szöveges adatok esetében, de költségesebb, a használati idő, valamint képzési is található. Azonban hajtottunk végre a betanítási idő fejlesztései, így a felhasználó kesztyűtartó szóbeágyazásokból, a hálózati inicializálása arra, bár ez továbbra is lassú. A betanítási idő jelentősen javítható, ha egy távoli Azure-GPU virtuális gépen futó HAN. HAN végrehajtásának leírt "Hierarchikus figyelmet hálózatok dokumentum besorolási (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ automatikusan válassza ki a megfelelő közvetlen ismertető és az ajánlott magyarázata adatok alapján az adott modell és az adatkészletek létrehozása. A metaadatok explainers összes könyvtár (Alakzatadatok, SÁRGÁSZÖLD, előnézet, stb.) a Microsoft integrált vagy fejlesztett használhatja. Az SDK-ban elérhető meta explainers a következők:

* **Táblázatos ismertető**: A táblázatos adatkészleteket használja.
* **Szöveg ismertető**: A szöveg adatkészleteket használja.

Emellett a meta lehetőségre kattint, a közvetlen explainers, meta explainers felett az alapul szolgáló kódtárak további funkciók fejlesztése és növelheti a sebességet és méretezhetőséget biztosít a közvetlen explainers keresztül.

Jelenleg `TabularExplainer` alkalmaz az alábbi logika, a közvetlen Explainers meghívásához:

1. Ha egy fa-alapú modell, a alkalmazni `TreeExplainer`, más
2. Ha egy DNN-modell, a alkalmazni `DeepExplainer`, más
3. Fekete beépített modellként kezelje azt, és a alkalmazni `KernelExplainer`

A beépített intelligencia `TabularExplainer` kifinomultabb fog válni, további kódtárak pedig az SDK integrálva vannak, és hogy ismertetése és az egyes ismertető hátrányai.

`TabularExplainer` a közvetlen Explainers keresztül is rendelkezik végzett jelentős funkciót és a teljesítmény fejlesztései:

* **Az inicializálás adatkészlet összefoglaló**. Olyan esetekben, ahol magyarázata sebességétől legfontosabb hogy összesíteni az inicializálási adatkészlet és létrehozása egy kis készletét reprezentatív mintát, amely felgyorsítja a globális és a helyi magyarázata.
* **Az értékelés adatkészlet mintavételi**. Ha a felhasználó átadja az értékelés minták nagy készletét, de nem ténylegesen szükséges összes ki kell értékelni őket, a mintavételi paraméterrel állítható az igaz értékre a globális magyarázat felgyorsítása érdekében.

Az alábbi ábrán látható a két készletnyi közvetlen és meta explainers közötti kapcsolatot.

[![Machine Learning-e architektúra](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Támogatott modellek

Nagy méretű adatkészletek a Pythonban képzett modellekre `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, vagy `scipy.sparse.csr_matrix` formátumot az e által támogatott `explain` SDK csomagot.

A magyarázat functions fogadja el a modellek és a folyamatok bemenetként. A modell áll rendelkezésre, ha a modell meg kell valósítania az előrejelző függvényben `predict` vagy `predict_proba` , amely megfelel a Scikit egyezmény. Egy folyamat (a csővezeték-parancsfájl nevét) áll rendelkezésre, ha a magyarázat függvény azt feltételezi, hogy a futó folyamat parancsfájl előrejelzést.

### <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási célnak

A `explain` csomag az célja, hogy mindkét helyi és távoli számítási célnak dolgozhat. Ha helyileg futtatja, az SDK-függvények nem kapcsolatba fog lépni bármely Azure-szolgáltatást. MAGYARÁZAT távolról futtatni az Azure Machine Learning COMPUTE számítási, és a magyarázatot info jelentkezzen be az Azure Machine Learning előzmények szolgáltatások futtatása. Ezeket az adatokat a rendszer naplózza, ha jelentéseket és vizualizációkat a magyarázat azonnal elérhetők az Azure Machine Learning-munkaterület-portál felhasználói elemzés céljából.

## <a name="interpretability-in-training"></a>E képzési

### <a name="train-and-explain-locally"></a>Betanítása és helyileg magyarázata

1. A helyi Jupyter notebook modellje betanításához.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Hívja meg a ismertető: Egy ismertető objektum inicializálása, szüksége a modell és a egy betanítási adatok átadása az ismertető a konstruktor. Akkor is lehetősége van átadni a szolgáltatások neveit és a kimeneti osztály neve (Ha ez a besorolás), hogy a magyarázatokat és a Vizualizációk konkrétabb használandó. Így egy ismertető objektum használatával példányosítható [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) és [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) helyileg. `TabularExplainer` hívja a három explainers alatti egyik (`TreeExplainer`, `DeepExplainer`, vagy `KernelExplainer`), és automatikusan a legmegfelelőbbhöz az használati esetekhez jelöl. Ugyanakkor a három alapul szolgáló explainers mindegyike közvetlenül hívja.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

    vagy

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. A globális szolgáltatás fontossági értékek beolvasása.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. A szolgáltatás helyi fontossági értékek: használja a következő függvényhívások annak magyarázata, egy egyéni példány vagy példányok csoportjára is.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    vagy

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Betanítása és távolról magyarázata

Az Azure Machine Learning szolgáltatás támogatja a különböző számítási célnak betaníthatja, miközben ebben a szakaszban a példa bemutatja, hogyan teheti ezt a az Azure Machine Learning Compute cél.

1. Helyi Jupyter notebook (például run_explainer.py) hozzon létre egy tanítási szkriptet.

    ```python
    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # Train your model here

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Kövesse az [állítsa be a modell betanítása és számítási célnak](how-to-set-up-training-targets.md#amlcompute) hogyan állíthatja be az Azure Machine Learning COMPUTE számítási, a számítási célnak, és küldje el a betanítási Futtatás.

3. Töltse le a helyi Jupyter notebook a magyarázatot.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Vizualizációk

A Vizualizáció Irányítópult segítségével megismerheti és értelmezni a modell:

### <a name="global-visualizations"></a>Globális Vizualizációk

A következő grafikon adja meg a globális nézet készítése a betanított modell annak előrejelzéseket és magyarázatok együtt.

|Diagram|Leírás|
|----|-----------|
|Az adatok feltárása| Az adatkészlet előrejelzési értékek együtt áttekintése.|
|Globális fontosság|A felső K (konfigurálható K) fontos funkciók globálisan mutatja. Ez a diagram hasznos annak megértéséhez, az alapul szolgáló modell globális viselkedését.|
|Explanation Exploration|Bemutatja, hogyan szolgáltatás felelős a módosítást a modell előrejelzéses értékek (vagy előrejelzési értékek valószínűségét). |
|Összefoglalás| Egy aláírt helyi szolgáltatás fontossági értékeket használja minden adatpont között a hatását, mindegyik szolgáltatás esetében az előrejelzési értéke eloszlása megjelenítéséhez.|

[![Visualization Dashboard Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Helyi Vizualizációk

A helyi szolgáltatás fontosság diagram a megadott adatponthoz tartozó betölteni az előző grafikon tetszőleges időpontban minden egyes adatpontra kattinthat.

|Diagram|Leírás|
|----|-----------|
|Helyi fontosság|A felső K (konfigurálható K) fontos funkciók globálisan mutatja. Ez a diagram hasznos az alapul szolgáló modell egy adott adatpontra helyi viselkedésének megértése.|

[![Vizualizáció irányítópult helyi](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

A Vizualizáció az irányítópult betöltése, a következő kód használatával:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Nyers funkció átalakítások

Szükség esetén a ismertető magyarázatokat a nyers szolgáltatások tekintetében fogadásához az átalakítást (helyett visszafejtett funkciók) előtt a szolgáltatás átalakítási folyamat adhat át. Ha kihagyja ezt a beállítást, a ismertető biztosít magyarázatok visszafejtett szolgáltatások tekintetében.

A támogatott átalakításokat formátuma azonos az egyik leírtak szerint [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Általánosságban véve a átalakításokat mindaddig, amíg azok a művelethez használandó egyoszlopos, ezért egyértelműen egy a többhöz támogatottak.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>A következtetésekhez érinti

Az ismertető és az eredeti modellt telepíthetők, és kiértékelés időpontja, adja meg a helyi magyarázata adatokat használható. Pontozó ismertető üzembe helyezésének folyamata hasonló üzembe helyezéséhez, és a következő lépésekből áll:

1. MAGYARÁZAT objektum létrehozása:

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ```

1. Hozzon létre egy pontozó ismertető a magyarázat objektummal:

   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ```

1. Konfigurálja, és regisztrálja a pontozási ismertető modellt használó kép.

   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ```

1. [Opcionális] A pontozó ismertető lekérni a felhőből, és a magyarázatokat tesztelése

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. A-rendszerkép üzembe helyezése számítási célt:

   1. Hozzon létre egy pontozó fájlt (ebben a lépésben előtt kövesse a [helyezhet üzembe modelleket az Azure Machine Learning szolgáltatással](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) regisztrálni az eredeti előrejelzési modell)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Adja meg a telepítési konfigurációt (Ez a konfiguráció attól függ, a modell követelményeinek. Az alábbi példa meghatározza egy processzormaggal és 1 GB memóriát használó konfiguráció)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "breastcancer",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for breast cancer data')
        ```

   1. Hozzon létre egy fájlt a környezet függőségek

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"],
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Hozzon létre egy egyéni docker-fájlban g ++ telepítve

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. A létrehozott rendszerkép üzembe helyezése (becsült időtartam: 5 perc)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. Az üzemelő példány tesztelése

    ```python
    import requests

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Karbantartása: Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.

## <a name="interpretability-in-automated-ml"></a>E automatizált ML-ben

Automatizált machine learning szolgáltatás fontosság automatikus betanított modellek értelmezéséhez csomagot tartalmaz. Ezenkívül besorolási forgatókönyveket teszi fontosság osztályszintű funkció lekéréséhez. Ehhez a viselkedéshez belüli automatikus machine learning két módszer áll rendelkezésre:

* Ensemble betanított modell fontosak a szolgáltatás engedélyezéséhez használja a [ `explain_model()` ](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) függvény.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Ahhoz, hogy minden egyes feladatfuttatáshoz képzési előtt a szolgáltatás fontosság, állítsa be a `model_explainability` paramétert `True` a a `AutoMLConfig` objektum érvényesítési adatok biztosítása mellett. Ezután a [ `retrieve_model_explanation()` ](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) függvény.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

További információkért lásd: a [útmutató](how-to-configure-auto-train.md#explain-the-model-interpretability) az automatizált machine learning e funkciók engedélyezésével.

## <a name="next-steps"></a>További lépések

A Jupyter notebooks, amelyek bemutatják a fenti utasítások gyűjteménye, olvassa el a [Azure Machine Learning-e mintafüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
