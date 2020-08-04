---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 624824f5b6b8f7154ccd7b50da49f3f4bb179bb9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542784"
---
## <a name="prerequisites"></a>Előfeltétel

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](../articles/machine-learning/how-to-manage-workspace.md).
- Egy modell. Ha nem rendelkezik betanított modellel, az [oktatóanyagban](https://aka.ms/azml-deploy-cloud)szereplő modell-és függőségi fájlokat is használhatja.
- A [Pythonhoz készült Azure Machine learning szoftverfejlesztői készlet (SDK)](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Az SDK munkaterülethez való kapcsolódásával kapcsolatos további információkért tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) dokumentációját.

## <a name="register-your-model"></a><a id="registermodel"></a>A modell regisztrálása

A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a munkaterületen. A fájlok regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

> [!TIP]
> Modell regisztrálása esetén megadja a Felhőbeli hely (betanítási Futtatás) vagy egy helyi könyvtár elérési útját. Ez az elérési út csak a feltöltéshez szükséges fájlok megkeresése a regisztrációs folyamat részeként. Nem kell megegyeznie a bejegyzési parancsfájlban használt elérési úttal. További információ: [a modell fájljainak megkeresése a belépési parancsfájlban](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

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

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

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

A Azure Machine Learningon kívül betanított modellek használatáról a következő témakörben talál további információt: [meglévő modell üzembe helyezése](../articles/machine-learning/how-to-deploy-existing-model.md).


## <a name="define-an-entry-script"></a>Bejegyzési parancsfájl definiálása

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Következtetési konfiguráció definiálása

A következtetések konfigurálásával megtudhatja, hogyan állíthatja be a modellt tartalmazó webes szolgáltatást. Ezt később, a modell telepítésekor használják.

A következtetések konfigurálásával Azure Machine Learning környezetek határozzák meg az üzemelő példányhoz szükséges szoftver-függőségeket. A környezetek lehetővé teszik a képzéshez és a telepítéshez szükséges szoftver-függőségek létrehozását, kezelését és újrafelhasználását. Létrehozhat egy környezetet egyéni függőségi fájlokból, vagy használhatja a kurátori Azure Machine Learning környezetek egyikét. Az alábbi YAML egy Conda-függőségi fájlra mutat példát. Vegye figyelembe, hogy a azureml alapértelmezett értékeit kell megadnia a (z) >= 1.0.45 lépésszám-függőségként, mert tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. Ha automatikus séma-generálást szeretne használni, a belépési parancsfájlnak importálnia kell a `inference-schema` csomagokat is.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
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

A Python-környezetek Azure Machine Learning használatával történő használatának és testreszabásának részletes ismertetését a következő témakörben tekintheti meg: [& szoftveres környezetek használata a Azure Machine learning](../articles/machine-learning/how-to-use-environments.md)

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](../articles/machine-learning/how-to-deploy-custom-docker-image.md).


Az alábbi példa bemutatja egy környezet betöltését a munkaterületről, majd azt a következtetési konfigurációval használva:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](../articles/machine-learning/how-to-use-environments.md).

További információ a konfigurációval kapcsolatban: [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) Class dokumentáció.

## <a name="choose-a-compute-target"></a>Számítási cél kiválasztása


[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]



## <a name="define-a-deployment-configuration"></a>Telepítési konfiguráció definiálása

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


## <a name="deploy-your-model"></a>A modell üzembe helyezése

Most már készen áll a modell üzembe helyezésére. Az alábbi példa egy helyi telepítést mutat be. A szintaxis az előző lépésben kiválasztott számítási céltól függően változhat.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információkért tekintse meg a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)és a [webszolgáltatások](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)dokumentációját.


## <a name="delete-resources"></a>Erőforrások törlése

Központilag telepített webszolgáltatás törléséhez használja a következőt: `service.delete()` .
Regisztrált modell törléséhez használja a következőt: `model.delete()` .

További információ: a [webszolgáltatások dokumentációja. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) és [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

