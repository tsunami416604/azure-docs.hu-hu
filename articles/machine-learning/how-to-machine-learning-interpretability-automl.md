---
title: Modell értelmezhetősége az automatizált gépi tanulásban
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan kaphat magyarázatot arról, hogy az automatikus ml-modell hogyan határozza meg a szolgáltatás fontosságát, és előrejelzéseket készít az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 03/11/2020
ms.openlocfilehash: e2465a2df3fab736c8f118911da14ef23c8aec86
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437283"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Modell értelmezhetősége az automatizált gépi tanulásban

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan engedélyezheti az automatikus gépi tanulás (ML) értelmezhetőségi funkcióit az Azure Machine Learningben. Az automatikus ml segít megérteni a mesterséges funkciók fontosságát. 

Az összes SDK-verzió alapértelmezés szerint `model_explainability=True` az 1.0.85 után. Az SDK 1.0.85-ös és korábbi `model_explainability=True` verzióiban a felhasználóknak be kell állítaniuk az objektumban a `AutoMLConfig` modell értelmezhetőségének használatához. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

- A legjobb modell vagy modell betanítása során értelmezhetőséget végezhet.
- A vizualizációk engedélyezése az adatok mintáinak és magyarázatainak megjelenítéséhez.
- Értelmezhetőség megvalósítása a következtetés vagy a pontozás során.

## <a name="prerequisites"></a>Előfeltételek

- Értelmezhetőségi jellemzők. Fuss, `pip install azureml-interpret azureml-contrib-interpret` hogy a szükséges csomagokat.
- Az automatizált ml-kísérletek készítésének ismerete. Az Azure Machine Learning SDK használatával kapcsolatos további információkért töltse ki ezt a [regressziós modell oktatóanyagát,](tutorial-auto-train-models.md) vagy tekintse meg az [automatikus gépi tanulási kísérletek konfigurálását.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>Értelmezhetőség a legjobb modell képzése során

A magyarázat lekérése a `best_run`ból, amely tartalmazza a mesterséges funkciók magyarázatát.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Mesterséges funkciófontosság letöltése a műterméktárolóból

A mesterséges `ExplanationClient` funkció magyarázatainak letöltésére a må± `best_run`műtermék tárolójából töltheti le. 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Bármely modell edzése során értelmezhetőség 

A modell magyarázatainak kiszámításakor és megjelenítésekor nem korlátozódik az automatikus rendszerhiba-modell meglévő modellmagyarázatára. A modell magyarázatát különböző tesztadatokkal is megkaphatja. Ebben a szakaszban található lépések bemutatják, hogyan számíthatja ki és jelenítheti meg a tesztadatok alapján a mesterséges szolgáltatásfontosságot.

### <a name="retrieve-any-other-automl-model-from-training"></a>Bármely más AutoML-modell lekérése a betanításból

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>A modell magyarázatának beállítása

Használja, `automl_setup_model_explanations` hogy a mesterséges magyarázatot. A `fitted_model` hozhat létre a következő elemeket:

- Kiemelt adatok betanított vagy vizsgálati mintákból
- Mesterséges szolgáltatásnévlisták
- Kereshető osztályok a címkézett oszlopban a besorolási forgatókönyvekben

Az `automl_explainer_setup_obj` tartalmazza a fenti lista összes struktúrát.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>A Mimic explainer inicializálása a funkció fontosságához

Az AutoML-modellek magyarázatának létrehozásához használja az `MimicWrapper` osztályt. A MimicWrapper inicializálható a következő paraméterekkel:

- A magyarázó beállítási objektuma
- Az Ön munkaterülete
- LightGBM modell, amely az `fitted_model` automatizált ml-modell helyettesítőjeként működik

A MimicWrapper azt `automl_run` az objektumot is elviszi, ahol a megtervezett magyarázatok fel töltődnek.

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

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>A MimicExplainer használata a mesterséges funkciófontosság kiszámításához és megjelenítéséhez

A MimicWrapper metódust `explain()` az átalakított tesztmintákkal hívhatja meg, hogy megkapja a létrehozott mesterséges funkciók jellemzőfontosságát. Az automatikus `ExplanationDashboard` ml-es featurizers által létrehozott mesterséges funkciók szolgáltatásfontossági értékeinek irányítópult-vizualizációját is megtekintheti.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Értelmezhetőség a következtetés során

Ebben a szakaszban megtudhatja, hogyan lehet üzembe egy automatikus ml-modell a magyarázó, amely az előző szakaszban a magyarázatok kiszámításához használt.

### <a name="register-the-model-and-the-scoring-explainer"></a>Regisztrálja a modellt és a pontozási magyarázót

A `TreeScoringExplainer` segítségével hozza létre a pontozási magyarázó, amely kiszámítja a megtervezett jellemző fontossági értékeket a következtetési idő. Inicializálja a pontozási `feature_map` magyarázó a korábban kiszámított. 

Mentse a pontozási magyarázó, majd regisztrálja a modell és a pontozási magyarázó a modellkezelési szolgáltatás. Futtassa a következő kódot:

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Conda-függőségek létrehozása a szolgáltatás beállításához

Ezután hozza létre a szükséges környezeti függőségeket az üzembe helyezett modell tárolójában. Vegye figyelembe, hogy az azureml-defaults verzióval >= 1.0.45 kell szerepelnie pip-függőség, mert tartalmazza a szükséges funkciókat a modell webszolgáltatásként.

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

Telepítse a szolgáltatást a conda-fájl és a pontozási fájl használatával az előző lépésekből.

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

### <a name="inference-with-test-data"></a>Következtetés a vizsgálati adatokkal

Következtetéseket néhány tesztadatokat az automatikus ml-modell előre jelzett érték megtekintéséhez. Tekintse meg az előre jelzett érték mesterséges funkciófontosságát.

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

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Vizualizálás az adatok és magyarázatok mintáinak felfedezéséhez a betanítási időben

Az [Azure Machine Learning stúdióban](https://ml.azure.com)vizualizálhatja a szolgáltatásfontossági diagramot a munkaterületén. Az automatikus gépi tanulási folyamat befejezése után válassza **a Modell részleteinek megtekintése** lehetőséget egy adott futtatás megtekintéséhez. A **magyarázatvizualizációs** irányítópult megtekintéséhez kattintson a Magyarázatok fülre.

[![Gépi tanulási értelmezhetőségi architektúra](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni arról, hogy miként engedélyezheti a modellmagyarázatokat és a szolgáltatásfontosságot az Automatikus gépi tanulástól eltérő Azure Machine Learning SDK-területeken, tekintse meg az [értelmezhetőségről szóló cikket.](how-to-machine-learning-interpretability.md)
