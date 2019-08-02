---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: a87773c8f6ddda11b62064758b46d666740a03c2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556945"
---
A `deploymentconfig.json` dokumentumban szereplő bejegyzések a [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)paramétereit képezik. A következő táblázat ismerteti a JSON-dokumentum entitásai és a metódus paramétereinek megfeleltetését:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. Helyi esetén az értéknek a következőnek kell lennie `local`:. |
| `port` | `port` | A helyi port, amelyen elérhetővé kívánja tenni a szolgáltatás HTTP-végpontját. |

A következő JSON a parancssori felülettel való használatra példaként használható telepítési konfiguráció:

```json
{
    "computeType": "local",
    "port": 32267
}
```
