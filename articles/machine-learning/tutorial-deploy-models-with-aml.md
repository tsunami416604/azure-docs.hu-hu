---
title: 'Képbesorolási oktatóanyag: Modellek telepítése'
titleSuffix: Azure Machine Learning
description: Ez az oktatóanyag, egy kétrészes sorozat második, bemutatja, hogyan azure Machine Learning használatával üzembe helyezhet egy scikit-learn egy Python Jupyter notebook egy lemezkép-besorolási modell üzembe helyezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 81e02492f7e79b87e1513a910afe4719908adbbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159074"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Oktatóanyag: Lemezrendszer-besorolási modell üzembe helyezése az Azure Container-példányokban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az [előző oktatóanyagban](tutorial-train-models-with-aml.md) gépi tanulási modelleket tanított be, majd regisztrált egy modellt a felhőbeli munkaterületen.  Most már készen áll a modell webszolgáltatásként való üzembe helyezésére. A webszolgáltatás egy rendszerkép, ebben az esetben egy Docker-rendszerkép. Magában foglalja a pontozási logikát és magát a modellt. 

Az oktatóanyag ezen részében az Azure Machine Learninget használja a következő feladatokhoz:

> [!div class="checklist"]
> * Állítsa be a tesztelési környezetet.
> * A modell beolvasása a munkaterületről.
> * Tesztelje a modellt helyileg.
> * Telepítse a modellt a tárolópéldányok.
> * Tesztelje az üzembe helyezett modellt.

A Tárolópéldányok nagyszerű megoldás a munkafolyamat tesztelésére és megértésére. Skálázható termelési környezetek esetén fontolja meg az Azure Kubernetes Service használatát. További információt a [telepítés módjában és helyén](how-to-deploy-and-where.md)talál.

>[!NOTE]
> Ebben a cikkben az Azure Machine Learning SDK 1.0.41-es verziójával teszteltük a kódot.

## <a name="prerequisites"></a>Előfeltételek

A jegyzetfüzet futtatásához először fejezze be a modellbetanítást az [oktatóanyagban (1. rész): Képbesorolási modell betanítása.](tutorial-train-models-with-aml.md)   Ezután nyissa meg az *img-classification-part2-deploy.ipynb* notebookot a klónozott *oktatóanyagokban/image-classification-mnist-data* mappában.

Ez az oktatóanyag a [GitHubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetben](how-to-configure-environment.md#local)szeretné használni.  Győződjön meg `matplotlib` arról, hogy telepítve van, és `scikit-learn` a környezetben. 

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint a jegyzetfüzetben.  
>
> Váltson most a Jupyter-jegyzetfüzetre, ha a kód futtatásakor szeretne olvasni.
> Ha egy jegyzetfüzetben egyetlen kódcellát szeretne futtatni, kattintson a kódcellára, és nyomja meg a **Shift+Enter billentyűkombinációt.** Vagy futtassa a teljes jegyzetfüzetet a Felső eszköztár **Összes futtatása** parancsával.

## <a name="set-up-the-environment"></a><a name="start"></a>A környezet beállítása

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

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>A modell beolvasása

Az előző oktatóanyagban regisztrált egy modellt a munkaterületen. Most töltse be ezt a munkaterületet, és töltse le a modellt a helyi könyvtárba:


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

A telepítés előtt győződjön meg arról, hogy a modell helyileg működik:
* Vizsgálati adatok betöltése.
* A tesztadatok előrejelzése.
* Vizsgálja meg a zűrzavarmátrixot.

### <a name="load-test-data"></a>Tesztadatok betöltése

Töltse be a tesztadatokat a betanítási oktatóanyag során létrehozott **./data/** könyvtárból:

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

Az előrejelzések becsatornázására a tesztadatkészletet a modellbe:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>A keveredési mátrix vizsgálata

Hozzon létre egy keveredési mátrixot, és ellenőrizze, hogy a tesztkészletből hány minta kapott helyes besorolást. Figyelje meg a helytelen előrejelzések helytelen értékét: 

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
   

Használja a `matplotlib` kódtárat a keveredési mátrix grafikonként való megjelenítéséhez. Ezen a grafikonon az x tengely a tényleges értékeket, az y tengely pedig az előre jelzett értékeket jeleníti meg. Az egyes rácsok színe a hibaarányt mutatja. Minél világosabb a szín, annál magasabb a hibaarány. Például, sok 5-ös tévesen 3-as. Tehát látsz egy fényes rács (5,3):

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

![Összetévesztési mátrixot megjelenítő diagram](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Üzembe helyezés webszolgáltatásként

Miután tesztelte a modellt, és elégedett az eredménnyel, telepítse a modellt a tárolópéldányokban üzemeltetett webszolgáltatásként. 

A tárolópéldányok megfelelő környezetének létrehozásához adja meg a következő összetevőket:
* A modell használatát bemutató pontozási parancsfájl.
* Környezeti fájl, amely megmutatja, hogy milyen csomagokat kell telepíteni.
* Konfigurációs fájl a tárolópéldány létrehozásához.
* A korábban betanított modell.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Pontozószkript létrehozása

A pontozási parancsfájl létrehozása **score.py**. A webszolgáltatás-hívás ezt a parancsfájlt használja a modell használatának bemutatására.

A következő két szükséges függvény felvétele a pontozási parancsfájlba:
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
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
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

Ezután hozzon létre egy **myenv.yml**nevű környezeti fájlt, amely megadja a parancsfájl összes csomagfüggőségét. Ez a fájl annak érdekében, hogy az összes függőség telepítve van a Docker-lemezkép. A jelen modell esetében ez a `scikit-learn` és az `azureml-sdk`. Minden egyéni környezeti fájlnak listáznia kell az azureml-defaults-t a verion >= 1.0.45 pip-függőséggel. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")
myenv.add_pip_package("azureml-defaults")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Tekintse át a `myenv.yml` fájl tartalmát:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Konfigurációs fájl létrehozása

Hozzon létre egy központi telepítési konfigurációs fájlt. Adja meg a tárolópéldányok tárolóhoz szükséges processzorok és gigabájt RAM számát. Bár ez a modelltől függ, az egyik mag és az 1 gigabájt RAM alapértelmezett értéke sok modell számára elegendő. Ha később többre van szüksége, újra létre kell hoznia a lemezképet, és újra kell telepítenie a szolgáltatást.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Üzembe helyezés tárolópéldányokban
A telepítés befejezéséhez szükséges becsült idő **körülbelül hét-nyolc perc.**

Konfigurálja, majd helyezze üzembe a rendszerképet. Az alábbi kód a következő lépéseket hajtja végre:

1. Kép létrehozása a következő fájlokkal:
   * A pontozási fájl, `score.py`.
   * A környezeti `myenv.yml`fájl, .
   * A modellfájl.
1. Regisztrálja a képet a munkaterület alatt. 
1. Küldje el a lemezképet a tárolópéldányok tárolóba.
1. A rendszerkép használatával indítsa el a tárolót a tárolópéldányokban.
1. A webszolgáltatás HTTP-végpontjának lekérése.

Kérjük, vegye figyelembe, hogy ha saját környezeti fájlt ad meg, az azureml-defaults-t pip-függőségként kell felsorolnia >= 1.0.45 verziójú verzióval. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Kérje le a pontozási webszolgáltatás REST-ügyfélhívásokat fogadó HTTP-végpontját. Ezt a végpontot megoszthatja bárkivel, aki tesztelni szeretné a webszolgáltatást, vagy integrálni szeretné egy alkalmazásba: 

```python
print(service.scoring_uri)
```

## <a name="test-the-deployed-service"></a>Az üzembe helyezett szolgáltatás tesztelése

Korábban az összes tesztadatot a modell helyi verziójával szerezte meg. Most tesztelheti az üzembe helyezett modellegy véletlenszerű minta 30 képa a vizsgálati adatokból.  

Az alábbi kód a következő lépéseket hajtja végre:
1. Az adatok küldése JSON-tömbként a tárolópéldányokban üzemeltetett webszolgáltatásnak. 

1. A szolgáltatás meghívása az SDK `run` API-jának használatával. Nyers hívásokat bármely HTTP-eszközzel, például a curl segítségével is **kezdeményezhet.**

1. A kapott előrejelzések kinyomtatása és a bemeneti képekkel való ábrázolása. A piros betűtípus és az inverz kép , fehér a fekete, a helytelenül osztályozott minták kiemelésére szolgál. 

Mivel a modell pontossága magas, előfordulhat, hogy néhányszor futtatnia kell a következő kódot, mielőtt egy tévesen minősített mintát láthatna:

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

Ez az eredmény egy véletlenszerű vizsgálati képmintából származik:

![Eredményeket megjelenítő ábra](./media/tutorial-deploy-models-with-aml/results.png)

Nyers HTTP-kérést is küldhet a webszolgáltatás teszteléséhez:

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

Ha meg szeretné tartani az erőforráscsoportot és a munkaterületet más oktatóanyagokhoz és feltáráshoz, csak a tárolópéldányok központi telepítését törölheti ezzel az API-hívással:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>További lépések

+ Ismerje meg az [Azure Machine Learning összes telepítési beállítását.](how-to-deploy-and-where.md)
+ További információ [arról, hogyan hozhat létre ügyfeleket a webszolgáltatáshoz.](how-to-consume-web-service.md)
+  [Előrejelzéseket készíthet nagy mennyiségű adatról](how-to-use-parallel-run-step.md) aszinkron módon.
+ Az [Application Insights](how-to-enable-app-insights.md)segítségével figyelheti Az Azure Machine Learning-modelleket.
+ Próbálja ki az [automatikus algoritmusválasztó oktatóanyagot.](tutorial-auto-train-models.md) 
