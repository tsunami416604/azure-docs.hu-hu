---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348499"
---
A `inferenceconfig.json` dokumentumban szereplő bejegyzések a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály paramétereinek felelnek meg. A következő táblázat ismerteti a JSON-dokumentum entitásai közötti leképezést, valamint a metódus paramétereit:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A rendszerképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `runtime` | `runtime` | A rendszerképhez használandó futtatókörnyezet. A jelenleg támogatott futtatókörnyezetek a Spark-és a Python. |
| `condaFile` | `conda_file` | Választható. A képhez használandó Conda környezeti definíciót tartalmazó helyi fájl elérési útja. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Választható. A rendszerkép beállításakor futtatandó további Docker-lépéseket tartalmazó helyi fájl elérési útja. |
| `sourceDirectory` | `source_directory` | Választható. Az összes fájlt tartalmazó mappák elérési útja a rendszerkép létrehozásához. |
| `enableGpu` | `enable_gpu` | Választható. Azt határozza meg, hogy a GPU-támogatás engedélyezhető-e a rendszerképben. A GPU-képet a Microsoft Azure szolgáltatásban kell használni. Például Azure Container Instances, Azure Machine Learning számítás, Azure Virtual Machines és Azure Kubernetes szolgáltatás. Az alapértelmezett érték a false. |
| `baseImage` | `base_image` | Választható. Az alapképként használandó egyéni rendszerkép. Ha nincs megadva alaprendszerkép, a rendszer az alaprendszerképet használja a megadott futásidejű paraméter alapján. |
| `baseImageRegistry` | `base_image_registry` | Választható. Az alapképet tartalmazó rendszerkép-beállításjegyzék. |
| `cudaVersion` | `cuda_version` | Választható. A CUDA verziója a GPU-támogatást igénylő lemezképek telepítéséhez. A GPU-képet Microsoft Azure-szolgáltatásokban kell használni. Például Azure Container Instances, Azure Machine Learning számítás, Azure Virtual Machines és Azure Kubernetes szolgáltatás. A támogatott verziók a következők: 9,0, 9,1 és 10,0. Ha a "enable_gpu" be van állítva, az alapértelmezett érték a "9,1". |
| `description` | `description` |  A rendszerkép leírása. |

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