---
title: 'Kép besorolási oktatóanyag: Modellek üzembe helyezése'
titleSuffix: Azure Machine Learning service
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure Machine Learning szolgáltatás képbesorolási modell üzembe helyezésére a scikit-learn alkalmazásával egy Python Jupyter-notebookban. Ez az oktatóanyag egy sorozat első két közül a második.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: ea446c89fc74fca444793a5e0f803a54fa251ed1
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312170"
---
# <a name="tutorial--deploy-an-image-classification-model-in-azure-container-instance"></a>Oktatóanyag:  Egy rendszerkép besorolási modell az Azure-Tárolópéldányon üzembe helyezése

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az [előző oktatóanyagban](tutorial-train-models-with-aml.md) gépi tanulási modelleket tanított be, majd regisztrált egy modellt a felhőbeli munkaterületen.  

Most már készen áll a modellt üzembe helyezzük webszolgáltatásként, amely a [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). A webszolgáltatás egy rendszerkép, ebben az esetben egy Docker-rendszerkép, amely magában foglalja a pontozási logikát, illetve magát a modellt is. 

Az oktatóanyag ezen részében használja az Azure Machine Learning szolgáltatásban:

> [!div class="checklist"]
> * A tesztkörnyezet beállítása
> * A modell lekérése a munkaterületről
> * A modell helyi tesztelése
> * A modell üzembe helyezése a Container Instances szolgáltatásban
> * Az üzembe helyezett modell tesztelése

Container Instances szolgáltatásban nem éles környezetekben üzemelő példányok ideális, de ez ideális megoldás a teszteléshez és a munkafolyamat ismertetése. Méretezhető éles környezetekben üzemelő példányok fontolja meg az Azure Kubernetes Service. További információkért lásd: [üzembe helyezése és hol](how-to-deploy-and-where.md).

## <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ez az oktatóanyag [Jupyter-notebookként](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part2-deploy.ipynb) is elérhető. Futtassa a `tutorials/img-classification-part2-deploy.ipynb` jegyzetfüzet, vagy az Azure-jegyzetfüzetek vagy a saját Jupyter notebook server.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

>[!NOTE]
> Ebben a cikkben kód az Azure Machine Learning SDK-val 1.0.2-es verzióját lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő jegyzetfüzet a modell betanítása: [#1. oktatóanyag: Egy rendszerkép osztályozási modell Azure Machine Learning szolgáltatással betanításához](tutorial-train-models-with-aml.md).  


## <a name="set-up-the-environment"></a>A környezet beállítása

Első lépésként állítsa be a tesztelési környezetet.

### <a name="import-packages"></a>Csomagok importálása

Importálja az oktatóanyaghoz szükséges Python-csomagokat.

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

Az előző oktatóanyagban regisztrált egy modellt a munkaterületen. Most töltse be a munkaterületet, és töltse le a modellt a helyi könyvtárba.


```python
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')
model.download(target_dir = '.')
import os 
# verify the downloaded model file
os.stat('./sklearn_mnist_model.pkl')
```

## <a name="test-model-locally"></a>A modell helyi tesztelése

Telepítés megkezdése előtt a következők elvégzésével győződjön meg arról, hogy a modell helyileg fut:
* Tesztadatok betöltése
* A tesztadatok előrejelzése
* A keveredési mátrix vizsgálata

### <a name="load-test-data"></a>Tesztadatok betöltése

A tesztadatokat az oktatóanyag követése során létrehozott **./data/** mappából töltheti be.

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

```

### <a name="predict-test-data"></a>A tesztadatok előrejelzése

Az előrejelzés elkészítéséhez töltse be a tesztadatkészletet a modellbe.

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>A keveredési mátrix vizsgálata

Hozzon létre egy keveredési mátrixot, és ellenőrizze, hogy a tesztkészletből hány minta kapott helyes besorolást. Figyelje meg, hogy a megfelelő előrejelzéseket misclassified értékét. 

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
   

Használja a `matplotlib` kódtárat a keveredési mátrix grafikonként való megjelenítéséhez. Ezen a grafikonon az X tengely képviseli a tényleges értékeket, az Y tengely pedig az előre jelzett értékeket. Az egyes rácsok színe a hibaarányt jelöli. Minél világosabb a szín, annál magasabb a hibaarány. Például sok 5 misclassified vannak, a 3. Ezért egy Világos rács (5,3), láthatja.

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
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

![Diagram megjelenítése keveredési mátrix](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-web-service"></a>Üzembe helyezés webszolgáltatásként

Miután tesztelte a modell, és elégedett az eredményeket, a modellt üzembe helyezzük webszolgáltatásként, amely a Container Instances szolgáltatásban üzemeltetett. 

Container Instances szolgáltatásban hozhat létre a megfelelő környezettel, a következőket biztosítják:
* Pontozószkript a modell használatának bemutatásához
* A telepítendő csomagokat bemutató környezeti fájl
* Egy konfigurációs fájlt a tárolópéldány létrehozása
* Az imént betanított modell

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Pontozószkript létrehozása

Hozzon létre a pontozó szkript, score.py nevezik. A webszolgáltatás-hívások használ, ez a modell használatának megjelenítéséhez.

A pontozószkriptnek két függvényt kell tartalmaznia:
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
    # retreive the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Környezeti fájl létrehozása

A következő lépésben hozzon létre egy myenv.yml nevű környezeti fájlt a szkript csomagfüggőségeinek meghatározásához. Ezzel a fájllal biztosítható, hogy az összes szükséges függőség telepítve legyen a Docker-rendszerképben. A jelen modell esetében ez a `scikit-learn` és az `azureml-sdk`.

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Tekintse át a `myenv.yml` fájl tartalmát.

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-configuration-file"></a>Konfigurációs fájl létrehozása

Hozzon létre egy központi telepítési konfigurációs fájlt, és adja meg a processzorok számának és GB RAM szükséges a Container Instances-tároló. Bár ez a modelltől is függ, az alapértelmezettként megadott 1 mag és 1 gigabyte RAM számos modell használatához elegendő. Ha később többre lenne szüksége, akkor újra létre kell hoznia a rendszerképet, és újra üzembe kell helyeznie a szolgáltatást.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>A Container Instances üzembe helyezése
A feladat elvégzéséhez szükséges várható időtartam **7-8 perc**.

Konfigurálja, majd helyezze üzembe a rendszerképet. Az alábbi kód a következő lépéseket hajtja végre:

1. Állítson össze egy rendszerképet használatával:
   * A pontozófájl (`score.py`).
   * A környezet fájlt (`myenv.yml`).
   * A modellfájl.
1. A rendszerkép regisztrálása a munkaterületen. 
1. A kép küldése a Container Instances-tároló.
1. A Container Instances-tároló indítása a lemezkép használatával.
1. A webszolgáltatás HTTP-végpontjának lekérése.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Kérje le a pontozási webszolgáltatás REST-ügyfélhívásokat fogadó HTTP-végpontját. Ez a végpont megoszthatja bárki tesztelheti a webszolgáltatás vagy integrálnia kell az alkalmazás által. 

```python
print(service.scoring_uri)
```


## <a name="test-deployed-service"></a>Üzembe helyezett szolgáltatás tesztelése

Korábban, a modell helyi verzióját az összes teszt adatai pontozunk. Most egy, a tesztadatok közül 30 véletlenszerű rendszerképet tartalmazó mintával tesztelheti az üzembe helyezett modellt.  

Az alábbi kód a következő lépéseket hajtja végre:
1. Az adatok egy JSON-fájlként a web Service, Container Instances szolgáltatásban lévő üzemeltetett tömböt küld. 

1. A szolgáltatás meghívása az SDK `run` API-jának használatával. Nyers hívás végezhet egy HTTP-eszközt, például a curl használatával is.

1. A visszaadott előrejelzéseket nyomtatása, és a bemeneti képekhez együtt jeleníti meg őket. A piros betűk és az inverz (fekete alapon fehér) képek kiemelik a tévesen besorolt mintákat. 

 Mivel a modell pontossága magas, előfordulhat, hogy a tévesen besorolt minta megtekintése előtt néhány alkalommal futtatnia kell a következő kódot.

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
result = json.loads(service.run(input_data=test_samples))

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Íme egy véletlenszerűen vett minta teszt lemezképek eredménye: ![Eredmények ábrája](./media/tutorial-deploy-models-with-aml/results.png)

A webszolgáltatás teszteléséhez nyers HTTP-kérést is küldhet.

```python
import requests
import json

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

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

Az erőforráscsoportot és a munkaterület más oktatóanyagokat és feltárási megtartásához törölheti csak a Container Instances üzembe helyezési az API-hívás használatával:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>További lépések

+ További információ az összes, a [központi telepítési beállítások az Azure Machine Learning szolgáltatás](how-to-deploy-and-where.md), többek között az ACI, Azure Kubernetes Service-ben, FPGA-kban vagy az IoT Edge.

+ Bemutatjuk, hogyan Azure Machine Learning szolgáltatás is automatikus kiválasztás és a legjobb algoritmus a modell finomhangolása, és az Ön számára, hogy a modell létrehozása. Próbálja ki a [automatikus algoritmus kiválasztása](tutorial-auto-train-models.md) oktatóanyag. 