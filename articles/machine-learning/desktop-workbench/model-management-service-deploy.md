---
title: Azure Machine Learning modell felügyeleti webes szolgáltatás központi telepítése |} Microsoft Docs
description: Ez a dokumentum a gépi tanulási modellek felügyeleti modell Azure Machine Learning segítségével telepítésének lépéseit ismerteti.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 5211fa29af1d8cba17049b69974189990d30f34a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>A gépi tanulási modellek webszolgáltatásként telepítése

Modell kezelése az Azure Machine Learning modellek, Docker-alapú webes szolgáltatások indexelése telepítendő felületek biztosít. Modellek keretrendszerek, például a Spark, a Microsoft kognitív Toolkit (CNTK), Keras, Tensorflow és Python használatával telepítheti. 

Ez a dokumentum a modellek webszolgáltatásként, az Azure Machine Learning modell kezelése parancssori felület (CLI) használatával telepítésének a lépéseit ismerteti.

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez

Ahhoz, hogy ez az útmutató a legtöbbet, Azure-előfizetés vagy egy erőforráscsoport, amely központilag telepíthető a modellek közreműködői hozzáférést kell rendelkeznie.
A parancssori felület előre előre telepített, az Azure Machine Learning-munkaterület és a [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).  Az önálló csomag is telepíthető.

Emellett a modell felügyeleti fiók és a telepítési környezet már kell be kell állítania.  A modell felügyeleti fiókja és a helyi környezet és a fürtöt tartalmazó környezetben beállításával kapcsolatos további információk: [modell felügyeletkonfigurálási](deployment-setup-configuration.md).

## <a name="deploying-web-services"></a>Webszolgáltatások üzembe helyezése
A CLIs használja, telepítheti a webszolgáltatások futtatásához a helyi számítógépen vagy egy fürtön.

Azt javasoljuk, hogy a helyi telepítés kezdve. Először ellenőrizze, hogy a modell és kód működik, majd a webszolgáltatás telepítése egy fürt méretű üzemi használatra.

Az üzembe helyezés lépései a következők:
1. A mentett, betanított, gépi tanulási modell
2. Hozzon létre a webszolgáltatás bemeneti és kimeneti adatok sémát
3. Tároló Docker-alapú lemezkép létrehozása
4. Hozzon létre, és a webszolgáltatás üzembe helyezése

### <a name="1-save-your-model"></a>1. Mentse a modellt
Indítsa el a mentett betanított modell fájllal, például mymodel.pkl. A fájl kiterjesztése attól függően változik, a platform képzése, és mentse a modellt használja. 

Tegyük fel a Python tartozó körte könyvtár segítségével a betanított modell egy fájlba történő mentését. Íme egy [példa](http://scikit-learn.org/stable/modules/model_persistence.html) a Iris adatkészlet:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Hozzon létre egy schema.json fájlt

Séma generációs ugyan nem kötelező, lehetőleg jobban kezeli a kérelmet, és a bemeneti változó formátumban megadhatók.

Hozzon létre egy séma a bemeneti és kimeneti a webszolgáltatás automatikusan érvényesítéséhez. A CLIs is használhatja a séma létrehozni a Swagger-dokumentum a webszolgáltatáshoz.

A séma létrehozásához a következő könyvtárak importálása:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Ezt követően adja meg a bemeneti változók, például egy Spark dataframe, Pandas dataframe vagy NumPy tömb. A következő példában egy Numpy tömb:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
A következő példában egy Spark dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

A következő példában egy PANDAS dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

A következő példa egy általános JSON formátumot használja:

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Hozzon létre egy score.py fájlt
Megadja a score.py fájlt, amely betölti a modell és a modell előrejelzéses eredmény adja vissza.

A fájl két funkciók tartalmaznia kell: init és futtatásához.

Adja hozzá a következő kód funkciónak az adatok gyűjtése, amelyek segítségével a modell bemeneti és a előrejelzési adatokat gyűjteni a score.py fájl tetején

```python
from azureml.datacollector import ModelDataCollector
```

Ellenőrizze [modellhez tartozó adatok gyűjtésének](how-to-use-model-data-collection.md) ezzel a szolgáltatással kapcsolatos további információkért.

#### <a name="init-function"></a>Init függvény
A init funkcióval betölteni a mentett modellt.

Például egy egyszerű init függvény a modell betöltése:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Függvény futtatása
A futtatási funkció a modell és a bemeneti adatok térjen vissza az előrejelzéshez.

A bemenet feldolgozása és az előrejelzés eredményt visszaadó funkció futtatásához egy egyszerű példa:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. A modell regisztrálása
A következő parancsot egy modellt a fenti 1. lépésben létrehozott regisztrálásához használt

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Hozzon létre egy jegyzéket.
A következő parancs segítségével a jegyzék a modell létrehozása

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
Hozzáadhat egy korábban regisztrált modellhez a jegyzékfájl argumentum használatával `--model-id` vagy `-i` a fenti parancsban. További adható meg több modellek -i argumentumokat.

### <a name="6-create-an-image"></a>6. Lemezkép létrehozása 
A képfájl beállítással, a jegyzék előtt hozunk létre hozhat létre. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>Egy parancs segítségével hajtsa végre a modell regisztráció, a jegyzékfájlt és a modell létrehozása. Használjon -h a szolgáltatással létrehozhat további részletekért.

Alternatív megoldásként nincs regisztrálása a modell jegyzékfájl létrehozása és lemezkép létrehozása (de nem hozzon létre, és a webszolgáltatás telepítése még) egy parancs egyik lépés az alábbiak szerint.

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>A parancs paraméterei a további részletekért típus -h végén található a következő parancs például az ml lemezkép létrehozása -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Hozzon létre, és a webszolgáltatás üzembe helyezése
Telepítse a szolgáltatást, a következő parancsot:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Egy parancs használatával végezze el az összes korábbi 4 lépéseket. Használjon -h a szolgáltatással létrehozhat további részletekért.

Alternatív megoldásként nincs regisztrálása a modell jegyzékfájl létrehozása, készítsen lemezképet, valamint, hozzon létre, és a webszolgáltatás telepítése egy lépést, az alábbiak szerint egy parancs.

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8. A szolgáltatás tesztelése
Az alábbi parancs segítségével hogyan hívhatja meg a szolgáltatás információk beolvasása:

```
az ml service usage realtime -i <service id>
```

Meghívja a szolgáltatást, a parancssorból futtassa függvény használatával:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

A következő példa egy minta Iris webszolgáltatás meghívja:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>További lépések
Most, hogy a webszolgáltatás helyi futtatásához tesztelése, ezután telepítheti azt egy fürthöz, a nagyméretű használatra. Webes szolgáltatás központi telepítése egy fürt beállításával kapcsolatos részletekért lásd: [modell Felügyeletkonfigurálási](deployment-setup-configuration.md). 
