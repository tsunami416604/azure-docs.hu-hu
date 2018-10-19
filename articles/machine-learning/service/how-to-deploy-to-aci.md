---
title: Az Azure Container Instances (ACI) – az Azure Machine Learning webszolgáltatások üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe egy betanított modellt webszolgáltatásként, amely az Azure Container Instances (ACI) Azure Machine Learning szolgáltatással. Ez a cikk bemutatja a modell üzembe helyezése az aci-ban három különböző módon. A kód és a vezérlő elnevezési részei a központi telepítésben található sorok számát különböznek.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 8a736516a598eee051b416834d2b737211e66b96
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429461"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Webszolgáltatások üzembe helyezése az Azure Container Instances szolgáltatásban 

Telepítheti a betanított modellt webszolgáltatásként, amely a [Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (aci) Szolgáltatásban, [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), az IoT él eszközt, vagy [programmable gate arrays (FPGA) mezőbe](concept-accelerate-with-fpgas.md) 

ACI általában az AKS olcsóbb és 4 – 6 sornyi kódot is beállítható. ACI tökéletes lehetőség központi telepítések teszteléséhez. Később, a modellek és webszolgáltatások nagy léptékben méretezhető, éles használatra kész, is [üzembe helyezheti őket az AKS](how-to-deploy-to-aks.md).

Ez a cikk bemutatja a modell üzembe helyezése az aci-ban három különböző módon. A kód és a vezérlő elnevezési részei a központi telepítésben található sorok számát különböznek. A metódus a lehető legkevesebb kódot és a metódus a legtöbb kóddal és vezérlőelem az ACI lehetőségek közül választhat:

* Üzembe helyezés a modell fájl használatával `Webservice.deploy()` 
* Üzembe helyezés a regisztrált modell használatával `Webservice.deploy_from_model()`
* A lemezkép használatával regisztrált modell üzembe helyezése `Webservice.deploy_from_image()`

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [Azure Machine Learning a rövid útmutató – első lépések](quickstart-get-started.md).

- Az Azure Machine Learning szolgáltatás munkaterület objektum

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- Modell üzembe helyezéséhez. Ebben a dokumentumban szereplő példák a végrehajtásával létrehozott modellt használja a "[a modell betanítását](tutorial-train-models-with-aml.md)" oktatóanyag. Ha nem használja ezt a modellt, módosíthatja a lépések a modell neve.  Is kell írnia a saját pontozó szkript futtatásához a modellt.


## <a name="configure-an-image"></a>Kép beállítása

Állítsa be a Docker-rendszerképet, amely a modell-fájlok tárolására szolgál.
1. Hozzon létre egy pontozó szkriptre (score.py) [ezen utasításokat követve](tutorial-deploy-models-with-aml.md#create-scoring-script)

1. Hozzon létre egy környezetben fájlt (myenv.yml) [ezen utasításokat követve](tutorial-deploy-models-with-aml.md#create-environment-file) 

1. Két fájlt használja a Docker-rendszerképet konfigurálhatja a Python SDK használatával a következőképpen:

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Az ACI-tároló konfigurálása

Az ACI-tároló konfigurálása szerint adja meg a processzorok számát és a GB memóriával történő üzembe helyezéshez az ACI-tároló. Az alapértelmezett egymagos és 1 GB memória elég sok modellek esetén. Ha úgy gondolja, hogy szüksége több később hozza létre újra a lemezképet, és telepítse újra a szolgáltatást.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Regisztrálja a modellt

> Hagyja ki ezt az előfeltételt, ha Ön [üzembe helyezése a egy modellfájl](#deploy-from-model-file) (`Webservice.deploy()`).

Regisztrálja a modellt használandó [Webservice.deploy_from_model](#deploy-from-registered-model) vagy [Webservice.deploy_from_image](#deploy-from-image). Vagy ha már rendelkezik egy regisztrált modell lekérni most.

### <a name="retrieve-a-registered-model"></a>A regisztrált modell beolvasása
Ha az Azure Machine Learning segítségével a modell betanítását, a modell előfordulhat, hogy már regisztrálva van a munkaterülethez.  Például az utolsó lépés a [modell létrehozása útmutató betanításához](tutorial-train-models-with-aml.md) regisztrálva a modell.  Majd kérheti le a regisztrált modell üzembe helyezéséhez.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Egy modellfájl regisztrálása

Ha a modell máshol lett létrehozva, továbbra is regisztrálhatja egyszerűen a munkaterületre.  Regisztrálja a modellt, a modellfájl (`sklearn_mnist_model.pkl` ebben a példában) kell lennie az aktuális munkakönyvtárba. Ezután regisztrálni ezt a fájlt nevű modellként `sklearn_mnist` a munkaterület- `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```

<a name='deploy-from-model-file'/>
## <a name="option-1-deploy-from-model-file"></a>1. lehetőség: A modellfájl üzembe helyezése

Modell-fájlból telepíteni kódot ír, de is kínál a lehető legkevesebb összetevők elnevezési szabályozhatja a lehető legkevesebb szükséges. Ez a beállítás egy modellfájl kezdődik, és az Ön számára a munkaterületre regisztrálja azt.  Azonban nem a modell neve, és társítsa a címkék és a egy leírást.  

Ez a beállítás az SDK-metódussal, Webservice.deploy() használja.  

**Becsült időtartam**: üzembe helyezése körülbelül 6 – 7 perc szükséges.

1. Győződjön meg arról, hogy a jelentésmodell fájlját a helyi munkakönyvtárból.

1. Nyissa meg az előfeltételként szükséges modellfájl score.py és módosítása a `init()` szakaszt:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. A modellfájl üzembe.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Mostantól [a webszolgáltatás teszteléséhez](#test-web-service).

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>2. lehetőség: A regisztrált modell üzembe helyezése

Regisztrált modellfájl telepíteni néhány további sornyi kód szükséges, és lehetővé teszi bizonyos kimenetek elnevezésén felett. Ez a beállítás kényelmes módja a már regisztrált modellek üzembe helyezéséhez.  Azonban nem adjon nevet a Docker-rendszerképet.  

Ez a beállítás az SDK-metódussal, Webservice.deploy_from_model() használja.

**Becsült időtartam**: Ezzel a lehetőséggel üzembe helyezése körülbelül 8 perc szükséges.

1. Futtassa a kódot, a Docker-tároló és az ACI-tároló konfigurálását, és adja meg a regisztrált modell.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Mostantól [a webszolgáltatás teszteléséhez](#test-web-service).

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>3. lehetőség: Lemezkép alapján üzembe helyezése

Modell üzembe helyezése regisztrált (`model`) használatával `Webservice.deploy_from_image()`. Ez a módszer lehetővé teszi a Docker-rendszerkép létrehozása külön-külön és üzembe helyezhesse a rendszerképből.

1. Hozhat létre, és regisztrálja a Docker-rendszerkép alatt a munkaterület használata `ContainerImage.create()`

    Ez a módszer segít jobban szabályozhatja a kép egy külön lépésben létrehozásával.  A regisztrált modell (`model`) a rendszerkép tartalmazza.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Becsült időtartam**: körülbelül 3 perc alatt.

1. A szolgáltatás használatával, a Docker-rendszerkép üzembe helyezése `Webservice.deploy_from_image()`

    A kép most üzembe helyezése az aci Szolgáltatásban.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Becsült időtartam**: körülbelül 3 perc alatt.

Ez a módszer létrehozásához és az összetevők a központi telepítésben lévő elnevezési legtöbb felett biztosít.

Most tesztelheti a webszolgáltatást.

<a name='test-web-service'/>
## <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez

A webes szolgáltatás nem ugyanaz, függetlenül attól, amely módszerrel történt.  Előrejelzés lekéréséhez használja a `run` metódus a szolgáltatás.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem a webes szolgáltatás használatához, törlése, így nem kell díjat fizetniük.

```python
service.delete()
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [telepítése az Azure Kubernetes Service](how-to-deploy-to-aks.md) nagyobb méretezési csoport üzembe helyezéséhez. 
