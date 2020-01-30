---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845143"
---
A `inferenceconfig.json` dokumentum bejegyzései a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály paramétereit képezik le. A következő táblázat ismerteti a JSON-dokumentum entitásai közötti leképezést, valamint a metódus paramétereit:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A rendszerképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `runtime` | `runtime` | Választható. A rendszerképhez használandó futtatókörnyezet. A támogatott futtatókörnyezetek `spark-py` és `python`. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést. |
| `condaFile` | `conda_file` | Választható. Egy helyi fájl elérési útja, amely a képhez használandó Conda-környezeti definíciót tartalmaz.  Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Választható. A rendszerkép beállításakor futtatandó további Docker-lépéseket tartalmazó helyi fájl elérési útja.  Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést.|
| `sourceDirectory` | `source_directory` | Választható. Az összes fájlt tartalmazó mappák elérési útja a rendszerkép létrehozásához, amely megkönnyíti a mappában vagy almappában található fájlok elérését. A webszolgáltatások függőségeiként feltölthet egy teljes mappát a helyi gépről. Megjegyzés: a entry_script, a conda_file és a extra_docker_file_steps útvonalak relatív elérési utak az source_directory elérési úthoz. |
| `enableGpu` | `enable_gpu` | Választható. Azt határozza meg, hogy a GPU-támogatás engedélyezhető-e a rendszerképben. A GPU-képet olyan Azure-szolgáltatáson kell használni, mint például a Azure Container Instances. Például Azure Machine Learning a számítás, az Azure Virtual Machines és az Azure Kubernetes szolgáltatás. Az alapértelmezett érték a false. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést.|
| `baseImage` | `base_image` | Választható. Alaprendszerképként használandó egyéni rendszerkép Ha nincs megadva alaprendszerkép, a rendszerkép a megadott futásidejű paraméter alapján fog alapulni. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést. |
| `baseImageRegistry` | `base_image_registry` | Választható. Az alapképet tartalmazó rendszerkép-beállításjegyzék. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést.|
| `cudaVersion` | `cuda_version` | Választható. A CUDA verziója a GPU-támogatást igénylő lemezképek telepítéséhez. A GPU-rendszerképet egy Azure-szolgáltatáson kell használni. Például Azure Container Instances, Azure Machine Learning számítás, Azure Virtual Machines és Azure Kubernetes szolgáltatás. A támogatott verziók a következők: 9,0, 9,1 és 10,0. Ha a `enable_gpu` be van állítva, az alapértelmezett érték a 9,1. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést. |
| `description` | `description` | A rendszerkép leírása. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja ezt a bejegyzést.  |
| `environment` | `environment` | Választható.  Azure Machine Learning [környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

A következő JSON egy példa a parancssori felület használatára:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

Azure Machine Learning [környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) teljes specifikációit a következtetési konfigurációs fájlban is megadhatja. Ha ez a környezet nem létezik a munkaterületen, akkor a Azure Machine Learning létrehozza azt. Ellenkező esetben a Azure Machine Learning szükség esetén frissíti a környezetet. A következő JSON egy példa:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Meglévő Azure Machine Learning- [környezetet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) is HASZNÁLHAT külön CLI-paraméterekben, és eltávolíthatja a "környezet" kulcsot a következtetési konfigurációs fájlból. Használja az-e nevet a környezet neveként, a--EV pedig a környezet verzióját. Ha nem ad meg--EV-t, a rendszer a legújabb verziót fogja használni. Íme egy példa a következtetési konfigurációs fájlra:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt az előző következtetési konfigurációs fájl (myInferenceConfig. JSON) használatával. 

Egy meglévő Azure Machine Learning- [környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) legújabb verzióját is használja (AzureML-minimal néven).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
