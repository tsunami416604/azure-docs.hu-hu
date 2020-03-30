---
title: Meglévő modellek használata és üzembe helyezése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az Azure Machine Learninget a szolgáltatáson kívül betanított modellekkel. Az Azure Machine Learningen kívül létrehozott modellekregisztrálhatók, majd webszolgáltatásként vagy Azure IoT Edge-modulként üzembe helyezhetik őket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472375"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Meglévő modell használata az Azure Machine Learning használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan használhatja a meglévő gépi tanulási modell az Azure Machine Learning.

Ha rendelkezik egy gépi tanulási modell, amely az Azure Machine Learningen kívül lett betanítva, továbbra is használhatja a szolgáltatást a modell webszolgáltatásként vagy IoT Edge-eszközként való üzembe helyezéséhez. 

> [!TIP]
> Ez a cikk alapvető információkat tartalmaz egy meglévő modell regisztrálása és üzembe helyezése. Üzembe helyezés után az Azure Machine Learning figyelést biztosít a modellhez. Azt is lehetővé teszi, hogy tárolja a központi telepítésre küldött bemeneti adatokat, amelyek adatelítás elemzés vagy a modell új verzióinak betanításához használható.
>
> Az itt használt fogalmakról és kifejezésekről a [Gépi tanulási modellek kezelése, üzembe helyezése és figyelése](concept-model-management-and-deployment.md)című témakörben olvashat bővebben.
>
> A telepítési folyamattal kapcsolatos általános információkért olvassa el a Modellek üzembe helyezése az Azure Machine Learning szolgáltatással című [témakört.](how-to-deploy-and-where.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információt a Munkaterület létrehozása című [témakörben talál.](how-to-manage-workspace.md)

    > [!TIP]
    > A python-példák ebben `ws` a cikkben feltételezik, hogy a változó az Azure Machine Learning-munkaterületre van beállítva.
    >
    > A CLI-példák a `myworkspace` `myresourcegroup`helyőrzőt használják a és a alkalmazásból. Cserélje le ezeket a munkaterület nevére és az azt tartalmazó erőforráscsoportra.

* Az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és [a Machine Learning CLI bővítmény.](reference-azure-machine-learning-cli.md)

* Egy képzett modell. A modellt meg kell persistni egy vagy több fájlt a fejlesztői környezetben.

    > [!NOTE]
    > Az Azure Machine Learningen kívül betanított modell regisztrálásának bemutatásához a jelen cikkben szereplő példakódrészletek a [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)Paolo Ripamonti Twitter-hangulatelemzési projektje által létrehozott modelleket használják: .

## <a name="register-the-models"></a>A modell(ek) regisztrálása

A modell regisztrálása lehetővé teszi a modellek metaadatait a munkaterületen való tárolását, verzióját és nyomon követését. A következő Python- és CLI-példákban `encoder.pkl`a `tokenizer.pkl` könyvtár a `models` `model.h5`, `model.w2v`, , és fájlokat tartalmazza. Ez a példa a `models` könyvtárban található fájlokat `sentiment`új modellregisztrációként tölti fel:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

További információ: [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) reference.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> A regisztrált modellhez hozzáadási `tags` és `properties` szótári objektumokat is beállíthat. Ezek az értékek később egy adott modell azonosításához használhatók. Például az alkalmazott keret, képzési paraméterek stb.

További információ: az [ml modellregiszter](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) hivatkozási ideje.


A modellregisztrációval kapcsolatos további információkért [lásd: Gépi tanulási modellek kezelése, üzembe helyezése és figyelése.](concept-model-management-and-deployment.md)

## <a name="define-inference-configuration"></a>Következtetési konfiguráció meghatározása

A következtetés konfigurációja határozza meg az üzembe helyezett modell futtatásához használt környezetet. A következtetés konfigurációja a következő entitásokra hivatkozik, amelyek a modell üzembe helyezésekor a következők:

* Egy belépési szkript. Ez a `score.py`fájl (név szerint) betölti a modellt, amikor az üzembe helyezett szolgáltatás elindul. Felelős továbbá az adatok fogadásáért, a modellnek való átadásáért, majd a válasz visszaküldésért.
* Egy Azure Machine Learning [környezetben.](how-to-use-environments.md) A környezet határozza meg a modell és a bejegyzésparancsfájl futtatásához szükséges szoftverfüggőségeket.

A következő példa bemutatja, hogyan lehet az SDK használatával környezetet létrehozni, majd egy következtetési konfigurációval használni:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

További információkért tekintse át a következő cikkeket:

+ [A környezetek használata](how-to-use-environments.md).
+ [KövetkeztetésKonfigurációs](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) hivatkozás.


A CLI betölti a következtetéskonfigurációt egy YAML-fájlból:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

A CLI-vel a conda környezet `myenv.yml` et a következtetés konfigurációja által hivatkozott fájl határozza meg. A fájl tartalma a következő YAML:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

A konfigurációval kapcsolatos további információkért lásd: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md)

### <a name="entry-script"></a>Bejegyzés parancsfájlja

A beviteli parancsfájlnak csak `init()` `run(data)`két szükséges funkciója van, és . Ezek a függvények a szolgáltatás indításkortörténő inicializálására és a modell futtatására szolgálnak az ügyfél által átadott kérelemadatok használatával. A parancsfájl többi része kezeli a modell(ek) betöltését és futtatását.

> [!IMPORTANT]
> Nincs olyan általános bejegyzésparancsfájl, amely minden modellnél működik. Mindig a használt modellre jellemző. Meg kell értenie, hogyan kell betölteni a modellt, a modell által elvárt adatformátumot, és hogyan kell az adatokat a modell használatával pontozni.

A következő Python-kód egy`score.py`példa bejegyzés parancsfájl ( ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

A bejegyzési parancsfájlokkal kapcsolatos további információkért lásd: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md)

## <a name="define-deployment"></a>Központi telepítés definiálása

A [webszolgáltatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) csomag tartalmazza a központi telepítéshez használt osztályokat. A használt osztály határozza meg, hogy a modell hol van telepítve. Például webszolgáltatásként az Azure Kubernetes szolgáltatás üzembe helyezéséhez használja [az AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) segítségével a központi telepítési konfiguráció létrehozásához.

A következő Python-kód határozza meg a központi telepítési konfigurációt a helyi központi telepítéshez. Ez a konfiguráció webszolgáltatásként telepíti a modellt a helyi számítógépre.

> [!IMPORTANT]
> A helyi központi telepítés a [Docker](https://www.docker.com/) működő telepítését igényli a helyi számítógépen:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

További információt a [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) hivatkozásban talál.

A CLI yaml-fájlból tölti be a központi telepítési konfigurációt:

```YAML
{
    "computeType": "LOCAL"
}
```

Üzembe helyezése egy másik számítási cél, például az Azure Kubernetes szolgáltatás az Azure-felhőben, ugyanolyan egyszerű, mint a központi telepítési konfiguráció módosítása. További információ: [Modellek üzembe helyezése és helye.](how-to-deploy-and-where.md)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A következő példa betölti a `sentiment`regisztrált modell nevét, majd `sentiment`telepíti azt a nevű szolgáltatásként. A telepítés során a következtetés konfigurációja és a központi telepítés konfigurációja a szolgáltatási környezet létrehozásához és konfigurálásához használatos:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

További információt a [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) referencia.

A modell CLI-ből történő központi telepítéséhez használja a következő parancsot. Ez a parancs a regisztrált modell`sentiment:1`( ) 1-es verzióját telepíti `inferenceConfig.json` `deploymentConfig.json` a következtetések és a fájlokban tárolt telepítési konfiguráció használatával:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

További információ: az [ml modell üzembe helyezési](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referencia.

A központi telepítésről további információt a Modellek üzembe helyezése című témakörben [talál.](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Kérés-válasz felhasználás

Üzembe helyezés után megjelenik a pontozási URI. Ezt az URI-t az ügyfelek a szolgáltatásnak küldött kérelmek küldésére használhatják. A következő példa egy alapvető Python-ügyfél, amely adatokat küld a szolgáltatásnak, és megjeleníti a választ:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

A telepített szolgáltatás felhasználásáról további információt az [Ügyfél létrehozása című](how-to-consume-web-service.md)témakörben talál.

## <a name="next-steps"></a>További lépések

* [Az Azure Machine Learning-modellek figyelése az Application Insights segítségével](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelésben lévő modellekről](how-to-enable-data-collection.md)
* [Modellek üzembe helyezése és helye](how-to-deploy-and-where.md)
* [Ügyfél létrehozása telepített modellhez](how-to-consume-web-service.md)
