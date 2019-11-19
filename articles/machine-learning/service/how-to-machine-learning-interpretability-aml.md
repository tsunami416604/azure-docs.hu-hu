---
title: A helyi és távoli futtatások modellezési értelmezése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állapíthatja meg, hogy a gépi tanulási modell hogyan határozza meg a funkció fontosságát, és előrejelzéseket készít az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: ffb9e0547c44ee47a43de00e51933ce7d0584759
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158726"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>A helyi és távoli futtatások modellezési értelmezése

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használja a Azure Machine Learning Python SDK értelmező csomagját, hogy megtudja, miért hozta létre a modell az előrejelzéseit. Az alábbiak végrehajtásának módját ismerheti meg:

* A helyileg és távoli számítási erőforrásokon egyaránt betanított gépi tanulási modellek értelmezése.
* Helyi és globális magyarázatok tárolása az Azure-beli futtatási előzményekben.
* Tekintse meg a [Azure Machine learning Studio](https://ml.azure.com)értelmezési vizualizációit.
* Pontozási-magyarázatot helyezhet üzembe a modellel.

További információ: [a modell értelmezése Azure Machine learning szolgáltatásban](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Helyi értelmezés

Az alábbi példa bemutatja, hogyan használhatja helyileg a értelmező csomagot az Azure-szolgáltatásokkal való kapcsolatfelvétel nélkül.

1. Ha szükséges, használja a `pip install azureml-interpret` az értelmező csomag beszerzéséhez.

1. Egy minta modell betanítása egy helyi Jupyter jegyzetfüzetbe.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. A magyarázatot helyileg hívja meg.
   * Egy magyarázó objektum inicializálásához adja át a modelljét és a betanítási adatait az elmagyarázó konstruktorának.
   * Annak érdekében, hogy a magyarázatokat és a vizualizációkat részletesebben is elvégezze, dönthet úgy, hogy a szolgáltatás nevét és a kimeneti osztály nevét adja meg, ha a besorolást végzi.

   A következő kódrészletek bemutatják, hogyan hozható létre egy magyarázó objektum a `TabularExplainer`, a `MimicExplainer`és a `PFIExplainer` helyileg.
   * `TabularExplainer` a három SHAP-magyarázat egyikét hívja meg (`TreeExplainer`, `DeepExplainer`vagy `KernelExplainer`).
   * `TabularExplainer` automatikusan kiválasztja a legmegfelelőbbet a használati esethez, de a három mögöttes magyarázatot közvetlenül is meghívhatja.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    vagy

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    vagy

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Általános, globális funkciók fontossági értékei

Tekintse át a következő példát, amely segítséget nyújt a globális funkciók fontossági értékeinek beszerzésében.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Példány-szint, a helyi funkció fontossági értékei

A helyi funkció fontossági értékeinek lekérése egy adott példány vagy példányok egy csoportjára vonatkozó magyarázatok meghívásával.
> [!NOTE]
> a `PFIExplainer` nem támogatja a helyi magyarázatokat.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Távoli futtatások értelmezése

Az alábbi példa bemutatja, hogyan használható a `ExplanationClient` osztály a modell-értelmezés távoli futtatásokhoz való engedélyezéséhez. Elméletileg a helyi folyamathoz hasonlít, a következők kivételével:

* Használja a távoli Futtatás `ExplanationClient` a értelmező környezet feltöltéséhez.
* A környezet későbbi letöltése helyi környezetben.

1. Ha szükséges, használja a `pip install azureml-contrib-interpret` a szükséges csomag beszerzéséhez.

1. Helyi Jupyter notebook létrehozása egy tanítási szkriptet. Például: `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Állítson be Azure Machine Learning számítást számítási célként, és küldje be a betanítási futtatást. Útmutatásért lásd: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md#amlcompute) . A [példaként használható jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) is hasznosak lehetnek.

1. Töltse le a magyarázatot a helyi Jupyter notebookon.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Nyers funkciók átalakítása

Megadhatja, hogy a mesterséges funkciók helyett a nyers, nem átalakított funkciókra vonatkozó magyarázatokat kapjon. Ennél a lehetőségnél a szolgáltatás átalakítási folyamatát átadja a `train_explain.py`ban található magyarázatnak. Ellenkező esetben a magyarázó elemek a megfogalmazott funkciókra vonatkozó magyarázatokat biztosítanak.

A támogatott átalakítások formátuma megegyezik a [sklearn-pandák](https://github.com/scikit-learn-contrib/sklearn-pandas)című témakörben leírtak szerint. Általánosságban elmondható, hogy az átalakítások csak akkor támogatottak, ha egyetlen oszlopon működnek, így egyértelmű, hogy egy-a-többhöz.

Olvassa el a nyers funkciók leírását `sklearn.compose.ColumnTransformer` vagy a beszerelt transzformátor-rekordok listáját használva. Az alábbi példa `sklearn.compose.ColumnTransformer`t használ.

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Ha szeretné futtatni a példát a beszerelt transzformátor-rekordok listájával, használja a következő kódot:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Vizualizációk

Miután letöltötte a magyarázatokat a helyi Jupyter notebookon, a vizualizáció irányítópultján megismerheti és értelmezheti a modelljét.

### <a name="global-visualizations"></a>Globális vizualizációk

A következő mintaterületek globális képet nyújtanak a betanított modellről, valamint annak előrejelzéseit és magyarázatait.

|Telek|Leírás|
|----|-----------|
|Adatelemzés| Az adatkészlet áttekintését jeleníti meg az előrejelzési értékekkel együtt.|
|Globális fontosság|A fő K (konfigurálható K) funkcióit jeleníti meg globálisan. Segít megérteni az alapul szolgáló modell globális viselkedését.|
|Magyarázat feltárása|Azt mutatja be, hogy a szolgáltatás hogyan befolyásolja a modell előrejelzési értékeinek változását, vagy az előrejelzési értékek valószínűségét. A funkciók interakciójának hatását mutatja.|
|Összefoglalás fontossága|A a helyi és a szolgáltatás fontossági értékeit használja az összes adatpontnál, hogy megjelenjenek az egyes szolgáltatások az előrejelzési értékre gyakorolt hatásának eloszlása.|

[![vizualizációs irányítópult globális](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Helyi vizualizációk

Az adatpontok helyi, szolgáltatásbeli fontossági területének betöltéséhez válassza ki az egyes adatpontokat a parcellán.

|Telek|Leírás|
|----|-----------|
|Helyi fontosság|Megjeleníti a legfontosabb szolgáltatásokat globálisan (konfigurálható K). Segít bemutatni az alapul szolgáló modell helyi viselkedését egy adott adatponton.|
|Perturbáció feltárása|Lehetővé teszi a kijelölt adatponthoz tartozó szolgáltatások értékének módosítását, és megfigyelheti az előrejelzés értékének változásait.|
|Egyéni feltételes várakozás (ICE)| Lehetővé teszi a szolgáltatás értékének a minimális értékről a maximális értékre való módosítását. Segít bemutatni, hogy az adatpont előrejelzése hogyan változik meg egy szolgáltatás módosításakor.|

[![vizualizációs irányítópult helyi funkciójának fontossága](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![vizualizációs irányítópult funkció perturbáció](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![vizualizációs irányítópult jég ábrázolása](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

> [!NOTE]
> A Jupyter-kernel elindítása előtt győződjön meg róla, hogy engedélyezi a vizualizációk irányítópultjának widget-bővítményeit.

* Jupyter-notebookok

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

A vizualizációs irányítópult betöltéséhez használja a következő kódot:

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Vizualizáció a Azure Machine Learning Studióban

A [távoli értelmezési](#interpretability-for-remote-runs) lépések elvégzése után megtekintheti a vizualizációs irányítópultot [Azure Machine learning Studióban](https://ml.azure.com). Ez az irányítópult a vizualizációk irányítópultjának a fent ismertetett egyszerűbb verziója. Csak két lapot támogat:

|Telek|Leírás|
|----|-----------|
|Globális fontosság|A fő K (konfigurálható K) funkcióit jeleníti meg globálisan. Segít megérteni az alapul szolgáló modell globális viselkedését.|
|Összefoglalás fontossága|A a helyi és a szolgáltatás fontossági értékeit használja az összes adatpontnál, hogy megjelenjenek az egyes szolgáltatások az előrejelzési értékre gyakorolt hatásának eloszlása.|

Ha a globális és a helyi magyarázat is elérhető, az adatok mindkét lapot kitöltik. Ha csak globális magyarázat áll rendelkezésre, az összefoglalás fontossága lap le van tiltva.

Kövesse az alábbi elérési utakat a vizualizációs irányítópult eléréséhez Azure Machine Learning Studióban:

* **Kísérletek** panel (előzetes verzió)
  1. Válassza a **kísérletek** lehetőséget a bal oldali ablaktáblán a Azure Machine learning szolgáltatáson futtatott kísérletek listájának megjelenítéséhez.
  1. Válasszon ki egy adott kísérletet a kísérlet összes futtatásának megtekintéséhez.
  1. Válasszon egy futtatást, majd a **magyarázatok lapot a** magyarázatok vizualizációs irányítópultján.

   [![vizualizációs irányítópult helyi funkciójának fontossága](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)

* **Modellek** panel
  1. Ha regisztrálta az eredeti modelljét a [modellek Azure Machine learning használatával történő üzembe helyezésének](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)lépéseivel, a bal oldali ablaktáblán kiválaszthatja a **modelleket** , és megtekintheti.
  1. Válasszon ki egy modellt, majd a **magyarázatok** lapon tekintse meg a magyarázó vizualizáció irányítópultját.

## <a name="interpretability-at-inference-time"></a>Tolmácsolás a következtetés időpontjában

A magyarázatot az eredeti modellel együtt üzembe helyezheti, és a helyi magyarázó információk megadásához használja a következtetést. A könnyebb súlyú pontozási magyarázatokat is kínáljuk, amelyekkel javítható az értelmező teljesítmény a következtetések idején. A könnyebb súlyozású pontozási elmagyarázó üzembe helyezésének folyamata hasonló a modellek üzembe helyezéséhez, és a következő lépéseket tartalmazza:

1. Hozzon létre egy magyarázat objektumot. Használhatja például a `TabularExplainer`:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Hozzon létre egy pontozási magyarázatot a magyarázat objektummal.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Egy pontozási bemutató modellt használó rendszerkép konfigurálása és regisztrálása.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Választható lépésként lekérheti a pontozási magyarázatot a felhőből, és tesztelheti a magyarázatokat.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Telepítse a lemezképet egy számítási célra a következő lépésekkel:

   1. Ha szükséges, regisztrálja az eredeti előrejelzési modellt a [modellek üzembe helyezése Azure Machine learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)használatával című témakörben ismertetett lépéseket követve.

   1. Hozzon létre egy pontozási fájlt.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Adja meg a telepítési konfigurációt.

         Ez a konfiguráció a modell követelményeitől függ. A következő példa egy olyan konfigurációt határoz meg, amely egy CPU-mag és egy GB memóriát használ.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Hozzon létre egy fájlt környezeti függőségekkel.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Hozzon létre egy egyéni Docker, amelynek a g + + telepítve van.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. A létrehozott rendszerkép üzembe helyezése.
   
         Ez a folyamat körülbelül öt percet vesz igénybe.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Tesztelje az üzemelő példányt.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Karbantartás.

   Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.

## <a name="next-steps"></a>Következő lépések

[További információ a modell értelmezéséről](how-to-machine-learning-interpretability.md)