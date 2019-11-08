---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799886"
---
A `inferenceconfig.json` dokumentum bejegyzései a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály paramétereit képezik le. A következő táblázat ismerteti a JSON-dokumentum entitásai közötti leképezést, valamint a metódus paramétereit:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A rendszerképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `runtime` | `runtime` | A rendszerképhez használandó futtatókörnyezet. A jelenleg támogatott futtatókörnyezetek `spark-py` és `python`. |
| `condaFile` | `conda_file` | Választható. Egy helyi fájl elérési útja, amely a képhez használandó Conda-környezeti definíciót tartalmaz. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Választható. A rendszerkép beállításakor futtatandó további Docker-lépéseket tartalmazó helyi fájl elérési útja. |
| `sourceDirectory` | `source_directory` | Választható. Az összes fájlt tartalmazó mappák elérési útja a rendszerkép létrehozásához. |
| `enableGpu` | `enable_gpu` | Választható. Azt határozza meg, hogy a GPU-támogatás engedélyezhető-e a rendszerképben. A GPU-képet olyan Azure-szolgáltatáson kell használni, mint például a Azure Container Instances, a Azure Machine Learning a számítás, az Azure Virtual Machines és az Azure Kubernetes szolgáltatás. Az alapértelmezett érték a false. |
| `baseImage` | `base_image` | Választható. Alaprendszerképként használandó egyéni rendszerkép Ha nincs megadva alaprendszerkép, a rendszerkép a megadott futásidejű paraméter alapján fog alapulni. |
| `baseImageRegistry` | `base_image_registry` | Választható. Az alapképet tartalmazó rendszerkép-beállításjegyzék. |
| `cudaVersion` | `cuda_version` | Választható. A CUDA verziója a GPU-támogatást igénylő lemezképek telepítéséhez. A GPU-képet olyan Azure-szolgáltatáson kell használni, mint például a Azure Container Instances, a Azure Machine Learning a számítás, az Azure Virtual Machines és az Azure Kubernetes szolgáltatás. A támogatott verziók a következők: 9,0, 9,1 és 10,0. Ha a `enable_gpu` be van állítva, az alapértelmezett érték a 9,1. |
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