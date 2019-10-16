---
title: Git-integráció a Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan integrálható a Azure Machine Learning egy helyi git-adattárral.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377511"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integráció a Azure Machine Learning

A [git](https://git-scm.com/) egy népszerű verziókövetés-rendszer, amely lehetővé teszi a projektek megosztását és együttműködését. Amikor beküld egy betanítási feladatot a Azure Machine Learningre, ha a betanítási fájlokat egy helyi git-tárházban tárolja, a program a betanítási folyamat részeként nyomon követi az adattárral kapcsolatos információkat.

Mivel Azure Machine Learning nyomon követi a helyi git-tárház adatait, nem kötődik egyetlen konkrét központi adattárhoz sem. A tárházat a GitHub, a GitLab, a bitbucket, az Azure DevOps vagy más git-kompatibilis szolgáltatásból lehet klónozott.

## <a name="how-does-git-integration-work"></a>Hogyan működik a git-integráció?

Amikor beküld egy képzést a Python SDK-ból vagy Machine Learning CLI-ből, a modell betanításához szükséges fájlok fel lesznek töltve a munkaterületre. Ha a `git` parancs elérhető a fejlesztői környezetben, a feltöltési folyamat segítségével ellenőrizze, hogy a fájlokat git-tárházban tárolja-e a rendszer. Ha igen, akkor a git-tárházból származó információk is fel vannak töltve a betanítási Futtatás részeként. Ezeket az adatokat a következő tulajdonságok tárolják a betanítási futtatáshoz:

| Tulajdonság | Leírás |
| ----- | ----- |
| `azureml.git.repository_uri` | Az a URI, amelyből a tárház klónozott volt. |
| `mlflow.source.git.repoURL` | Az a URI, amelyből a tárház klónozott volt. |
| `azureml.git.branch` | Az aktív ág a Futtatás elküldését követően. |
| `mlflow.source.git.branch` | Az aktív ág a Futtatás elküldését követően. |
| `azureml.git.commit` | A futtatáshoz benyújtott kód véglegesítő kivonata. |
| `mlflow.source.git.commit` | A futtatáshoz benyújtott kód véglegesítő kivonata. |
| `azureml.git.dirty` | @no__t – 0, ha a véglegesítés inkonzisztens; Ellenkező esetben `false`. |

Ezeket az információkat a rendszer a becsléseket, a gépi tanulási folyamatot vagy a parancsfájlok futtatását használó futtatásokhoz továbbítja.

Ha a képzési fájlok nem egy git-tárházban találhatók a fejlesztői környezetben, vagy a `git` parancs nem érhető el, akkor a rendszer nem követi a git-hez kapcsolódó adatokat.

## <a name="view-the-logged-information"></a>Naplózott adatok megtekintése

A git-adatokat egy tanítási Futtatás tulajdonságai tárolják. Ezeket az információkat a Azure Portal, a Python SDK és a CLI használatával tekintheti meg. 

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com)válassza ki a munkaterületet.
1. Válassza a __kísérletek__lehetőséget, majd válassza ki az egyik kísérletet.
1. Válassza ki a __futtatások száma__ oszlop egyik futtatását.
1. Válassza a __naplók__lehetőséget, majd bontsa ki a __naplók__ és a __azureml__ bejegyzéseket. Válassza ki a __### @ no__t-2azure__kezdetű hivatkozást.

A naplózott információ a következő JSON-hoz hasonló szöveget tartalmaz:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

A betanítási Futtatás elküldése után a rendszer egy [futtatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) objektumot ad vissza. Az objektum `properties` attribútuma tartalmazza a naplózott git-információkat. A következő kód például lekéri a véglegesítő kivonatot:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

A `az ml run` CLI-parancs használatával lekérheti a tulajdonságokat egy futtatásból. A következő parancs például visszaadja a `train-on-amlcompute` nevű kísérlet utolsó futtatásának tulajdonságait:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

További információ: az [ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) Reference dokumentáció.

## <a name="next-steps"></a>Következő lépések

* A Azure Machine Learning Visual Studio Code-ban való betanításával kapcsolatos útmutatóért lásd az [oktatóanyag: modellek betanítása a Azure Machine learning](tutorial-train-models-with-aml.md)használatával című témakört.
* A kódok helyi szerkesztéséről, futtatásáról és hibakereséséről a [Python Hello-World oktatóanyagban](https://code.visualstudio.com/docs/Python/Python-tutorial)talál további információt.
