---
title: Egy meglévő modell használatával
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja az Azure Machine Learning szolgáltatás, amely a szolgáltatás kívül is betanított modellek. Azure Machine Learning szolgáltatás kívül létrehozott modelleket használva regisztrálhat, és telepítheti őket a webszolgáltatás vagy az Azure IoT Edge-modul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453683"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatás egy meglévő modell használata

Ismerje meg, hogyan használható egy már létező gépi tanulási modellt az Azure Machine Learning szolgáltatással.

Ha egy gépi tanulási modellt, amely az Azure Machine Learning szolgáltatás kívül volt betanított, a szolgáltatás továbbra is használhatja a modellt üzembe helyezzük webszolgáltatásként, vagy az IoT Edge-eszköz. 

> [!TIP]
> Ez a cikk alapvető információkat biztosít regisztrálása és a egy már meglévő modell üzembe helyezésével. Miután üzembe helyezte az Azure Machine Learning szolgáltatás a modell figyelését teszi lehetővé. Azt is lehetővé teszi a központi telepítést, amely használható az adatok eltéréseket elemzés vagy képzési újabb verziója, a modell küldött bemeneti adatok tárolására.
>
> Az itt használt fogalmak és további információkért lásd: [kezelése, telepítheti és figyelheti a machine learning-modellek](concept-model-management-and-deployment.md).
>
> A telepítési folyamatról általános információkért lásd: [modellek Azure Machine Learning szolgáltatás üzembe helyezése](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. További információkért lásd: [hozzon létre egy munkaterületet](setup-create-workspace.md).

    > [!TIP]
    > Ez a cikk a Python példák feltételezik, hogy a `ws` változó beállítása az Azure Machine Learning szolgáltatás munkaterületén.
    >
    > A CLI-példák használata, helyőrző `myworkspace` és `myresourcegroup`. Cserélje le ezeket a munkaterületet, és az azt tartalmazó erőforráscsoport nevét.

* The Azure Machine Learning SDK. További információkért lásd: a Python SDK szakaszában [hozzon létre egy munkaterületet](setup-create-workspace.md#sdk).

* A [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és [Machine Learning CLI-bővítmény](reference-azure-machine-learning-cli.md).

* Betanított modell. A modell kell őrizhető meg, a fejlesztési környezetet az egy vagy több fájlt.

    > [!NOTE]
    > Bemutatása, Azure Machine Learning szolgáltatás kívül betanított modellek regisztrálása, ebben a cikkben példakódrészletben a Paolo Ripamonti Twitter sentiment analysis projekt által létrehozott modellek használata: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>A modellek regisztrálása

Modellek regisztrálása lehetővé teszi tárolja, verzió, és nyomon követheti a metaadatokat, modellek a munkaterületén. A következő Python és a parancssori példákban a `models` könyvtár tartalmazza a `model.h5`, `model.w2v`, `encoder.pkl`, és `tokenizer.pkl` fájlokat. Ebben a példában található fájlokat, feltölti a `models` egy új modell regisztrációs nevű könyvtárat `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

További információkért lásd: a [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) hivatkozást.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

További információkért lásd: a [az gépi tanulási modell register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) hivatkozást.


További információ a modell regisztrálását általában: [kezelése, telepítheti és figyelheti a machine learning-modellek](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Következtetésekhez konfiguráció definiálása

A következtetésekhez konfiguráció a környezet, az üzembe helyezett modell futtatásához használt határozza meg. A következtetésekhez konfigurációs hivatkozik a következő fájlokat, amelyek futtatni a modell telepítése:

* A modul. Az egyetlen érvényes érték a futtatókörnyezet jelenleg Python.
* Egy bejegyzés szkriptet. Ez a fájl (nevű `score.py`) betölti a modellt, ha az üzembe helyezett szolgáltatás elindul. A rendszer is fogad adatokat, és adná azt át a modellbe, majd válasz visszaküldése felelős.
* Egy conda-környezet fájlt. Ez a fájl határozza meg, hogy a modell és a bejegyzés parancsfájl futtatásához szükséges Python-csomagokat. 

Az alábbi példa bemutatja egy egyszerű következtetésekhez konfigurációt a Python SDK-val:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

További információkért lásd: a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) hivatkozást.

A parancssori felület a következtetésekhez konfigurációs tölt be egy YAML-fájlt:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Következtetésekhez konfigurációs további információkért lásd: [modellek Azure Machine Learning szolgáltatás üzembe helyezése](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Bejegyzés parancsfájl

A bejegyzés szkriptre csak két szükséges funkciókat, `init()` és `run(data)`. Ezek a függvények inicializálása a szolgáltatás indításkor, és futtassa a modell segítségével az ügyfelek által átadott kérelem adatai szolgálnak. A parancsfájl a többi betöltéséhez és a modellek futtatása kezeli.

> [!IMPORTANT]
> Nincs olyan általános bejegyzés parancsfájlt, amely minden modell esetében működik. A rendszer mindig használt modell vonatkozik. Ez értenie kell betölteni a modellt az adatok formátumát, amely a modellt vár, és hogyan pontszámot rendelni az adatokat a modell használatával.

A következő Python-kód példa bejegyzés parancsfájl (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

A parancsfájlok a bejegyzés további információkért lásd: [modellek Azure Machine Learning szolgáltatás üzembe helyezése](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Conda-környezet

A következő yaml-kódot a conda-környezetet a modell és a bejegyzés parancsfájl futtatásához szükséges ismerteti:

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

További információkért lásd: [modellek Azure Machine Learning szolgáltatás üzembe helyezése](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Adja meg a központi telepítés

A [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) csomag tartalmazza a központi telepítéshez használt osztályok. Az osztály használja azt határozza meg, ahol a modell üzembe lett helyezve. Például az Azure Kubernetes Service webszolgáltatásként üzembe helyezéséhez használjon [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) hozhat létre a központi telepítés konfigurálása.

A következő Python-kód egy helyi központi telepítés telepítési konfigurációja határozza meg. Ez a konfiguráció a modellt webszolgáltatásként, amely a helyi számítógépen telepíti.

> [!IMPORTANT]
> Egy helyi telepítéséhez van szükség a telepített és működő [Docker](https://www.docker.com/) a helyi számítógépen:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

További információkért lásd: a [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) hivatkozást.

A CLI egy YAML-fájlt tölt be a központi telepítés konfigurálása:

```YAML
{
    "computeType": "LOCAL"
}
```

Egy, a különböző számítási célt, például az Azure-felhőben, Azure Kubernetes Service üzembe helyezése nem kell csak a központi telepítési konfiguráció módosítása. További információkért lásd: [hogyan és hol helyezhet üzembe modelleket](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Az alábbi példa betölti a regisztrált modell nevű információkat `sentiment`, majd telepíti azt nevű szolgáltatás `sentiment`. A telepítés során a következtetésekhez és telepítési konfigurációt használt létrehozása és konfigurálása a service-környezet:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

További információkért lásd: a [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) hivatkozást.

A modell rendszerbe a parancssori felületen, használja a következő parancsot. Ez a parancs üzembe helyezi az 1. verzióját a regisztrált modell (`sentiment:1`) tárolt következtetésekhez és üzembe helyezési konfiguráció használatával a `inferenceConfig.json` és `deploymentConfig.json` fájlok:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

További információkért lásd: a [az gépi tanulási modell-üzembehelyezés](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) hivatkozást.

A telepítés további információkért lásd: [hogyan és hol helyezhet üzembe modelleket](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Kérés-válasz fogyasztás

Az üzembe helyezést követően a pontozási URI jelenik meg. Ez az URI segítségével az ügyfelek által a szolgáltatáshoz érkező kérések elküldése. Az alábbi példa egy alapszintű Python-kliens, amely elküldi az adatokat a szolgáltatásba, és megjeleníti a válasz:

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

Az üzemelő szolgáltatás használata. További információkért lásd: [hozzon létre egy ügyfél](how-to-consume-web-service.md).

## <a name="next-steps"></a>További lépések

* [Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése](how-to-enable-app-insights.md)
* [A modellek éles adatok gyűjtése](how-to-enable-data-collection.md)
* [Hogyan és hol érdemes a modellek üzembe helyezése](how-to-deploy-and-where.md)
* [Egy ügyfél egy üzembe helyezett modell létrehozása](how-to-consume-web-service.md)