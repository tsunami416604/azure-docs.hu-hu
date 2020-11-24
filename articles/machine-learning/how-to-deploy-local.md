---
title: Helyi Futtatás és központi telepítés
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathat betanított modelleket a helyi gépen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550096"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Üzembe helyezheti a Azure Machine Learning-mel betanított helyi gépek modelljeit

Ez a cikk bemutatja, hogyan használható a helyi számítógép a Azure Machine Learning-ben létrehozott modellek betanításához vagy üzembe helyezéséhez. Azure Machine Learning rugalmassága lehetővé teszi, hogy működjön a legtöbb Python Machine learning-keretrendszerrel. A gépi tanulási megoldások általában olyan összetett függőségekkel rendelkeznek, amelyeket nehéz lehet duplikálni. Ez a cikk bemutatja, hogyan kereskedhet a teljes ellenőrzés és a könnyű használat között.

A helyi telepítés egyes forgatókönyvei a következők:

* Egy projekt elején gyorsan megismételheti az adat, parancsfájlokat és modelleket
* Hibakeresés és hibaelhárítás a későbbi fázisokban
* Végső üzembe helyezés felhasználó által felügyelt hardveren

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)
- Modell és környezet. Ha nem rendelkezik betanított modellel, használhatja az [oktatóanyagban](tutorial-train-models-with-aml.md) megadott modellt és függőségi fájlokat
- A [Pythonhoz készült Azure Machine learning szoftverfejlesztői készlet (SDK)](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- Conda-kezelő, például anaconda vagy miniconda, ha szeretné tükrözni Azure Machine Learning csomagjának függőségeit
- Docker, ha a Azure Machine Learning környezet egy tárolós verzióját szeretné használni

## <a name="prepare-your-local-machine"></a>A helyi gép előkészítése

Azure Machine Learning modell helyi futtatásának legmegbízhatóbb módja egy Docker-rendszerkép. A Docker-rendszerkép egy elkülönített, tárolóban lévő, duplikált, a hardverrel kapcsolatos problémákat, az Azure végrehajtási környezetét is lehetővé teszi. A Docker fejlesztési forgatókönyvek telepítésével és konfigurálásával kapcsolatos további információkért lásd: a [Windowson a Docker Remote Development áttekintése](/windows/dev-environment/docker/overview).

Habár lehetséges a hibakereső csatlakoztatása a Docker-ben futó folyamathoz (lásd: [csatolás egy futó tárolóhoz](https://code.visualstudio.com/docs/remote/attach-container)), érdemes lehet hibakeresést végezni, és megismételni a Python-kódot a Docker bevonása nélkül. Ebben az esetben fontos, hogy a helyi gép ugyanazokat a kódtárakat használja, mint amelyeket a kísérlet Azure Machine Learning való futtatásakor használ. A Python-függőségek kezeléséhez az Azure a [Conda](https://docs.conda.io/)-t használja. Habár a környezetet más csomagkezelő használatával is újra létrehozhatja, a helyi gépen a Conda telepítése és konfigurálása a legegyszerűbb módja a szinkronizálásnak. 

## <a name="prepare-your-entry-script"></a>A nevezési parancsfájl előkészítése

Akkor is, ha a Docker használatával kezeli a modellt és a függőségeket, a Python pontozási parancsfájlnak helyinek kell lennie. A szkriptnek két módszerrel kell rendelkeznie:

- Olyan `init()` metódus, amely nem tartalmaz argumentumot, és nem ad vissza semmit 
- Egy `run()` JSON-formázott karakterláncot tartalmazó metódus, amely egy JSON-szerializálható objektumot ad vissza.

A metódus argumentuma a következő lesz `run()` : 

```json
{
    "data": <model-specific-data-structure>
}
```

A metódusból visszaadott objektumnak `run()` meg kell valósítania `toJSON() -> string` .

Az alábbi példa bemutatja, hogyan tölthető be a regisztrált scikit-modell, és hogyan szerezheti be a NumPy-információkat. Ez a példa az [oktatóanyag](tutorial-train-models-with-aml.md)modelljén és függőségein alapul:

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

A speciális példákat, például az automatikus hencegés sémájának létrehozását, valamint a bináris adat (például képek) adatának a kiértékelését, a [speciális bejegyzés parancsfájl-létrehozási](how-to-deploy-advanced-entry-script.md)módját olvashatja. 

## <a name="deploy-as-a-local-web-service-using-docker"></a>Üzembe helyezés helyi webszolgáltatásként a Docker használatával

A Azure Machine Learning által használt környezet replikálásának legegyszerűbb módja, ha a Docker használatával helyez üzembe egy webszolgáltatást. A helyi gépen futó Docker a következőket teszi:

1. Kapcsolódás a Azure Machine Learning munkaterülethez, amelyben a modell regisztrálva van
1. `Model`A modellt jelképező objektum létrehozása
1. Hozzon létre egy `Environment` objektumot, amely tartalmazza a függőségeket, és definiálja azt a szoftver-környezetet, amelyben a kód futni fog
1. Hozzon létre egy `InferenceConfig` objektumot, amely társítja a bejegyzési parancsfájlt és a `Environment`
1. Alosztály `DeploymentConfiguration` objektumának létrehozása `LocalWebserviceDeploymentConfiguration`
1. Objektum létrehozásához használja a következőt: `Model.deploy()` `Webservice` . Ez a metódus letölti a Docker-rendszerképet, és társítja azt a `Model` , `InferenceConfig` , és `DeploymentConfiguration`
1. A és a közötti aktiválása `Webservice``Webservice.wait_for_deployment()`

Az alábbi kód a következő lépéseket mutatja be:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

A hívása `Model.deploy()` néhány percet is igénybe vehet. A kezdeti üzembe helyezést követően hatékonyabban használhatja a `update()` metódust, nem pedig teljesen új. Lásd: [központilag telepített webszolgáltatás frissítése](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>A helyi telepítés tesztelése

Az előző üzembe helyezési parancsfájl futtatásakor a rendszer kiírja az URI-t, amelyre a pontozáshoz (például) is KÖZZÉTEHETi az adatokat `http://localhost:6789/score` . Az alábbi példa egy olyan parancsfájlt mutat be, amely a `"sklearn-mnist-local"` helyileg üzembe helyezett modellel szerzi be a mintaadatok. A modell, ha megfelelően be van tanítva, a kikövetkeztetés `normalized_pixel_values` "2"-ként értelmezhető. 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>A modell letöltése és futtatása közvetlenül

Miközben a Docker használatával helyezi üzembe a modellt webszolgáltatásként a leggyakoribb lehetőség, érdemes lehet közvetlenül a helyi Python-szkriptekkel is futtatnia a kódot. Két fontos elemre lesz szüksége: 

- Maga a modell
- Azok a függőségek, amelyekre a modell támaszkodik 

A modell letöltése a következőket teheti:  

- A portálon válassza a **modellek** fület, válassza ki a kívánt modellt, majd a **részletek** lapról válassza a **Letöltés** lehetőséget.
- A parancssorból a használatával `az ml model download` (lásd a [modell letöltési referenciáját](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false))
- A Python SDK-val a metódus használatával `Model.download()` (lásd a [modell API-referenciáját](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false))

Az Azure-modell egy vagy több szerializált Python-objektum, amely Python-lé-fájlként (**. PKL** kiterjesztéssel) van csomagolva. A lé-fájl tartalma a modell betanításához használt ML-könyvtár vagy-módszertől függ. Ha például az oktatóanyag modelljéből származik, a modellt a következővel lehet betölteni:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

A függőségek mindig trükkös, különösen a gépi tanulással, ahol gyakran egy adott verzióra vonatkozó követelmények szédítő webes változata lehet. Az `Environment` osztály metódusának használatával újra létrehozhat egy Azure Machine learning környezetet a helyi gépen teljes Conda-környezetként vagy Docker-rendszerképként `build_local()` . 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

Ha az argumentumát a értékre állítja `build_local()` `useDocker` `True` , akkor a függvény Conda-környezet helyett Docker-rendszerképet hoz létre. Ha további vezérlésre van szüksége, használhatja a `Environment` `save_to_directory()` metódust, amely a **conda_dependencies. yml** és a **azureml_environment.jst írja be a** definíciós fájlokra, amelyeket a bővítmény alapjaként finomhangolásra és használatra használhat. 

Az `Environment` osztály számos más módszerrel is szinkronizálhatja a környezeteket a számítási hardverek, az Azure-munkaterület és a Docker-lemezképek között. További információkért lásd az API- [ `Environment` referenciát](/python/api/azureml-core/azureml.core.environment(class)).

Miután letöltötte a modellt, és feloldotta a függőségeit, nincsenek az Azure-ban definiált korlátozások a pontozási teljesítményének, a modell finomhangolásának, az átadási tanulás használatának és így tovább. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Újratanított modell feltöltése Azure Machine Learning

Ha van egy helyileg betanított vagy átképzett modellje, regisztrálhatja azt az Azure-ban. A regisztráció után folytathatja az Azure számítási szolgáltatással történő finomhangolását, vagy üzembe helyezheti azt az Azure-szolgáltatások, például az [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) vagy a [Triton következtetési kiszolgáló (előzetes verzió)](how-to-deploy-with-triton.md)használatával.

A Azure Machine Learning Python SDK-val való használathoz a modellt olyan szerializált Python-objektumként kell tárolni, amely egy **PKL** -fájlban van, és olyan metódust kell megvalósítani, `predict(data)` amely JSON-szerializálható objektumot ad vissza. Előfordulhat például, hogy egy helyileg betanított scikit-Learn diabétesz modellt tárol a következővel: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Ahhoz, hogy a modell elérhető legyen az Azure-ban, az `register()` osztály metódusát használhatja `Model` :

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Ezután megkeresheti az újonnan regisztrált modellt Azure Machine Learning Model ( **modell** ) lapján:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Képernyőkép a Azure Machine Learning Model lapról, amely a feltöltött modellt mutatja.":::

A modellek és környezetek feltöltésével és frissítésével kapcsolatos további információkért lásd: [modell regisztrálása és helyi telepítése speciális használattal](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Következő lépések

- A környezetek kezelésével kapcsolatos további információkért tekintse meg a következő témakört: [Create & Software Environments in Azure Machine learning](how-to-use-environments.md)
- Az adattárból származó adatok eléréséről a Kapcsolódás az [Azure-beli tárolási szolgáltatásokhoz](how-to-access-data.md) című témakörben olvashat bővebben.