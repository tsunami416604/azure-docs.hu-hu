---
title: Modell értelmezése automatizált ML-ben
titleSuffix: Azure Machine Learning
description: Ebből a témakörből megtudhatja, hogy az automatikus ML-modell miért teszi lehetővé az előrejelzések használatát az Azure Machine Learning SDK-val. A képzés során felhasználható, hogy megtudja, hogyan határozza meg a modell a funkciók fontosságát, és előrejelzéseket készít.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515329"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Modellek értelmezése automatizált ML-modellekhez

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a útmutatóban megtudhatja, hogyan engedélyezheti a tolmácsolási funkciókat az automatizált gépi tanulásban Azure Machine Learning szolgáltatás használatával. Az automatikus ML lehetővé teszi a nyers és a Kiemelt funkciók fontosságának megismerését. A modell-értelmező használatához a `AutoMLConfig` objektumban állítsa be a `model_explainability=True`.  

Ez a cikk a következő feladatokat ismerteti:

* A legjobb modell vagy bármely modell betanítása során értelmezhető
* Vizualizációk engedélyezése az adatmintázatok felderítésében és a magyarázatokban
* Értelmezés a következtetés vagy pontozás során

## <a name="prerequisites"></a>Előfeltételek

* `pip install azureml-interpret azureml-contrib-interpret` futtatása a szükséges csomagok beszerzéséhez a tolmácsolási funkciókhoz.
* Ez a cikk az automatikus ML-kísérletek létrehozásának ismeretét feltételezi. Tekintse meg az [oktatóanyag](tutorial-auto-train-models.md) vagy útmutató című témakört, amelyből megtudhatja, hogyan használhatja az automatikus ml-t az SDK [-](how-to-configure-auto-train.md) ban.
 
## <a name="interpretability-during-training-for-the-best-model"></a>A legjobb modell képzésének értelmezése 

Kérje le a `best_run`magyarázatát, amely tartalmazza a megtervezett funkciók és a nyers funkciók magyarázatait. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Az összetevő-tárolóban megjelenő Kiemelt funkciók fontosságának letöltése

A `ExplanationClient` használatával letöltheti a megtervezett funkciók magyarázatait a best_run összetevő-tárolójából. A `raw=True`által beállított nyers szolgáltatások magyarázatának megismeréséhez. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Értelmezés a modell betanítása során 

Ebből a szakaszból megtudhatja, hogyan számíthatja ki a modell magyarázatait, és hogyan jelenítheti meg a magyarázatokat. Az automatizált ML-modellekhez tartozó meglévő modell beolvasása mellett különböző tesztelési adatokkal is elmagyarázhatja a modellt. A következő lépésekkel kiszámíthatja és megjelenítheti a funkciók fontosságát és a nyers funkciók fontosságát a tesztelési adatok alapján.

### <a name="retrieve-any-other-automl-model-from-training"></a>Bármely más AutoML-modell beolvasása a képzésből

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>A modell magyarázatának beállítása

A fitted_model a következő elemeket tudja előállítani, amelyeket a rendszer a mérnöki és nyers funkciók magyarázatának beszerzéséhez használ a automl_setup_model_explanations használatával:

* Featurized és tesztelési mintákból származó adatok
* A feldolgozatlan és a nyers szolgáltatások nevének listája
* A címkével ellátott oszlopban szereplő osztályok megkeresése besorolási helyzetekben

A automl_explainer_setup_obj tartalmazza a fenti lista összes szerkezetét.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>A funkciók fontosságának inicializálása az utánozó-magyarázattal

A AutoML modellek ismertetése a `MimicWrapper` osztályt használja. A MimicWrapper inicializálható paraméterekkel az elmagyarázó beállítási objektumhoz, a munkaterülethez és egy LightGBM-modellhez, amely helyettesítő modellként szolgál az automatikus ML-modell (fitted_model) elmagyarázása érdekében. A MimicWrapper a automl_run objektumot is felveszi, ahol a nyers és a megtervezett magyarázatok fel lesznek töltve.

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

A MimicWrapper magyarázata () metódusa az átalakított tesztelési mintákkal hívható meg, hogy a funkció fontosságot kapjon a generált mérnöki funkciók számára. A ExplanationDashboard használatával is megtekintheti a generált mérnöki funkciók kiemelt fontosságú funkcióinak az automatikus ML-featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Az utánozó elmagyarázó használata a nyers funkciók fontosságának meghatározásához és megjelenítéséhez

A MimicWrapper magyarázata () metódusa újra meghívható az átalakított tesztelési mintákkal, és a `get_raw`t igaz értékre állítva, hogy a funkció fontosságot kapjon a nyers funkciókhoz. A ExplanationDashboard használatával is megtekintheti a nyers szolgáltatások funkció fontossági értékeinek irányítópult-vizualizációját.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Értelmezhető a következtetés során

Ebből a szakaszból megtudhatja, hogyan működővé tenni egy automatizált ML-modellt a magyarázattal, amely az előző szakaszban ismertetett magyarázatok kiszámítására szolgál.

### <a name="register-the-model-and-the-scoring-explainer"></a>A modell és a pontozási magyarázat regisztrálása

A `TreeScoringExplainer` segítségével hozza létre a pontozási magyarázatot, amelyet a rendszer a nyers és a Kiemelt funkciók fontossági értékeinek kiszámítására használ a következtetések időpontjában. Vegye figyelembe, hogy a pontozási magyarázatot a korábban kiszámított feature_map inicializálja. A feature_map a pontozási magyarázat fogja használni a nyers funkció fontosságának visszaküldéséhez.

Az alábbi kódban mentse a pontozási magyarázatot, és regisztrálja a modellt és a pontozási magyarázatot a modellkezelés szolgáltatással.

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

Ezután hozza létre a telepített modellhez a tárolóban szükséges környezeti függőségeket.

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
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>Következtetés a teszt adataival

Némi tesztelési adatok használatával megtekintheti az automatikus ML-modellből származó előre jelzett értéket, és megtekintheti az előre jelzett érték és a nyers funkció fontosságát a tervezett érték szempontjából.

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

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Vizualizációk, amelyek segítséget nyújtanak az adatmintázatok felderítésében, valamint a betanítás időpontjában.

A szolgáltatás fontossága diagramot a munkaterületen is megjelenítheti [Azure Machine learning Studióban](https://ml.azure.com). Az automatikus ML-Futtatás befejezése után a "modell részleteinek megtekintése" gombra kell kattintania, amely egy adott futtatásra kerül. Innen a "magyarázatok" fülre kattintva megtekintheti a magyarázat vizualizációs irányítópultot. 

[![Machine Learning értelmező architektúrája](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>További lépések

További információk arról, hogy a modell magyarázatait és funkcióit hogyan lehet engedélyezni az SDK más területein az automatikus gépi tanuláson kívül, lásd az értelmezési cikk [fogalmát](how-to-machine-learning-interpretability.md) .