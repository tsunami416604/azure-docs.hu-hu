---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: ec0f85ff568bcd89d74ccd727d1c5ecfd8aab398
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390662"
---
A `deploymentconfig.json` dokumentumban szereplő bejegyzések a [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)paramétereit képezik. A következő táblázat ismerteti a JSON-dokumentum entitásai és a metódus paramétereinek megfeleltetését:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. Helyi célok esetén az értéknek a következőnek kell lennie `local`:. |
| `port` | `port` | A helyi port, amelyen elérhetővé kívánja tenni a szolgáltatás HTTP-végpontját. |

Ez a JSON egy példaként szolgáló telepítési konfiguráció a parancssori felülettel való használatra:

```json
{
    "computeType": "local",
    "port": 32267
}
```
