---
title: A modell értelmezése az automatizált gépi tanulásban
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állapíthatja meg, hogy az automatikus ML-modell hogyan határozza meg a funkció fontosságát, és előrejelzéseket készít az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a0d805d6ae9b129443a2850e0741d5da87feac84
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539605"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>A modell értelmezése az automatizált gépi tanulásban

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan engedélyezheti az automatikus gépi tanulás (ML) értelmező funkcióit Azure Machine Learning. Az automatizált ML segítségével megismerheti a nyers és a megtervezett funkciók fontosságát is. A modell-értelmező használatához a `AutoMLConfig` objektumban állítsa be a `model_explainability=True`.  

Ebben a cikkben az alábbiakkal ismerkedhet meg:

- A legjobb modell vagy bármely modell tanítása során végezzen tolmácsolást.
- A vizualizációk lehetővé teszik az adatmintázatok és a magyarázatok megjelenítését.
- Értelmezhető megvalósítás a következtetés vagy pontozás során.

## <a name="prerequisites"></a>Előfeltételek

- Értelmező funkciók. Futtassa `pip install azureml-interpret azureml-contrib-interpret` a szükséges csomagok beszerzéséhez.
- Az automatizált ML-kísérletek létrehozásának ismerete. További információ az Azure Machine Learning SDK használatáról: a [regressziós modell oktatóanyagának](tutorial-auto-train-models.md) elvégzése, vagy az [automatikus ml-kísérletek konfigurálása](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>A legjobb modell képzésének értelmezése

Kérje le a `best_run`magyarázatát, amely tartalmazza a megtervezett funkciók és a nyers funkciók magyarázatait.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Az összetevő-tárolóban megjelenő Kiemelt funkciók fontosságának letöltése

A `ExplanationClient` használatával letöltheti a mesterséges funkciók magyarázatait a `best_run`összetevő-tárolójából. A `raw=True`által beállított nyers szolgáltatások magyarázatának megismeréséhez.

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Értelmezés a modell betanítása során 

Ha kiszámítja a modellre vonatkozó magyarázatokat, és megjeleníti őket, nem korlátozódik az automatizált ML-modellre vonatkozó, meglévő modellre. A modellre vonatkozó magyarázatot különböző tesztelési adataival is megtalálhatja. Az ebben a szakaszban ismertetett lépések bemutatják, hogyan lehet kiszámítani és megjeleníteni a kitalált funkciók fontosságát és a nyers funkció fontosságát a tesztelési adatok alapján.

### <a name="retrieve-any-other-automl-model-from-training"></a>Bármely más AutoML-modell beolvasása a képzésből

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>A modell magyarázatának beállítása

Az `automl_setup_model_explanations` használatával megtekintheti a mérnöki és a nyers funkciók magyarázatait. A `fitted_model` a következő elemeket tudja előállítani:

- Kiemelt adatok a betanított vagy tesztelési mintákból
- Megtervezett és nyers szolgáltatások nevének listája
- Megkereshető osztályok a címkével ellátott oszlopban besorolási helyzetekben

A `automl_explainer_setup_obj` a fenti listában szereplő összes struktúrát tartalmazza.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>A funkciók fontosságának inicializálása az utánozó-magyarázattal

A AutoML-modellek magyarázatának létrehozásához használja a `MimicWrapper` osztályt. A MimicWrapper a következő paraméterekkel inicializálhatja:

- A magyarázó telepítő objektum
- Munkaterülete
- LightGBM-modell, amely helyettesítőként működik a `fitted_model` automatizált ML-modellben

A MimicWrapper azt a `automl_run` objektumot is felveszi, ahol a nyers és a megtervezett magyarázatok fel lesznek töltve.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>A MimicExplainer használata a mérnöki funkciók fontosságának meghatározásához és megjelenítéséhez

A MimicWrapper `explain()` metódusát meghívhatja az átalakított tesztelési mintákkal, hogy a funkció fontosságot kapjon a generált mérnöki funkciók számára. Az `ExplanationDashboard` segítségével is megtekintheti a létrehozott mérnöki funkciók kiemelt fontosságú funkcióinak irányítópult-vizualizációját az automatikus ML featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Az utánozó elmagyarázó használata a nyers funkciók fontosságának meghatározásához és megjelenítéséhez

Az átalakított tesztelési mintákkal újra meghívhatja a `explain()` metódust a MimicWrapper-ben, és beállíthatja `get_raw=True` a funkció fontosságának megállapításához a nyers funkciókhoz. A `ExplanationDashboard` használatával is megtekintheti a nyers szolgáltatások funkció fontossági értékeinek irányítópult-vizualizációját.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Értelmezhető a következtetés során

Ebből a szakaszból megtudhatja, hogyan működővé tenni egy olyan automatizált ML-modellt, amely az előző szakaszban ismertetett magyarázatok kiszámításához használatos.

### <a name="register-the-model-and-the-scoring-explainer"></a>A modell és a pontozási magyarázat regisztrálása

A `TreeScoringExplainer` segítségével hozza létre a pontozási magyarázatot, amely kiszámítja a nyers és a Kiemelt funkció fontossági értékeit a következtetések időpontjában. A pontozási magyarázatot a korábban kiszámított `feature_map` inicializálhatja. A pontozási magyarázat a `feature_map` használja a nyers funkció fontosságának visszaküldéséhez.

Mentse a pontozási magyarázatot, majd regisztrálja a modellt és a pontozási magyarázatot a modellkezelés szolgáltatással. Futtassa a következő kódot:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>A szolgáltatás beállításához szükséges Conda-függőségek létrehozása

Ezután hozza létre a szükséges környezeti függőségeket a tárolóban az üzembe helyezett modellhez. Vegye figyelembe, hogy a (z) > = 1.0.45 verzióval rendelkező alapértelmezett azureml pip-függőségként kell szerepelnie, mert tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

Telepítse a szolgáltatást a Conda fájl és az előző lépésekből származó pontozási fájl használatával.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Következtetés tesztelési adattal

Bizonyos tesztelési adatok alapján megtekintheti az automatikus ML-modellből származó előre jelzett értéket. Megtekintheti az előrejelzett érték és a nyers funkció fontosságát az előre jelzett értékhez.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Vizualizáció az adatmodellek és a magyarázatok észleléséhez a képzés ideje alatt

A funkció fontossága diagramot a munkaterületen, [Azure Machine learning Studióban](https://ml.azure.com)jelenítheti meg. Az automatikus ML-Futtatás befejezése után válassza a **modell részleteinek megtekintése** lehetőséget egy adott Futtatás megtekintéséhez. A **magyarázatok lapon** megtekintheti a magyarázat megjelenítése irányítópultot.

[![Machine Learning értelmező architektúrája](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan engedélyezheti a modell magyarázatait és funkcióit a Azure Machine Learning SDK azon területein, amelyek [nem az automatizált](how-to-machine-learning-interpretability.md)gépi tanulást ismertetik.
