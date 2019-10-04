---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 055909d51fcd1228e8eb26189ba682e09aee6a1a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390577"
---
A `inferenceconfig.json` dokumentumban szereplő bejegyzések a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály paramétereinek felelnek meg. A következő táblázat ismerteti a JSON-dokumentum entitásai közötti leképezést, valamint a metódus paramétereit:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A rendszerképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `runtime` | `runtime` | A rendszerképhez használandó futtatókörnyezet. A jelenleg támogatott futtatókörnyezetek `spark-py` a `python`és a. |
| `condaFile` | `conda_file` | Nem kötelező. Egy helyi fájl elérési útja, amely a képhez használandó Conda-környezeti definíciót tartalmaz. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Nem kötelező. A rendszerkép beállításakor futtatandó további Docker-lépéseket tartalmazó helyi fájl elérési útja. |
| `sourceDirectory` | `source_directory` | Nem kötelező. Az összes fájlt tartalmazó mappák elérési útja a rendszerkép létrehozásához. |
| `enableGpu` | `enable_gpu` | Nem kötelező. Azt határozza meg, hogy a GPU-támogatás engedélyezhető-e a rendszerképben. A GPU-képet olyan Azure-szolgáltatáson kell használni, mint például a Azure Container Instances, a Azure Machine Learning a számítás, az Azure Virtual Machines és az Azure Kubernetes szolgáltatás. Az alapértelmezett érték a false. |
| `baseImage` | `base_image` | Nem kötelező. Alaprendszerképként használandó egyéni rendszerkép Ha nincs megadva alaprendszerkép, a rendszerkép a megadott futásidejű paraméter alapján fog alapulni. |
| `baseImageRegistry` | `base_image_registry` | Nem kötelező. Az alapképet tartalmazó rendszerkép-beállításjegyzék. |
| `cudaVersion` | `cuda_version` | Nem kötelező. A CUDA verziója a GPU-támogatást igénylő lemezképek telepítéséhez. A GPU-képet olyan Azure-szolgáltatáson kell használni, mint például a Azure Container Instances, a Azure Machine Learning a számítás, az Azure Virtual Machines és az Azure Kubernetes szolgáltatás. A támogatott verziók a következők: 9,0, 9,1 és 10,0. Ha `enable_gpu` be van állítva, az alapértelmezett érték a 9,1. |
| `description` | `description` | A rendszerkép leírása. |

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