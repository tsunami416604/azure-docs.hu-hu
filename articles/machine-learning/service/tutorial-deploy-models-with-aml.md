---
title: 'A képbesorolás oktatóanyaga: Modellek üzembe helyezése'
titleSuffix: Azure Machine Learning service
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure Machine Learning szolgáltatás képbesorolási modell üzembe helyezésére a scikit-learn alkalmazásával egy Python Jupyter-notebookban. Ez az oktatóanyag a két részből álló sorozat második része.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 57d34bb170c0ff86f3d3c42a25184d8af71c0270
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036220"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Oktatóanyag: Rendszerkép-besorolási modell üzembe helyezése Azure Container Instances

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az [előző oktatóanyagban](tutorial-train-models-with-aml.md) gépi tanulási modelleket tanított be, majd regisztrált egy modellt a felhőbeli munkaterületen.  

Most már készen áll arra, hogy webszolgáltatásként telepítse a modellt [Azure Container instances](https://docs.microsoft.com/azure/container-instances/)-ben. A webszolgáltatás egy rendszerkép, ebben az esetben egy Docker-rendszerkép. Magába foglalja a pontozási logikát és a modellt. 

Az oktatóanyag ezen részében a következő feladatokhoz használja a Azure Machine Learning szolgáltatást:

> [!div class="checklist"]
> * A tesztelési környezet beállítása.
> * A modell lekérése a munkaterületről.
> * A modell helyi tesztelése.
> * A modell üzembe helyezése Container Instances.
> * Tesztelje az üzembe helyezett modellt.

A Container Instances nagyszerű megoldás a munkafolyamatok tesztelésére és megismerésére. Méretezhető éles környezetekben üzemelő példányok fontolja meg az Azure Kubernetes Service. További információ: [a telepítés és a hol](how-to-deploy-and-where.md).

>[!NOTE]
> A cikkben ismertetett kód Azure Machine Learning SDK 1.0.41-verzióval lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

A jegyzetfüzet futtatásához először végezze el [a modell betanítását az oktatóanyagban (1. rész): Képbesorolási modell](tutorial-train-models-with-aml.md)betanítása.   Ezután nyissa meg az **oktatóanyagok/IMG-Classification-part2-Deploy. ipynb** jegyzetfüzetet ugyanazzal a notebook-kiszolgálóval.

Ez az oktatóanyag a githubon [](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetében](how-to-configure-environment.md#local)szeretné használni.  Győződjön meg arról, hogy `matplotlib` telepítve `scikit-learn` van és a környezetében. 

## <a name="start"></a>A környezet beállítása

Első lépésként állítsa be a tesztelési környezetet.

### <a name="import-packages"></a>Csomagok importálása

Importálja az oktatóanyaghoz szükséges Python-csomagokat:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>A modell beolvasása

Az előző oktatóanyagban regisztrált egy modellt a munkaterületen. Most töltse be a munkaterületet, és töltse le a modellt a helyi könyvtárba:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>A modell helyi tesztelése

A telepítése előtt győződjön meg róla, hogy a modell helyileg működik-e:
* Tesztelési célú adatterhelés.
* Tesztelési adatértékek előrejelzése.
* Vizsgálja meg a zűrzavar mátrixát.

### <a name="load-test-data"></a>Tesztadatok betöltése

Töltse be a tesztelési adatait a betanítási oktatóanyag során létrehozott **./Data/** könyvtárból:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>A tesztadatok előrejelzése

Az előrejelzések beszerzéséhez a teszt adatkészletet a modellhez kell etetni:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>A keveredési mátrix vizsgálata

Hozzon létre egy keveredési mátrixot, és ellenőrizze, hogy a tesztkészletből hány minta kapott helyes besorolást. Figyelje meg a helytelenül besorolt értéket a helytelen előrejelzések esetében: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

A kimenet a keveredési mátrixot jeleníti meg:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Használja a `matplotlib` kódtárat a keveredési mátrix grafikonként való megjelenítéséhez. Ebben a diagramban az x tengely megjeleníti a tényleges értékeket, az y tengely pedig megjeleníti az előre jelzett értékeket. Az egyes rácsok színe a hibák arányát jeleníti meg. Minél világosabb a szín, annál magasabb a hibaarány. Például sok 5 's besorolása 3. Így a fényes rács jelenik meg (5, 3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![A zavart mátrixot bemutató diagram](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Üzembe helyezés webszolgáltatásként

Miután tesztelte a modellt, és elégedett az eredménnyel, telepítse a modellt Container Instancesban üzemeltetett webszolgáltatásként. 

A Container Instances megfelelő környezetének létrehozásához adja meg a következő összetevőket:
* Egy pontozási szkript, amely a modell használatát mutatja be.
* Egy környezeti fájl, amely megjeleníti, hogy milyen csomagokat kell telepíteni.
* A tároló példányának összeállítására szolgáló konfigurációs fájl.
* A korábban betanított modell.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Pontozószkript létrehozása

Hozza létre az **score.py**nevű pontozási szkriptet. A webszolgáltatás hívása ezt a parancsfájlt használja a modell használatának megjelenítéséhez.

Adja meg a következő két szükséges függvényt a pontozási parancsfájlban:
* Az `init()` függvényt, amely általában a modellt tölti be a globális objektumba. Ezt a függvényt csak egyszer kell futtatni, a Docker-tároló indításakor. 

* A `run(input_data)` függvény a modell segítségével értékeket jelez elő a bemeneti adatok alapján. A futtatás bemenetei és kimenetei általában JSON-fájlokat használnak a szerializáláshoz vagy a deszerializáláshoz, de más formátumokat is támogatnak.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Környezeti fájl létrehozása

Ezután hozzon létre egy **myenv. YML**nevű környezeti fájlt, amely a parancsfájl összes csomag-függőségét megadja. Ezzel a fájllal meggyőződhet arról, hogy az összes függőség telepítve van-e a Docker-rendszerképben. Ehhez a modellhez `azureml-sdk`a következő igények szükségesek `scikit-learn` :

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Tekintse át a `myenv.yml` fájl tartalmát:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Konfigurációs fájl létrehozása

Hozzon létre egy telepítési konfigurációs fájlt. Adja meg a Container Instances tárolóhoz szükséges processzorok és gigabájtok számát. Bár a modelltől függ, az alapértelmezett egy mag és 1 GB RAM elegendő a számos modellhez. Ha később további segítségre van szüksége, újra létre kell hoznia a lemezképet, és újra kell telepítenie a szolgáltatást.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Üzembe helyezés Container Instances
Az üzembe helyezés befejezésének becsült ideje **körülbelül hét – nyolc percet**vesz igénybe.

Konfigurálja, majd helyezze üzembe a rendszerképet. Az alábbi kód a következő lépéseket hajtja végre:

1. Hozzon létre egy rendszerképet a következő fájlok használatával:
   * A pontozási fájl `score.py`.
   * A környezeti fájl `myenv.yml`.
   * A modell fájlja.
1. Regisztrálja a rendszerképet a munkaterületen. 
1. Küldje el a rendszerképet a Container Instances tárolóba.
1. A rendszerkép használatával indítson el egy tárolót Container Instances.
1. A webszolgáltatás HTTP-végpontjának lekérése.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Kérje le a pontozási webszolgáltatás REST-ügyfélhívásokat fogadó HTTP-végpontját. Ezt a végpontot bárki megoszthatja, aki szeretné tesztelni a webszolgáltatást, vagy integrálni azt egy alkalmazásba: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>A telepített szolgáltatás tesztelése

Korábban az összes tesztelési adatmennyiséget a modell helyi verziójával szerezte ki. Most már tesztelheti az üzembe helyezett modellt a tesztelési adatokból származó, 30 lemezképből álló véletlenszerű minta használatával.  

Az alábbi kód a következő lépéseket hajtja végre:
1. Az adatküldés JSON-tömbként a Container Instancesban üzemeltetett webszolgáltatásnak. 

1. A szolgáltatás meghívása az SDK `run` API-jának használatával. A nyers hívásokat bármilyen HTTP-eszközzel, például a **curl**használatával is elvégezheti.

1. A kapott előrejelzések kinyomtatása és a bemeneti képekkel való ábrázolása. A nem besorolt minták kiemelésére a piros betűkészlet és az inverz, fehér feketén található képet használjuk. 

Mivel a modell pontossága magas, előfordulhat, hogy a következő kódot néhány alkalommal le kell futtatnia, mielőtt a nem megfelelő besorolt mintát lássuk:

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize=(20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Ez az eredmény a tesztelési lemezképek egy véletlenszerű mintája:

![Az eredményeket bemutató ábra](./media/tutorial-deploy-models-with-aml/results.png)

A webszolgáltatás teszteléséhez nyers HTTP-kérést is küldhet:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforráscsoportot és a munkaterületet más oktatóanyagokhoz és feltáráshoz szeretné megőrizni, csak a Container Instances üzemelő példányt törölheti az alábbi API-hívás használatával:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>További lépések

+ További információ a [Azure Machine learning szolgáltatás összes üzembe helyezési lehetőségéről](how-to-deploy-and-where.md).
+ Ismerje meg, hogyan [hozhat létre ügyfeleket a](how-to-consume-web-service.md)webszolgáltatáshoz.
+  [Előrejelzések készítése aszinkron módon nagy mennyiségű adattal](how-to-run-batch-predictions.md) .
+ A Azure Machine Learning-modellek monitorozása [Application Insightsokkal](how-to-enable-app-insights.md).
+ Próbálja ki az [automatikus algoritmus kiválasztása](tutorial-auto-train-models.md) oktatóanyagot. 
