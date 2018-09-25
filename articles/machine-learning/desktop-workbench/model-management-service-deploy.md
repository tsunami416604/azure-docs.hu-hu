---
title: Az Azure Machine Learning-modell felügyeleti webszolgáltatás üzembe |} A Microsoft Docs
description: Ez a dokumentum kezelése az Azure Machine Learning-modell használatával, egy gépi tanulási modell üzembe helyezése a lépéseit ismerteti.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 2a486da73d00ddaebee5c1fe32fc6112c177f01e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992337"
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Egy gépi tanulási modellt üzembe webszolgáltatásként

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Az Azure Machine Learning Modellkezelés helyezhet üzembe modelleket, a Docker-alapú webszolgáltatások konténeralapú felületeket biztosít. Modellek létrehozása keretrendszereket, mint a Spark, a Microsoft Cognitive Toolkit (CNTK), Keras, tensorflow-hoz és Python használatával telepítheti. 

Ez a dokumentum ismerteti a lépéseket, a modellek üzembe helyezése az Azure Machine Learning Modellkezelési parancssori felület (CLI) használatával webszolgáltatásként.

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez

Ez az útmutató kinyerhessen kell közreműködői hozzáférés az Azure-előfizetéssel, vagy egy erőforráscsoport, a modellek üzembe helyezhető.
A parancssori felület előre telepítve áll rendelkezésre az Azure Machine Learning Workbench, majd a [Azure Dsvm](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).  Is telepíthető egy különálló csomagként.

Emellett a modell felügyeleti fiók és üzembe helyezési környezet már kell be kell állítania.  A modellkezelési fiókot és környezetet a helyi és a fürt üzembe helyezése beállításával kapcsolatos további információkért lásd: [Modellkezelési konfigurációs](deployment-setup-configuration.md).

## <a name="deploying-web-services"></a>Webszolgáltatások üzembe helyezéséhez
A CLI-k használatával, a helyi számítógépre vagy egy fürtön futtatott webszolgáltatásokat helyezhetnek üzembe.

Javasoljuk, hogy a helyi üzembe helyezéssel. Először ellenőrizze, hogy a modell és a kód működik, majd a webszolgáltatás üzembe helyezése egy fürtön üzemi szintű használatra.

A telepítési lépések a következők:
1. A mentett, betanított, Machine Learning-modellek használata
2. Hozzon létre egy sémát a webszolgáltatás bemeneti és kimeneti adatok
3. Docker-alapú tárolórendszerkép létrehozása
4. Hozzon létre, és a web Service szolgáltatásának telepítése

### <a name="1-save-your-model"></a>1. A modell mentése
Indítsa el a betanított modell mentett fájlt, például mymodel.pkl. A platform, betanítását és mentse a módosítást a attól függően változik, a fájl kiterjesztése. 

Tegyük fel a Python a Pickle-kódtár segítségével mentse egy betanított modellt egy fájlba. Íme egy [példa](http://scikit-learn.org/stable/modules/model_persistence.html) az Írisz adatkészletet használ:

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

Séma létrehozása nem kötelező, míg erősen ajánlott jobb kezelésére vonatkozó kérelem és a bemeneti időformátum meghatározásához.

Hozzon létre egy sémát a bemeneti és kimeneti a webszolgáltatás automatikus ellenőrzése. A CLI-k használatával is a séma a webszolgáltatás Swagger-dokumentumok létrehozásához.

A séma létrehozásához a következő kódtárakra importálása:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Ezt követően adja meg a bemeneti változók, például a Spark dataframe, Pandas dataframe vagy NumPy tömb. Az alábbi példában egy Numpy tömb:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
Az alábbi példa a Spark dataframe használja:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

A következő példában a PANDAS dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

Az alábbi példa egy általános JSON formátumot használja:

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Hozzon létre egy score.py fájlt
Azt adja meg egy score.py fájlt, amely betölti a modellt, és adja vissza az előrejelzési találat a modellel.

A fájlnak tartalmaznia kell két függvényt: init, és futtassa.

Adja hozzá az alábbi kód Modelladat-gyűjtési funkciót, amellyel a bemeneti és előrejelzési modelladatok gyűjtése engedélyezéséhez a score.py fájl elején

```python
from azureml.datacollector import ModelDataCollector
```

Ellenőrizze [Modelladat-gyűjtési](how-to-use-model-data-collection.md) Ez a funkció használatáról további részletekért.

#### <a name="init-function"></a>Init függvény
Az init függvény használatával a mentett modell betöltése.

A példában egy egyszerű init függvény a modell betöltésekor:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Függvény futtatása
A futtatási funkció a modell és a bemeneti adatok előrejelzési adja vissza.

A bemeneti adatok feldolgozását, és az előrejelzési eredményt visszaadó függvény futtatása egy egyszerű példát:

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

### <a name="4-register-a-model"></a>4. Regisztrálja a modellt
A következő parancs segítségével regisztrálja a modellt, a fenti 1. lépésben létrehozott

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Hozzon létre egy jegyzéket.
A következő parancs segítségével hozzon létre egy jegyzéket modell

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
Adhat hozzá a korábban regisztrált modell a jegyzékfájl argumentum használatával `--model-id` vagy `-i` a fenti parancsban. Több modell adható meg a további -i argumentumokat.

### <a name="6-create-an-image"></a>6. Rendszerkép létrehozása 
Beállítással, miután létrehozta a jegyzékfájlt, mielőtt rendszerképet hozhat létre. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>Egyetlen parancs használatával végrehajtani a modell regisztráció, a jegyzékfájlban és a modell létrehozásához. Használat -h a szolgáltatással create paranccsal további részletekért.

Alternatív megoldásként nincs egyetlen paranccsal regisztrálja a modellt, jegyzék létrehozásához, és hozzon létre egy rendszerképet (de nem létrehozása és üzembe helyezéséhez a web service még) egy lépést a következő.

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>A parancs paraméterei a további részletekért típus -h végén található a következő parancs például az ml lemezkép létrehozása -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Hozzon létre, és a web Service szolgáltatásának telepítése
Telepítse a szolgáltatást, a következő paranccsal:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Egyetlen parancs használatával végezze el az összes korábbi 4 lépéseket. Használat -h a szolgáltatással create paranccsal további részletekért.

Alternatív megoldásként nincs regisztrálja a modellt, hozzon létre egy jegyzéket, hozzon létre egy rendszerképet, létrehozása és a webszolgáltatás üzembe egy lépést a következő egyetlen paranccsal.

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8. Tesztelheti a szolgáltatást
Az alábbi parancs segítségével a szolgáltatás hívásához tudnivalók:

```
az ml service usage realtime -i <service id>
```

Hívja meg a szolgáltatás a Futtatás függvényt a parancssorból:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

Az alábbi példa egy mintául szolgáló Iris webszolgáltatás meghívja:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>További lépések
Most, hogy a webszolgáltatás futtatása helyileg tesztelt, telepíthet egy fürtön nagy léptékű használatra. A webszolgáltatás üzembe egy fürtöt a beállítás a részletekért lásd: [modell Felügyeletkonfigurálási](deployment-setup-configuration.md). 
