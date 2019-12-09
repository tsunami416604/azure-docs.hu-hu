---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926978"
---
A `inferenceconfig.json` dokumentum bejegyzései a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály paramétereit képezik le. A következő táblázat ismerteti a JSON-dokumentum entitásai közötti leképezést, valamint a metódus paramétereit:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A rendszerképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `runtime` | `runtime` | Választható. A rendszerképhez használandó futtatókörnyezet. A jelenleg támogatott futtatókörnyezetek `spark-py` és `python`. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja. |
| `condaFile` | `conda_file` | Választható. Egy helyi fájl elérési útja, amely a képhez használandó Conda-környezeti definíciót tartalmaz.  Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Választható. A rendszerkép beállításakor futtatandó további Docker-lépéseket tartalmazó helyi fájl elérési útja.  Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja.|
| `sourceDirectory` | `source_directory` | Választható. Az összes fájlt tartalmazó mappák elérési útja a rendszerkép létrehozásához. |
| `enableGpu` | `enable_gpu` | Választható. Azt határozza meg, hogy a GPU-támogatás engedélyezhető-e a rendszerképben. A GPU-képet olyan Azure-szolgáltatáson kell használni, mint például a Azure Container Instances, a Azure Machine Learning a számítás, az Azure Virtual Machines és az Azure Kubernetes szolgáltatás. Az alapértelmezett érték a False. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja.|
| `baseImage` | `base_image` | Választható. Alaprendszerképként használandó egyéni rendszerkép Ha nincs megadva alaprendszerkép, a rendszerkép a megadott futásidejű paraméter alapján fog alapulni. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja. |
| `baseImageRegistry` | `base_image_registry` | Választható. Az alapképet tartalmazó rendszerkép-beállításjegyzék. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja.|
| `cudaVersion` | `cuda_version` | Választható. A CUDA verziója a GPU-támogatást igénylő lemezképek telepítéséhez. A GPU-képet olyan Azure-szolgáltatáson kell használni, mint például a Azure Container Instances, a Azure Machine Learning a számítás, az Azure Virtual Machines és az Azure Kubernetes szolgáltatás. A támogatott verziók a következők: 9,0, 9,1 és 10,0. Ha a `enable_gpu` be van állítva, az alapértelmezett érték a 9,1. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja. |
| `description` | `description` | A rendszerkép leírása. Ha a `environment` be van állítva, a rendszer figyelmen kívül hagyja.  |
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

A következő JSON egy olyan példa, amely olyan konfigurációt mutat be, amely egy meglévő Azure Machine Learning [környezetet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) használ a parancssori felülettel való használathoz:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

A következő JSON példa arra a következtetésre, hogy egy meglévő Azure Machine Learning [környezetet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) használ a legújabb verzióval a CLI-vel való használatra:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
