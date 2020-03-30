---
title: Modell értelmezhetősége helyi és távoli futtatásokhoz
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan kaphat magyarázatot arról, hogy a gépi tanulási modell hogyan határozza meg a szolgáltatás fontosságát, és előrejelzéseket készít az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a479982eeac325c9774e3858ec51643e8ba699c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064036"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Modell értelmezhetősége helyi és távoli futtatásokhoz

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogy az Azure Machine Learning Python SDK értelmezhetőségi csomagját használja annak megértéséhez, hogy a modell miért készítette el az előrejelzéseket. Az alábbiak végrehajtásának módját ismerheti meg:

* Értelmezze a helyileg és a távoli számítási erőforrásokon betanított gépi tanulási modelleket.
* Helyi és globális magyarázatokat tárolaz Azure Run History webhelyen.
* Az [Azure Machine Learning stúdióban](https://ml.azure.com)megtekintheti az értelmezhetőségi vizualizációkat.
* Telepítsen egy pontozási magyarázó a modell.

További információ: [Model interpretability in Azure Machine Learning.](how-to-machine-learning-interpretability.md)

## <a name="local-interpretability"></a>Helyi értelmezhetőség

A következő példa bemutatja, hogyan használhatja az értelmezhetőségi csomagot helyileg az Azure-szolgáltatásokkal való kapcsolatfelvétel nélkül.

1. Szükség esetén `pip install azureml-interpret` használja az értelmezhetőségi csomag bekapását.

1. Mintamodell betanítása helyi Jupyter-jegyzetfüzetbe.

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

1. Hívd a magyarázót helyben.
   * A magyarázó objektum inicializálásához adja át a modellt és néhány betanítási adatot a magyarázó konstruktorának.
   * Annak érdekében, hogy a magyarázatok és a vizualizációk informatívabbak legyenek, a besorolás során megadhatja a jellemzőneveket és a kimeneti osztályneveket.

   A következő kódblokkok bemutatják, hogyan lehet egy `TabularExplainer` `MimicExplainer`magyarázó `PFIExplainer` objektumot a , és helyileg példányosítani.
   * `TabularExplainer`felhívja az alatta lévő három`TreeExplainer`SHAP `DeepExplainer`magyarázó ( , , vagy `KernelExplainer`) egyikét.
   * `TabularExplainer`automatikusan kiválasztja a használati esetnek leginkább megfelelőt, de a három mögöttes magyarázót közvetlenül hívhatja.

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

### <a name="overall-global-feature-importance-values"></a>Összességében a globális jellemzőfontossági értékek

A következő példában segíthet a globális jellemzőfontossági értékek lefelvételéhez.

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

### <a name="instance-level-local-feature-importance-values"></a>Példányszintű, helyi jellemzőfontossági értékek

A helyi szolgáltatás fontossági értékeit egy adott példány vagy példánycsoport magyarázatainak hívásával szerezheti be.
> [!NOTE]
> `PFIExplainer`nem támogatja a helyi magyarázatokat.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>A távoli futtatások értelmezhetősége

A következő példa bemutatja, `ExplanationClient` hogyan használhatja az osztályt a modell értelmezhetőségének engedélyezésére a távoli futtatásokhoz. Fogalmilag hasonló a helyi folyamathoz, kivéve:

* Használja `ExplanationClient` a távoli futtatásban az értelmezhetőségi környezet feltöltéséhez.
* Töltse le a környezetet később egy helyi környezetben.

1. Ha szükséges, `pip install azureml-contrib-interpret` használja, hogy a szükséges csomagot.

1. Hozzon létre egy betanítási parancsfájlt egy helyi Jupyter-jegyzetfüzetben. Például: `train_explain.py`.

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

1. Állítson be egy Azure Machine Learning-számítást számítási célként, és küldje el a betanítási futtatást. Az utasításokért tekintse meg [a számítási célok beállítása a modellbetanításhoz.](how-to-set-up-training-targets.md#amlcompute) Előfordulhat, hogy a [példajegyzetfüzetek is hasznosnak találhatják.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)

1. Töltse le a magyarázatot a helyi Jupyter notebook.

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

Választhat, hogy a nyers, átnemformított funkciók, nem pedig a mesterséges funkciók tekintetében kapjon magyarázatot. Ehhez a beállításhoz adja át a szolgáltatásátalakítási `train_explain.py`folyamatát a magyarázónak a alkalmazásban. Ellenkező esetben a magyarázó magyarázatot ad a mesterséges funkciók tekintetében.

A támogatott transzformációk formátuma megegyezik a [sklearn-pandákban leírtakkal.](https://github.com/scikit-learn-contrib/sklearn-pandas) Általában minden átalakítások támogatottak, amíg azok egyetlen oszlopban működnek, így egyértelmű, hogy egy-a-többhöz.

Magyarázatot kaphat a nyers `sklearn.compose.ColumnTransformer` jellemzőkre a testhez álló transzformátor-tuples segítségével vagy listájával. A következő `sklearn.compose.ColumnTransformer`példa a .

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

Abban az esetben, ha a példát a felszerelt transzformátor-tuples listájával szeretné futtatni, használja a következő kódot:

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

Miután letöltötte a magyarázatokat a helyi Jupyter-jegyzetfüzetben, a vizualizációs irányítópult segítségével megértheti és értelmezheti a modellt.

### <a name="global-visualizations"></a>Globális vizualizációk

A következő telkek a betanított modell globális nézetét, valamint az előrejelzéseket és magyarázatokat biztosítják.

|Telek|Leírás|
|----|-----------|
|Adatok feltárása| Az adatkészlet áttekintését jeleníti meg az előrejelzési értékekkel együtt.|
|Globális fontosság|A legjobb K (konfigurálható K) fontos funkciók megjelenítése világszerte. Segít megérteni az alapul szolgáló modell globális viselkedését.|
|Magyarázat feltárása|Bemutatja, hogy egy funkció hogyan befolyásolja a modell előrejelzési értékeinek vagy az előrejelzési értékek valószínűségét. A funkcióinterakció hatását mutatja.|
|Összefoglaló fontosság|Helyi, jellemzőfontossági értékeket használ az összes adatpontban az egyes funkciók előrejelzési értékre gyakorolt hatásának megjelenítéséhez.|

[![Visualization Dashboard globális](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Helyi vizualizációk

A helyi, jellemzőfontossági rajzot bármely adatponthoz betöltheti, ha kiválasztja a nyomtatás egyes adatpontját.

|Telek|Leírás|
|----|-----------|
|Helyi fontosság|A legjobb K (konfigurálható K) fontos funkciók at jeleníti meg világszerte. Segít szemléltetni az alapul szolgáló modell helyi viselkedését egy adott adatponton.|
|Perturbation feltárása|Lehetővé teszi a kijelölt adatpont értékeinek szolgáltatását, és megfigyelheti az előrejelzési érték ebből eredő módosításait.|
|Egyéni feltételes elvárás (ICE)| Lehetővé teszi, hogy a jellemzőérték a minimális értékről a maximális értékre változik. Segít szemléltetni, hogyan változik az adatpont előrejelzése egy szolgáltatás változásakor.|

[![Vizualizációs irányítópult helyi szolgáltatásának fontossága](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Vizualizációs irányítópult-szolgáltatás perturbációja](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Vizualizációs irányítópult ICE-mintarajzai](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> A Jupyter kernel indítása előtt engedélyezze a widgetbővítményeket a vizualizációs irányítópulton.

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

A vizualizációs irányítópult betöltéséhez használja a következő kódot.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Képi megjelenítés az Azure Machine Learning stúdióban

Ha elvégzi a [távoli értelmezhetőségi](#interpretability-for-remote-runs) lépéseket, megtekintheti a vizualizációs irányítópultot az [Azure Machine Learning stúdióban.](https://ml.azure.com) Ez az irányítópult a vizualizációs irányítópult fent ismertetett egyszerűbb változata. Ez egyetlen támogat kettő tabulátor:

|Telek|Leírás|
|----|-----------|
|Globális fontosság|A legjobb K (konfigurálható K) fontos funkciók megjelenítése világszerte. Segít megérteni az alapul szolgáló modell globális viselkedését.|
|Összefoglaló fontosság|Helyi, jellemzőfontossági értékeket használ az összes adatpontban az egyes funkciók előrejelzési értékre gyakorolt hatásának megjelenítéséhez.|

Ha globális és helyi magyarázatok is rendelkezésre állnak, az adatok mindkét lapot feltöltik. Ha csak egy globális magyarázat érhető el, az Összefoglaló fontosság lap le van tiltva.

Az alábbi elérési utak egyikének követésével érheti el a vizualizációs irányítópultot az Azure Machine Learning stúdióban:

* **Kísérletek** ablaktábla (előnézet)
  1. Válassza **a bal** oldali ablaktáblában a kísérletek listáját az Azure Machine Learningen futtatott kísérletek listájának megtekintéséhez.
  1. Válasszon ki egy adott kísérletet a kísérlet összes futásának megtekintéséhez.
  1. Jelöljön ki egy **futtatást,** majd a Magyarázatok lapot a kimagyarázó vizualizációs irányítópultra.

   [![Vizualizációs irányítópult helyi szolgáltatásának fontossága](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Modellek** ablaktábla
  1. Ha az [azure Machine Learning használatával](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)a modellek üzembe helyezése című lépéseit követve regisztrálta az eredeti modellt, a bal oldali ablaktáblában **kiválaszthatja** a Modellek lehetőséget a megtekintéséhez.
  1. Jelöljön ki egy **modellt,** majd a Magyarázatok lapon tekintse meg a magyarázatvizualizációs irányítópultot.

## <a name="interpretability-at-inference-time"></a>Értelmezhetőség a következtetési időpontban

A magyarázó üzembe helyezhető az eredeti modellel együtt, és a következtetési időpontban használhatja a helyi magyarázat-információk megadásához. Kínálunk könnyebb súlyú pontozási magyarázók, hogy javítsa értelmezhetőségi teljesítmény következtetés idáig időben. A könnyebb súlyú pontozási magyarázó üzembe helyezésének folyamata hasonló a modell üzembe helyezéséhez, és a következő lépéseket tartalmazza:

1. Magyarázatobjektum létrehozása. Használhatja például `TabularExplainer`a következőket:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Hozzon létre egy pontozási magyarázó a magyarázat objektumot.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfiguráljon és regisztráljon egy lemezképet, amely a pontozási magyarázó modellt használja.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Opcionális lépésként lekérheti a pontozási magyarázó a felhőből, és tesztelje a magyarázatokat.

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

1. Telepítse a lemezképet egy számítási célra az alábbi lépésekkel:

   1. Szükség esetén regisztrálja az eredeti előrejelzési modellt a modellek üzembe helyezése az Azure Machine Learning szolgáltatással című lépések [végrehajtásával.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

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
   1. Az üzembe helyezési konfiguráció meghatározása.

         Ez a konfiguráció a modell követelményeitől függ. A következő példa egy olyan konfigurációt határoz meg, amely egy PROCESSZORmagot és egy GB memóriát használ.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Hozzon létre egy környezeti függőségekkel rendelkező fájlt.

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

   1. Hozzon létre egy egyéni dockerfile g++ telepítve.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Telepítse a létrehozott lemezképet.
   
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

1. Tesztelje az üzembe helyezést.

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

1. Takarítson fel.

   Telepített webszolgáltatás törléséhez használja `service.delete()`a használatát.

## <a name="next-steps"></a>További lépések

[További információ a modell értelmezhetőségéről](how-to-machine-learning-interpretability.md)