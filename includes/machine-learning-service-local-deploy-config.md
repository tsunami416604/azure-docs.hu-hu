---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: e8c0b35f2ac6d3468a5e38a90cc7f4de09d3682b
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348540"
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
