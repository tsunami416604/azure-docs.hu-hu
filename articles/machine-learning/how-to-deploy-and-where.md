---
title: Modellek üzembe helyezésének módja és helye
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan és hol helyezheti üzembe a Azure Machine Learning modelleket, beleértve a Azure Container Instances, az Azure Kubernetes Service, a Azure IoT Edge és a Field-programozható Gate-tömböket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d7502414f6476cafcc85bbefd28a4ec463f62099
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751687"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellek üzembe helyezése az Azure Machine Learninggel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan helyezheti üzembe a gépi tanulási modellt webszolgáltatásként az Azure-felhőben, vagy Azure IoT Edge eszközöket.

A munkafolyamat a modell telepítésének helyétől függetlenül hasonló:

1. A modell regisztrálása.
1. Következtetési konfiguráció előkészítése
1. Beléptetési parancsfájl előkészítése (kivéve, ha [kód nélküli telepítést](how-to-deploy-no-code-deployment.md)használ)
1. A modell üzembe helyezése a számítási célon.
1. Tesztelje az üzembe helyezett modellt, más néven webszolgáltatást.

Az üzembe helyezési munkafolyamatban részt vevő fogalmakkal kapcsolatos további információkért lásd: [modellek kezelése, üzembe helyezése és figyelése Azure Machine Learningokkal](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Előfeltételek

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).
- Egy modell. Ha nem rendelkezik betanított modellel, az [oktatóanyagban](https://aka.ms/azml-deploy-cloud)szereplő modell-és függőségi fájlokat is használhatja.
- Az [Azure parancssori felület (CLI) bővítmény a Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).
- Egy modell. Ha nem rendelkezik betanított modellel, az [oktatóanyagban](https://aka.ms/azml-deploy-cloud)szereplő modell-és függőségi fájlokat is használhatja.
- A [Pythonhoz készült Azure Machine learning szoftverfejlesztői készlet (SDK)](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Az [előfizetési környezet beállításához](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)kövesse az Azure CLI dokumentációjának utasításait.

Ezután tegye a következőket:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

lehetőségre kattintva megtekintheti a munkaterületeket.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Az SDK munkaterülethez való kapcsolódásával kapcsolatos további információkért tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) dokumentációját.


---


## <a name="register-your-model"></a><a id="registermodel"></a> A modell regisztrálása

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a munkaterületen. A fájlok regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

> [!TIP]
> Modell regisztrálása esetén megadja a Felhőbeli hely (betanítási Futtatás) vagy egy helyi könyvtár elérési útját. Ez az elérési út csak a feltöltéshez szükséges fájlok megkeresése a regisztrációs folyamat részeként. Nem kell megegyeznie a bejegyzési parancsfájlban használt elérési úttal. További információ: [a modell fájljainak megkeresése a belépési parancsfájlban](how-to-deploy-advanced-entry-script.md#load-registered-models).

A gépi tanulási modellek regisztrálva vannak a Azure Machine Learning munkaterületen. A modell Azure Machine Learning vagy máshonnan is származhat. Modell regisztrálása esetén a modellre vonatkozó metaadatokat is megadhat. A `tags` modell-regisztrációra alkalmazott és a szótárak használatával szűrheti a modelleket `properties` .

Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Modell regisztrálása egy Azure ML betanítási futtatásból

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

A `--asset-path` paraméter a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `--asset-path` a fájlokat tartalmazó mappa elérési útjára.

### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `-p` a fájlokat tartalmazó mappa elérési útjára.

A szolgáltatással kapcsolatos további információkért `az ml model register` olvassa el a [dokumentációt](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a munkaterületen. A fájlok regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

> [!TIP]
> Modell regisztrálása esetén megadja a Felhőbeli hely (betanítási Futtatás) vagy egy helyi könyvtár elérési útját. Ez az elérési út csak a feltöltéshez szükséges fájlok megkeresése a regisztrációs folyamat részeként. Nem kell megegyeznie a bejegyzési parancsfájlban használt elérési úttal. További információ: [a modell fájljainak megkeresése a belépési parancsfájlban](how-to-deploy-advanced-entry-script.md#load-registered-models).

A gépi tanulási modellek regisztrálva vannak a Azure Machine Learning munkaterületen. A modell Azure Machine Learning vagy máshonnan is származhat. Modell regisztrálása esetén a modellre vonatkozó metaadatokat is megadhat. A `tags` modell-regisztrációra alkalmazott és a szótárak használatával szűrheti a modelleket `properties` .

Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Modell regisztrálása egy Azure ML betanítási futtatásból

  Ha az SDK-t használja a modell betanításához, akkor a modell kiképzésének módjától függően [futtathat egy futtatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) objektumot vagy egy [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) -objektumot. Minden objektum használható a kísérlet futtatásával létrehozott modellek regisztrálására.

  + Modell regisztrálása egy `azureml.core.Run` objektumból:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    A `model_path` paraméter a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `model_path` a fájlokat tartalmazó mappa elérési útjára. További információ: [Run. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) dokumentáció.

  + Modell regisztrálása egy `azureml.train.automl.run.AutoMLRun` objektumból:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Ebben a példában a `metric` és a `iteration` paraméterek nincsenek megadva, ezért a rendszer a legjobb elsődleges metrikával rendelkező iterációt regisztrálja. A rendszer a `model_id` futtatásból visszaadott értéket használja a modell neve helyett.

    További információ: [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) dokumentáció.


### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

A modell helyi elérési útjának megadásával regisztrálhat egy modellt. Megadhatja egy mappa vagy egy fájl elérési útját. Ezzel a módszerrel regisztrálhatja Azure Machine Learning, majd letöltheti a betanított modelleket. Ezt a módszert használhatja a Azure Machine Learningon kívül betanított modellek regisztrálására is.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Az SDK és a ONNX használata**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `model_path` a fájlokat tartalmazó mappa elérési útjára.

További információkért tekintse meg a [modell osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)dokumentációját.

A Azure Machine Learningon kívül betanított modellek használatáról a következő témakörben talál további információt: [meglévő modell üzembe helyezése](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Bejegyzési parancsfájl definiálása

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Következtetési konfiguráció definiálása

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt a parancssori felület használatával:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő beállításokat adja meg:

* A modellhez Python szükséges
* A [beléptetési parancsfájl](#define-an-entry-script), amely a központilag telepített szolgáltatásnak küldött webes kérelmek kezelésére szolgál.
* A következtetéshez szükséges Python-csomagokat ismertető Conda-fájl

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).

# <a name="python"></a>[Python](#tab/python)

A következtetések konfigurálásával megtudhatja, hogyan állíthatja be a modellt tartalmazó webes szolgáltatást. Ezt később, a modell telepítésekor használják.

A következtetések konfigurálásával Azure Machine Learning környezetek határozzák meg az üzemelő példányhoz szükséges szoftver-függőségeket. A környezetek lehetővé teszik a képzéshez és a telepítéshez szükséges szoftver-függőségek létrehozását, kezelését és újrafelhasználását. Létrehozhat egy környezetet egyéni függőségi fájlokból, vagy használhatja a kurátori Azure Machine Learning környezetek egyikét. Az alábbi YAML egy Conda-függőségi fájlra mutat példát. Vegye figyelembe, hogy a azureml alapértelmezett értékeit kell megadnia a (z) >= 1.0.45 lépésszám-függőségként, mert tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. Ha automatikus séma-generálást szeretne használni, a belépési parancsfájlnak importálnia kell a `inference-schema` csomagokat is.

```YAML

name: project_environment
dependencies:
- python=3.6.2
- scikit-learn=0.22.1
- pip:
 # You must list azureml-defaults as a pip dependency
 - azureml-defaults>=1.0.45
 - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Ha a függőség a Conda és a PIP (a PyPi) szolgáltatáson keresztül érhető el, a Microsoft a Conda-verzió használatát javasolja, mivel a Conda-csomagok jellemzően olyan előre elkészített bináris fájlokkal rendelkeznek, amelyek megbízhatóbb telepítést tesznek elérhetővé.
>
> További információ: a [Conda és a pip ismertetése](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Ha szeretné megnézni, hogy a függőség elérhető-e a Conda-on keresztül, használja a `conda search <package-name>` parancsot, vagy használja a csomag indexeit a és a értéknél [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

A függőségek fájl használatával létrehozhat egy környezeti objektumot, és mentheti a munkaterületre későbbi használatra:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

A Python-környezetek Azure Machine Learning használatával történő használatának és testreszabásának részletes ismertetését a következő témakörben tekintheti meg: [&-környezetek használata a Azure Machine Learningban](how-to-use-environments.md).

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).


Az alábbi példa bemutatja egy környezet betöltését a munkaterületről, majd azt a következtetési konfigurációval használva:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md).

További információ a konfigurációval kapcsolatban: [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) Class dokumentáció.

---

## <a name="choose-a-compute-target"></a>Számítási cél kiválasztása

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Telepítési konfiguráció definiálása

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

A központi telepítési konfigurációhoz elérhető lehetőségek a kiválasztott számítási céltól függően eltérőek.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

További információ: az [ml Model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) dokumentáció.

# <a name="python"></a>[Python](#tab/python)

A modell üzembe helyezése előtt meg kell határoznia a telepítési konfigurációt. *A központi telepítési konfiguráció a webszolgáltatást futtató számítási célra vonatkozik.* Ha például helyileg helyez üzembe egy modellt, meg kell adnia azt a portot, ahol a szolgáltatás fogadja a kérelmeket. A telepítési konfiguráció nem része a belépési parancsfájlnak. A rendszer a modell és a beléptetési parancsfájlt futtató számítási cél jellemzőinek meghatározására szolgál.

Előfordulhat, hogy a számítási erőforrást is létre kell hoznia, ha például még nem rendelkezik a munkaterülethez társított Azure Kubernetes Service-(ak-) példánnyal.

Az alábbi táblázat az egyes számítási célkitűzések központi telepítési konfigurációjának létrehozásához nyújt példát:

| Számítási cél | Üzembe helyezési konfiguráció – példa |
| ----- | ----- |
| Helyi | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

A helyi, Azure Container Instances és AK webszolgáltatásokhoz tartozó osztályok a következő helyekről importálhatók `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---



## <a name="deploy-your-model"></a>A modell üzembe helyezése

Most már készen áll a modell üzembe helyezésére. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Regisztrált modell használata

Ha regisztrálta a modellt a Azure Machine Learning munkaterületen, cserélje le a "mymodel: 1" kifejezést a modell nevével és annak verziószámával.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Helyi modell használata

Ha nem szeretné regisztrálni a modellt, akkor a inferenceconfig.js"sourceDirectory" paraméterének megadásával megadhat egy helyi könyvtárat a modell kiszolgálásához.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy helyi telepítést mutat be. A szintaxis az előző lépésben kiválasztott számítási céltól függően változhat.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információkért tekintse meg a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)és a [webszolgáltatások](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)dokumentációját.

---

### <a name="understanding-service-state"></a>A szolgáltatás állapotának ismertetése

A modell telepítése során előfordulhat, hogy a szolgáltatás állapota megváltozik a teljes üzembe helyezése során.

Az alábbi táblázat a különböző szolgáltatás-állapotokat ismerteti:

| Webszolgáltatás állapota | Leírás | Végső állapot?
| ----- | ----- | ----- |
| Transitioning | A szolgáltatás üzembe helyezési folyamatban van. | Nem |
| Nem kifogástalan | A szolgáltatás telepítve van, de jelenleg nem érhető el.  | Nem |
| Unschedulable | A szolgáltatás jelenleg nem telepíthető az erőforrások hiánya miatt. | Nem |
| Sikertelen | Hiba vagy összeomlás miatt nem sikerült telepíteni a szolgáltatást. | Igen |
| Kifogástalan | A szolgáltatás kifogástalan állapotban van, és a végpont elérhető. | Igen |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batch-következtetés
A Azure Machine Learning számítási célokat Azure Machine Learning hozza létre és kezeli. A kötegelt előrejelzésekhez Azure Machine Learning folyamatokból is felhasználhatók.

A Batch-következtetések Azure Machine Learning számítási feladatokkal való áttekintését lásd: [a Batch-előrejelzések futtatása](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge következtetés
A peremhálózati üzembe helyezésének támogatása előzetes verzióban érhető el. További információ: [Azure Machine learning telepítése IoT Edge modulként](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="delete-resources"></a>Erőforrások törlése

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Telepített webszolgáltatás törléséhez használja a következőt: `az ml service <name of webservice>` .

Ha törölni szeretne egy regisztrált modellt a munkaterületről, használja a következőt: `az ml model delete <model id>`

További információ a [webszolgáltatások törléséről](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) és [a modellek törléséről](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Központilag telepített webszolgáltatás törléséhez használja a következőt: `service.delete()` .
Regisztrált modell törléséhez használja a következőt: `model.delete()` .

További információ: a [webszolgáltatások dokumentációja. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) és [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

---


## <a name="next-steps"></a>További lépések

* [Sikertelen üzembe helyezés hibáinak megoldása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Esemény-riasztások és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)

