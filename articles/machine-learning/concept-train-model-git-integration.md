---
title: Git-integráció az Azure Machine Learninghez
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan integrálható az Azure Machine Learning egy helyi Git-tárházzal. Amikor egy git-tárházból származó helyi könyvtárból küld el egy betanítási futtatást, a tárházzal, az elágazással és az aktuális véglegesítéssel kapcsolatos információkat a futtatás részeként követi nyomon a rendszer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402821"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integráció az Azure Machine Learninghez

[Git](https://git-scm.com/) egy népszerű verzió-ellenőrzési rendszer, amely lehetővé teszi, hogy megosszák és együttműködnek a projektek. 

Az Azure Machine Learning teljes mértékben támogatja a Git-adattárak at nyomon követési munka – klónozhatja a tárolók közvetlenül a megosztott munkaterület ifájlrendszer, a Git a helyi munkaállomáson, vagy használja a Git egy CI/CD-folyamat.

Amikor egy feladatot küld az Azure Machine Learning, ha a forrásfájlok egy helyi git tárházban vannak tárolva, majd a tárházra vonatkozó információkat a betanítási folyamat részeként nyomon követi.

Mivel az Azure Machine Learning nyomon követi a helyi git-tárházból származó információkat, nem kötődik egyetlen központi tárházhoz sem. A tárház klónozható a GitHubról, a GitLabből, a Bitbucketből, az Azure DevOps-ból vagy bármely más git-kompatibilis szolgáltatásból.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git-adattárak klónozása a munkaterületi fájlrendszerbe
Az Azure Machine Learning megosztott fájlrendszert biztosít a munkaterület összes felhasználója számára.
Git-tárház ebbe a fájlmegosztásba történő klónozásához javasoljuk, hogy hozzon létre egy számítási példányt, & nyisson meg egy terminált.
A terminál megnyitása után hozzáférhet egy teljes Git klienshez, és klónozhatja és dolgozhat a Git-tel a Git CLI-felületen keresztül.

Azt javasoljuk, hogy klónozza a tárházat a felhasználói könyvtárba, hogy mások ne ütközhessenek közvetlenül a munkaágán.

Klónozhat bármely Git-tárházat, amelyen hitelesíthető (GitHub, Azure Repos, BitBucket stb.)

A Git CLI használatának útmutatóját [itt](https://guides.github.com/introduction/git-handbook/)olvashatja.

## <a name="track-code-that-comes-from-git-repositories"></a>Git-adattárakból származó kód nyomon követése

Amikor beküld egy betanítást a Python SDK vagy a Machine Learning CLI, a modell betanításához szükséges fájlokat feltölti a munkaterületre. Ha `git` a parancs elérhető a fejlesztői környezetben, a feltöltési folyamat segítségével ellenőrzi, hogy a fájlok egy git-tárházban vannak-e tárolva. Ha igen, akkor a git-tárházból származó adatok is feltöltésre kerül a betanítási futtatás részeként. Ezt az információt a következő tulajdonságok tárolják a betanítási futtatáshoz:

| Tulajdonság | Az érték leértékeléséhez használt Git parancs | Leírás |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Az URI, amelyből a tárház klónozott. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Az URI, amelyből a tárház klónozott. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Az aktív ág a futtatás elküldésekor. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Az aktív ág a futtatás elküldésekor. |
| `azureml.git.commit` | `git rev-parse HEAD` | A futtatásra elküldött kód véglegesítési kivonata. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | A futtatásra elküldött kód véglegesítési kivonata. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, ha a fióktelep/elköteleződés piszkos; ellenkező `false`esetben . |

Ez az információ egy estimator, machine learning-folyamat vagy parancsfájl futtatását használó futtatások esetén kerül elküldésre.

Ha a betanítási fájlok nem találhatók a git-tárházban a fejlesztői környezetben, vagy a `git` parancs nem érhető el, majd nem git kapcsolatos információkat nyomon követi nyomon.

> [!TIP]
> Annak ellenőrzéséhez, hogy a git parancs elérhető-e a fejlesztői környezetben, nyisson meg egy rendszerhéj-munkamenetet, parancssort, PowerShell vagy más parancssori felületet, és írja be a következő parancsot:
>
> ```
> git --version
> ```
>
> Ha telepítve van, és az elérési úton, a következőhöz hasonló választ kap, mint a. `git version 2.4.1` A git fejlesztési környezetre történő telepítéséről a [Git webhelyén](https://git-scm.com/)talál további információt.

## <a name="view-the-logged-information"></a>A naplózott adatok megtekintése

A git-adatok egy betanítási futtatás tulajdonságai között tárolódnak. Ezeket az információkat az Azure Portalon, a Python SDK-n és a CLI-n keresztül tekintheti meg. 

### <a name="azure-portal"></a>Azure portál

1. Az [Azure Portalon](https://portal.azure.com)válassza ki a munkaterületet.
1. Válassza __a Kísérletek__lehetőséget, majd válasszon egyet a kísérletek közül.
1. Válassza ki az egyik fut a __RUN NUMBER__ oszlopban.
1. Válassza __a Naplók__lehetőséget, majd __bontsa__ ki a naplókat és az __azureml__ bejegyzéseket. Válassza ki az __ ### \_azure-ral__kezdődő hivatkozást.

    ![A ###_azure bejegyzés a portálon](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

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

Egy betanítási futtatás elküldése után egy [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) objektum ot ad vissza. Az `properties` objektum attribútuma tartalmazza a naplózott git-adatokat. A következő kód például lekéri a véglegesítési kivonatot:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>parancssori felület

A `az ml run` CLI paranccsal lekérheti a tulajdonságokat egy futtatásból. A következő parancs például a kísérlet utolsó futtatásának `train-on-amlcompute`tulajdonságait adja vissza:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

További információt az [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) futtatásreferencia-dokumentációban talál.

## <a name="next-steps"></a>További lépések

* [Számítási célok beállítása és használata a modellbetanításhoz](how-to-set-up-training-targets.md)
