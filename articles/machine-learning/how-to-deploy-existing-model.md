---
title: Meglévő modellek használata és üzembe helyezése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learningt a szolgáltatáson kívül betanított modellekkel. Regisztrálhatja a Azure Machine Learningon kívül létrehozott modelleket, majd webszolgáltatásként vagy Azure IoT Edge modulként is telepítheti őket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: d8f0f6c63c584cacfa5c996bd541ce4dfc4bd289
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763929"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Meglévő modell használata Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan használhat meglévő gépi tanulási modellt Azure Machine Learning használatával.

Ha Azure Machine Learningon kívül betanított gépi tanulási modellel rendelkezik, továbbra is használhatja a szolgáltatást a modell webszolgáltatásként való üzembe helyezésére vagy egy IoT Edge eszközre. 

> [!TIP]
> Ez a cikk a meglévő modellek regisztrálásával és üzembe helyezésével kapcsolatos alapvető információkat tartalmazza. A üzembe helyezést követően Azure Machine Learning a modell figyelését teszi lehetővé. Lehetővé teszi az üzembe helyezéshez továbbított bemeneti adatok tárolását is, amelyek adatdrift-elemzéshez vagy a modell új verzióinak betanításához használhatók.
>
> Az itt használt fogalmakkal és kifejezésekkel kapcsolatos további információkért lásd: [gépi tanulási modellek kezelése, üzembe helyezése és figyelése](concept-model-management-and-deployment.md).
>
> Az üzembe helyezési folyamattal kapcsolatos általános információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Munkaterület létrehozása](how-to-manage-workspace.md).

    > [!TIP]
    > A cikkben szereplő Python-példák azt feltételezik, hogy a `ws` változó a Azure Machine Learning munkaterületre van beállítva.
    >
    > A CLI-példák `myworkspace` és `myresourcegroup`helyőrzőjét használják. Cserélje le ezeket a munkaterület nevére és az azt tartalmazó erőforráscsoporthoz.

* A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és a [Machine learning CLI bővítmény](reference-azure-machine-learning-cli.md).

* Egy betanított modell. A modellt a fejlesztői környezet egy vagy több fájljába kell megőrizni.

    > [!NOTE]
    > Egy Azure Machine Learningon kívül betanított modell regisztrálásának bemutatásához a jelen cikkben szereplő kódrészletek a Paolo Ripamonti Twitter hangulat-elemzési projekt által létrehozott modelleket használják: [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>A modell (ek) regisztrálása

A modell regisztrálása lehetővé teszi a munkaterületen lévő modellekhez tartozó metaadatok tárolását, verzióját és nyomon követését. A következő Python-és CLI-példákban a `models` könyvtár tartalmazza a `model.h5`, `model.w2v`, `encoder.pkl`és `tokenizer.pkl` fájlokat. Ez a példa a `models` könyvtárban található fájlokat a `sentiment`nevű új modell regisztrációjának megfelelően tölti fel:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

További információ: [modell. Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) – hivatkozás.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

További információ: az [ml Model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) Reference.


A modell-regisztrációval kapcsolatos további információkért lásd: [gépi tanulási modellek kezelése, üzembe helyezése és figyelése](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Következtetési konfiguráció megadása

A következtetési konfiguráció határozza meg az üzembe helyezett modell futtatásához használt környezetet. A következtetési konfiguráció a következő entitásokra hivatkozik, amelyek a modellnek a telepítésekor történő futtatására szolgálnak:

* Egy bejegyzési parancsfájl. Ez a fájl (`score.py`) betölti a modellt a telepített szolgáltatás indításakor. Emellett az adatfogadásért, a modellbe való átadásért, majd a válasz visszaküldéséhez is felelős.
* Azure Machine Learning [környezet](how-to-use-environments.md). A környezet a modell és a bejegyzési parancsfájl futtatásához szükséges szoftver-függőségeket határozza meg.

Az alábbi példa azt szemlélteti, hogyan használható az SDK egy környezet létrehozásához, majd egy következtetési konfiguráció használatával:

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

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

További információkért tekintse át a következő cikkeket:

+ [Környezetek használata](how-to-use-environments.md).
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -hivatkozás.


A CLI betölti a következtetési konfigurációt egy YAML-fájlból:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

A CLI-vel a Conda környezet a következtetési konfiguráció által hivatkozott `myenv.yml` fájlban van definiálva. A következő YAML a fájl tartalma:

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
```

További információ a konfigurációval kapcsolatban: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Bejegyzési parancsfájl

A bejegyzési parancsfájlnak csak két szükséges funkciója van, `init()` és `run(data)`. Ezekkel a függvényekkel inicializálhatja a szolgáltatást indításkor, és futtathatja a modellt az ügyfél által átadott kérelem-adatok használatával. A parancsfájl többi része kezeli a modell (ek) betöltését és futtatását.

> [!IMPORTANT]
> Nincs olyan általános bejegyzési parancsfájl, amely minden modell esetében működik. Mindig a használt modellre jellemző. Meg kell ismernie, hogyan tölthető be a modell, a modell által várt adatformátum, valamint az adatok a modell használatával történő pontozása.

A következő Python-kód egy példaként szolgáló bejegyzési parancsfájl (`score.py`):

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

A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Központi telepítés meghatározása

A [webszolgáltatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) csomag az üzembe helyezéshez használt osztályokat tartalmazza. A használt osztály meghatározza a modell központi telepítését. Ha például webszolgáltatásként kíván üzembe helyezni az Azure Kubernetes szolgáltatásban, a [AksWebService. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) használatával hozza létre a telepítési konfigurációt.

A következő Python-kód egy helyi telepítés központi telepítési konfigurációját határozza meg. Ezzel a konfigurációval a modellt webszolgáltatásként helyezi üzembe a helyi számítógépen.

> [!IMPORTANT]
> A helyi telepítéshez a [Docker](https://www.docker.com/) működéséhez a helyi számítógépen kell telepíteni:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

További információ: [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) hivatkozás.

A CLI betölti a telepítési konfigurációt egy YAML-fájlból:

```YAML
{
    "computeType": "LOCAL"
}
```

Egy másik számítási célra való üzembe helyezés, például az Azure Kubernetes szolgáltatás az Azure-felhőben, olyan egyszerűen, mint a telepítési konfiguráció módosítása. További információt a [modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

## <a name="deploy-the-model"></a>A modell rendszerbe állítása

Az alábbi példa a `sentiment`nevű regisztrált modell adatait tölti be, majd `sentiment`néven telepíti a szolgáltatást. Az üzembe helyezés során a rendszer a következő viszonyítási konfigurációt és telepítési konfigurációt használja a szolgáltatási környezet létrehozásához és konfigurálásához:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

További információ: [modell. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) hivatkozás.

A modell parancssori felületről történő üzembe helyezéséhez használja a következő parancsot. Ez a parancs a regisztrált modell (`sentiment:1`) 1. verzióját telepíti a `inferenceConfig.json` és `deploymentConfig.json` fájlokban tárolt következtetési és telepítési konfiguráció használatával:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

További információ: az [ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference.

A telepítéssel kapcsolatos további információkért lásd a [modellek üzembe helyezésének módját és helyét](how-to-deploy-and-where.md)ismertető témakört.

## <a name="request-response-consumption"></a>Kérelem – válasz felhasználás

Az üzembe helyezés után megjelenik a pontozási URI. Ezt az URI-t használhatja az ügyfelek a szolgáltatásnak küldött kérések küldésére. A következő példa egy alapszintű Python-ügyfél, amely elküldi az adatait a szolgáltatásnak, és megjeleníti a választ:

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

Az üzembe helyezett szolgáltatás használatáról további információt az [ügyfél létrehozása](how-to-consume-web-service.md)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [Ügyfél létrehozása központilag telepített modellhez](how-to-consume-web-service.md)
