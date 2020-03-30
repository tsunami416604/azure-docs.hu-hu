---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477272"
---
A dokumentum `deploymentconfig.json` bejegyzései a [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)paramétereire vannak leképezve. Az alábbi táblázat a JSON-dokumentumban szereplő entitások és a módszer paraméterei közötti leképezést ismerteti:

| JSON entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. Helyi célok esetén az `local`értéknek . |
| `port` | `port` | A helyi port, amelyen a szolgáltatás HTTP-végpontját elérhetővé teszi. |

Ez a JSON egy példa a központi telepítési konfiguráció a CLI-vel való használatra:

```json
{
    "computeType": "local",
    "port": 32267
}
```
