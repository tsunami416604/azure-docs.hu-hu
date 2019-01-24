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
ms.openlocfilehash: cf83c99f562029fae932bc294cfc87750c908c99
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828213"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Oktatóanyag: Egy rendszerkép besorolási modell az Azure Container Instances szolgáltatásban üzembe helyezése

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az [előző oktatóanyagban](tutorial-train-models-with-aml.md) gépi tanulási modelleket tanított be, majd regisztrált egy modellt a felhőbeli munkaterületen.  

Most már készen áll a modellt üzembe helyezzük webszolgáltatásként, amely a [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). Webszolgáltatás egy kép, ebben az esetben egy Docker-rendszerképet. Magába foglalja a pontozási logika és modellelemek a modellben. 

Az oktatóanyag ezen részében használja az Azure Machine Learning szolgáltatás a következő feladatokhoz:

> [!div class="checklist"]
> * A tesztelési környezet kialakítása.
> * A modell lekérdezni a munkaterülethez.
> * A modell helyileg történő teszteléséhez.
> * A modell üzembe helyezése a Container Instances szolgáltatásban.
> * Az üzembe helyezett modell teszteléséhez.

Container Instances szolgáltatásban nem éles környezetekben üzemelő példányok ideális, de ez ideális megoldás a teszteléshez és a munkafolyamat ismertetése. Méretezhető éles környezetekben üzemelő példányok fontolja meg az Azure Kubernetes Service. További információkért lásd: [üzembe helyezése és hol](how-to-deploy-and-where.md).

## <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ez az oktatóanyag [Jupyter-notebookként](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part2-deploy.ipynb) is elérhető. Futtassa a *oktatóanyagok/img – osztályozás – part2-deploy.ipynb* notebook vagy [Azure notebookok](https://notebooks.azure.com/) vagy a saját Jupyter notebook server.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

>[!NOTE]
> Ebben a cikkben kód az Azure Machine Learning SDK-val 1.0.2-es verzióját lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő jegyzetfüzet a modell betanítása: [Oktatóanyag: (1. rész): Egy rendszerkép osztályozási modell Azure Machine Learning szolgáltatással betanításához](tutorial-train-models-with-aml.md).  


## <a name="set-up-the-environment"></a>A környezet beállítása

Első lépésként állítsa be a tesztelési környezetet.

### <a name="import-packages"></a>Csomagok importálása

Importálja az ehhez az oktatóanyaghoz szükséges Python-csomagokat:

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

Az előző oktatóanyagban regisztrált egy modellt a munkaterületen. Most már betölteni a munkaterületet, és a modell letöltése a helyi könyvtárba:


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

## <a name="test-the-model-locally"></a>A modell helyi tesztelése

Mielőtt telepítene, győződjön meg arról, a modell helyileg működik:
* Terhelési teszt adatai.
* Teszt adatai előre jelezni.
* Vizsgálja meg a keveredési mátrix.

### <a name="load-test-data"></a>Tesztadatok betöltése

A teszt adatokat letölteni a **. /data/** az oktatóprogram során létrehozott könyvtár:

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

```

### <a name="predict-test-data"></a>A tesztadatok előrejelzése

Előrejelzés lekéréséhez-hírcsatorna a tesztelési adatkészletnél a modell:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>A keveredési mátrix vizsgálata

Hozzon létre egy keveredési mátrixot, és ellenőrizze, hogy a tesztkészletből hány minta kapott helyes besorolást. Figyelje meg, hogy a megfelelő előrejelzéseket misclassified értékét: 

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
   

Használja a `matplotlib` kódtárat a keveredési mátrix grafikonként való megjelenítéséhez. Ezen a gráfon az x tengelyt megjeleníti a tényleges értékek, és az y tengelyen jeleníti meg az előre jelzett értékek. Minden egyes rács színe a hibák aránya jeleníti meg. Minél világosabb a szín, annál magasabb a hibaarány. Például sok 5 misclassified vannak, a 3. Ezért egy Világos rács címen (5,3):

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

## <a name="deploy-as-a-web-service"></a>Webszolgáltatásként üzembe helyezése

Miután, tesztelni a modellt, és elégedett az eredménnyel, a modellt üzembe helyezzük webszolgáltatásként, amely a Container Instances szolgáltatásban üzemeltetett. 

Container Instances a megfelelő környezetet hozhat létre, adja meg az alábbi összetevőket:
* Pontozó szkript bemutatja a modellt használja.
* Egy környezet fájljához csomagokat kell telepíteni.
* Egy konfigurációs fájl a tárolópéldányt hozhat létre.
* A korábban, a betanított modell.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Pontozószkript létrehozása

Hozzon létre a pontozó szkript, nevű **score.py**. A webszolgáltatás-hívások ezt a parancsfájlt használja a modell használatának megjelenítése.

A pontozó szkript ezen két szükséges funkciókat tartalmazza:
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
    return json.dumps(y_hat.tolist())
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Környezeti fájl létrehozása

Ezután hozzon létre egy környezetben nevű fájlt **myenv.yml**, amely meghatározza, hogy az összes a parancsfájl csomagfüggőségek. Ez a fájl segítségével győződjön meg arról, hogy minden ezeket a függőségeket a Docker-rendszerkép telepítve. Ez a modell kell `scikit-learn` és `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Tekintse át a tartalmát a `myenv.yml` fájlt:

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Egy konfigurációs fájl létrehozása

Hozzon létre egy központi telepítési konfigurációs fájlt. Adja meg a processzorok számának és GB RAM szükséges a Container Instances-tároló. Bár ez függ a modellt, a rendszer az alapértelmezett egymagos és 1 GB memória elegendő számos modellt. Ha később több, akkor hozza létre újból a lemezképet, és telepítse újra a szolgáltatást.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>A Container Instances üzembe helyezése
Fejezze be az üzembe helyezés becsült ideje van **körülbelül hét-nyolc percet**.

Konfigurálja, majd helyezze üzembe a rendszerképet. Az alábbi kód a következő lépéseket hajtja végre:

1. Állítson össze egy rendszerképet a fájlok használatával:
   * A pontozófájl `score.py`.
   * A környezet fájl `myenv.yml`.
   * A modellfájl.
1. Regisztrálja a képen a munkaterületen. 
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

Kérje le a pontozási webszolgáltatás REST-ügyfélhívásokat fogadó HTTP-végpontját. Ez a végpont megoszthatja bárki tesztelheti a webszolgáltatás vagy integrálnia kell az alkalmazás által: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Az üzemelő szolgáltatás tesztelése

Korábban, a modell helyi verzióját az összes teszt adatai pontozunk. Most tesztelheti az üzembe helyezett modell egy véletlenszerűen vett minta, a Tesztadatok 30 képeit.  

Az alábbi kód a következő lépéseket hajtja végre:
1. Az adatok egy JSON-fájlként a web Service, Container Instances szolgáltatásban lévő üzemeltetett tömböt küld. 

1. A szolgáltatás meghívása az SDK `run` API-jának használatával. Bármilyen HTTP-eszköz használatával is meghatározhat nyers hívás **curl**.

1. A kapott előrejelzések kinyomtatása és a bemeneti képekkel való ábrázolása. Jelölje ki a misclassified minták piros színnel és más néven inverz kép, fekete, a fehér szolgál. 

Mivel a modell pontosságát magas, előfordulhat, hogy kell futtatnia a következő kódot néhány alkalommal ahhoz, hogy megjelenjenek a misclassified minta:

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

Ez az eredmény egy véletlenszerűen vett minta teszt lemezképek származik:

![Eredmények ábrája](./media/tutorial-deploy-models-with-aml/results.png)

Ezenkívül elküldheti a nyers HTTP-kérést a webszolgáltatás teszteléséhez:

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

+ További információ az összes, a [központi telepítési beállítások az Azure Machine Learning szolgáltatás](how-to-deploy-and-where.md). Options include Azure Container Instances, Azure Kubernetes Service, FPGAs, and Azure IoT Edge.

+ Tekintse meg, hogyan lehet az Azure Machine Learning szolgáltatás a automatikus kiválasztás és a legjobb algoritmus a modell finomhangolása. Emellett a hoz létre ehhez a modellhez. Próbálja ki a [automatikus algoritmus kiválasztása](tutorial-auto-train-models.md) oktatóanyag. 
