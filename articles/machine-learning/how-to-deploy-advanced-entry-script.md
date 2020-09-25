---
title: A speciális forgatókönyvek szerzői bejegyzési parancsfájlja
titleSuffix: Azure Machine Learning entry script authoring
description: Megtudhatja, hogyan írhat Azure Machine Learning bejegyzési parancsfájlokat az üzembe helyezés előtti és utáni feldolgozáshoz.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.openlocfilehash: 0146c6c003e3c22b63b5fde5c8979a9d7c112b69
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261391"
---
# <a name="advanced-entry-script-authoring"></a>Speciális bejegyzésszkript létrehozása

Ez a cikk bemutatja, hogyan írhatók be a speciális használati esetekben a beléptetési parancsfájlok.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik egy betanított gépi tanulási modellel, amelyet a Azure Machine Learning használatával szeretne üzembe helyezni. A modell üzembe helyezésével kapcsolatos további tudnivalókért tekintse meg [ezt az oktatóanyagot](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Hencegő séma automatikus létrehozása

Ha automatikusan szeretne létrehozni egy sémát a webszolgáltatás számára, adja meg a bemeneti és/vagy kimeneti adatokat a konstruktorban egy adott típusú objektumhoz. A rendszer a típust és a mintát használja a séma automatikus létrehozásához. A Azure Machine Learning Ezután létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (hencegő) specifikációt a webszolgáltatás számára az üzembe helyezés során.

Ezek a típusok jelenleg támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objektum

A séma generálásához a függőségek fájljában adja meg a nyílt forráskódú `inference-schema` csomag 1.1.0-es vagy újabb verzióját. A csomaggal kapcsolatos további információkért lásd: [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . A hencegő automatizált webszolgáltatás-felhasználás létrehozásához a pontozási szkript futtatása () függvénynek API-formának kell lennie:
* Egy "StandardPythonParameterType" típusú első paraméter, amely elnevezett bemenetek, beágyazott PandasDataframeParameterTypes tartalmaz.
* Egy "StandardPythonParameterType" típusú, GlobalParameter nevű második paraméter, amely nincs beágyazva.
* Egy "StandardPythonParameterType" típusú szótárt ad vissza, amely a PandasDataFrameParameterTypes tartalmazó beágyazást is magában foglalhatja.
Adja meg a bemeneti és kimeneti minták formátumait a `input_sample` és a `output_sample` változóban, amelyek a webszolgáltatáshoz tartozó kérések és válaszok formátumait jelölik. Ezeket a mintákat a függvény bemeneti és kimeneti függvényében használhatja `run()` . A következő scikit példa a séma generálását használja.



```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

## <a name="power-bi-compatible-endpoint"></a>Power BI kompatibilis végpont 

Az alábbi példa bemutatja, hogyan határozhatja meg az API-alakzatokat a fenti utasítások szerint. Ez a módszer a központilag telepített webszolgáltatás Power BI való felhasználására használható. ([További információ a webszolgáltatás Power BIból való](https://docs.microsoft.com/power-bi/service-machine-learning-integration)használatáról.)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)

# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                            'input2': pandas_sample_input, 
                                            'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) #this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])

@input_schema('inputs', sample_input)
@input_schema('global_parameters', sample_global_parameters) #this is optional
@output_schema(sample_output)
def run(inputs, global_parameters):
    try:
        data = inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Bináris (azaz Képfájlos) adatok

Ha a modellben bináris adatok (például rendszerkép) is szerepelnek, akkor módosítania kell a `score.py` központi telepítéshez használt fájlt, hogy fogadja a nyers http-kérelmeket. A nyers adat elfogadásához használja az `AMLRequest` osztályt a bejegyzési parancsfájlban, és adja hozzá a `@rawhttp` bedekorációt a `run()` függvényhez.

Az alábbi példa egy olyan példát mutat be, `score.py` amely a bináris adatmennyiséget fogadja el:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLRequest` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```

Az `AMLRequest` osztály csak a score.py található nyers közzétett adat elérését teszi lehetővé, nincs ügyféloldali összetevő. Egy ügyfélről az adatok a szokásos módon kerülnek közzétételre. Például a következő Python-kód beolvas egy képfájlt, és az adatokat:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Több eredetű erőforrás-megosztás (CORS)

Az eltérő eredetű erőforrás-megosztás lehetővé teszi, hogy egy weblapon lévő erőforrásokat egy másik tartománytól lehessen kérni. A CORS az ügyfél kérelmével elküldött HTTP-fejléceken keresztül működik, és a szolgáltatás válaszával tért vissza. További információ a CORS és az érvényes fejlécekről: a wikipedia [több eredetű erőforrás-megosztása](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

A modell telepítésének a CORS támogatására való konfigurálásához használja a `AMLResponse` osztályt a bejegyzési parancsfájlban. Ez az osztály lehetővé teszi a fejlécek beállítását a válasz objektumon.

A következő példa a `Access-Control-Allow-Origin` bejegyzési parancsfájlból származó válasz fejlécét állítja be:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLResponse` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning csak a POST és a GET kérelmeket irányítja a pontozási szolgáltatást futtató tárolók számára. Ez hibákhoz vezethet, mert a böngészők a CORS-kérelmekre vonatkozó beállításokat használnak.
> 


## <a name="load-registered-models"></a>Regisztrált modellek betöltése

A következő két módon kereshet modelleket a nevezési parancsfájlban:
* `AZUREML_MODEL_DIR`: Egy környezeti változó, amely a modell helyének elérési útját tartalmazza.
* `Model.get_model_path`: Egy API, amely visszaadja a modell elérési útját a regisztrált modell nevével.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR a szolgáltatás telepítése során létrehozott környezeti változó. Ezt a környezeti változót használhatja a telepített modell (ek) helyének megkereséséhez.

A következő táblázat a telepített modellek számától függően AZUREML_MODEL_DIR értékét ismerteti:

| Üzembe helyezés | Környezeti változó értéke |
| ----- | ----- |
| Egyetlen modell | A modellt tartalmazó mappa elérési útja. |
| Több modell | Az összes modellt tartalmazó mappa elérési útja. A modellek a mappa neve és verziója szerint találhatók ( `$MODEL_NAME/$VERSION` ) |

A modell regisztrálása és üzembe helyezése során a rendszer a modelleket a AZUREML_MODEL_DIR útvonalon helyezi el, és az eredeti fájlnevek megmaradnak.

Ha szeretné beolvasni a parancsfájl elérési útját a bejegyzési parancsfájlban, akkor a környezeti változót a keresett fájl elérési útjával kell egyesíteni.

**Példa egyetlen modellre**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Több modell – példa**

Ebben a forgatókönyvben két modell van regisztrálva a munkaterületen:

* `my_first_model`: Egy fájlt ( `my_first_model.pkl` ) tartalmaz, és csak egy verzió ( `1` ) található.
* `my_second_model`: Egy fájlt ( `my_second_model.pkl` ) tartalmaz, és két verzió; `1` és `2` .

A szolgáltatás üzembe helyezése után mindkét modell a telepítés műveletben van megadva:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

A szolgáltatást futtató Docker-rendszerképben a `AZUREML_MODEL_DIR` környezeti változó tartalmazza azt a könyvtárat, ahol a modellek találhatók.
Ebben a könyvtárban mindegyik modell egy könyvtár elérési útjában található `MODEL_NAME/VERSION` . Ahol a a `MODEL_NAME` regisztrált modell neve, és `VERSION` a modell verziója. A regisztrált modellt alkotó fájlokat a rendszer ezekben a címtárakban tárolja.

Ebben a példában az elérési utak a következőek: `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` és `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Modell regisztrálása esetén meg kell adnia a modellnek a beállításjegyzékben való kezeléséhez használt modell nevét. Ezt a nevet használja a [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) metódussal, hogy lekérje a modell fájljának vagy fájljainak elérési útját a helyi fájlrendszerben. Ha egy mappát vagy fájl-gyűjteményt regisztrál, az API a fájlokat tartalmazó könyvtár elérési útját adja vissza.

Modell regisztrálása esetén a nevet adja meg. A név a modell elhelyezésének helyét adja meg helyileg vagy a szolgáltatás telepítése során.

## <a name="framework-specific-examples"></a>Keretrendszer-specifikus példák

Az egyes gépi tanulási használati esetekben további bejegyzési parancsfájlokra példákat talál a következő címen:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>Következő lépések

* [Sikertelen üzembe helyezés hibáinak megoldása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Esemény-riasztások és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)
