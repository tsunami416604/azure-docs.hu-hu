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
ms.openlocfilehash: b2c7825b10feab45df9cb89dbe2b82da1c143866
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129748"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>A modell értelmezése az automatizált gépi tanulásban

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan engedélyezheti az automatikus gépi tanulás (ML) értelmező funkcióit Azure Machine Learning. Az automatikus ML segít megismerni a megtervezett funkciók fontosságát. 

A 1.0.85 utáni összes SDK-verzió alapértelmezés szerint `model_explainability=True`. Az SDK-verzió 1.0.85 és korábbi verzióiban a felhasználóknak be kell állítaniuk `model_explainability=True` a `AutoMLConfig` objektumban a modell-értelmező használata érdekében. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

- A legjobb modell vagy bármely modell tanítása során végezzen tolmácsolást.
- A vizualizációk lehetővé teszik az adatmintázatok és a magyarázatok megjelenítését.
- Értelmezhető megvalósítás a következtetés vagy pontozás során.

## <a name="prerequisites"></a>Előfeltételek

- Értelmező funkciók. Futtassa `pip install azureml-interpret azureml-contrib-interpret` a szükséges csomagok beszerzéséhez.
- Az automatizált ML-kísérletek létrehozásának ismerete. További információ az Azure Machine Learning SDK használatáról: a [regressziós modell oktatóanyagának](tutorial-auto-train-models.md) elvégzése, vagy az [automatikus ml-kísérletek konfigurálása](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>A legjobb modell képzésének értelmezése

Kérje le a `best_run`magyarázatát, amely tartalmazza a megtervezett funkciók magyarázatait.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Az összetevő-tárolóban megjelenő Kiemelt funkciók fontosságának letöltése

A `ExplanationClient` használatával letöltheti a mesterséges funkciók magyarázatait a `best_run`összetevő-tárolójából. 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Értelmezés a modell betanítása során 

Ha kiszámítja a modellre vonatkozó magyarázatokat, és megjeleníti őket, nem korlátozódik az automatizált ML-modellre vonatkozó, meglévő modellre. A modellre vonatkozó magyarázatot különböző tesztelési adataival is megtalálhatja. Az ebben a szakaszban ismertetett lépések bemutatják, hogyan számíthatja ki és jelenítheti meg a tesztelési adatokon alapuló, mérnöki funkciók fontosságát.

### <a name="retrieve-any-other-automl-model-from-training"></a>Bármely más AutoML-modell beolvasása a képzésből

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>A modell magyarázatának beállítása

Az `automl_setup_model_explanations` segítségével megtalálhatja a megtervezett magyarázatokat. A `fitted_model` a következő elemeket tudja előállítani:

- Kiemelt adatok a betanított vagy tesztelési mintákból
- Megtervezett funkciók neve listázza
- Megkereshető osztályok a címkével ellátott oszlopban besorolási helyzetekben

A `automl_explainer_setup_obj` a fenti listában szereplő összes struktúrát tartalmazza.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>A funkciók fontosságának inicializálása az utánozó-magyarázattal

A AutoML-modellek magyarázatának létrehozásához használja a `MimicWrapper` osztályt. A MimicWrapper a következő paraméterekkel inicializálhatja:

- A magyarázó telepítő objektum
- Munkaterülete
- LightGBM-modell, amely helyettesítőként működik a `fitted_model` automatizált ML-modellben

A MimicWrapper azt a `automl_run` objektumot is felveszi, ahol a megtervezett magyarázatok fel lesznek töltve.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>A MimicExplainer használata a mérnöki funkciók fontosságának meghatározásához és megjelenítéséhez

A MimicWrapper `explain()` metódusát meghívhatja az átalakított tesztelési mintákkal, hogy a funkció fontosságot kapjon a generált mérnöki funkciók számára. Az `ExplanationDashboard` segítségével is megtekintheti a létrehozott mérnöki funkciók kiemelt fontosságú funkcióinak irányítópult-vizualizációját az automatikus ML featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Értelmezhető a következtetés során

Ebből a szakaszból megtudhatja, hogyan működővé tenni egy olyan automatizált ML-modellt, amely az előző szakaszban ismertetett magyarázatok kiszámításához használatos.

### <a name="register-the-model-and-the-scoring-explainer"></a>A modell és a pontozási magyarázat regisztrálása

A `TreeScoringExplainer` segítségével hozza létre a pontozási magyarázatot, amely kiszámítja a tervezett funkció fontossági értékeit a következtetések időpontjában. A pontozási magyarázatot a korábban kiszámított `feature_map` inicializálhatja. 

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

Bizonyos tesztelési adatok alapján megtekintheti az automatikus ML-modellből származó előre jelzett értéket. Megtekintheti az előre jelzett értékhez tartozó mérnöki funkció fontosságát.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Vizualizáció az adatmodellek és a magyarázatok észleléséhez a képzés ideje alatt

A funkció fontossága diagramot a munkaterületen, [Azure Machine learning Studióban](https://ml.azure.com)jelenítheti meg. Az automatikus ML-Futtatás befejezése után válassza a **modell részleteinek megtekintése** lehetőséget egy adott Futtatás megtekintéséhez. A **magyarázatok lapon** megtekintheti a magyarázat megjelenítése irányítópultot.

[![Machine Learning értelmező architektúrája](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan engedélyezheti a modell magyarázatait és funkcióit a Azure Machine Learning SDK azon területein, amelyek [nem az automatizált](how-to-machine-learning-interpretability.md)gépi tanulást ismertetik.
