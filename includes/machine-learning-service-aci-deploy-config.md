---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: 6e2a3a75181cf381f09e06b52c9bb3928dee4896
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556844"
---
A `deploymentconfig.json` dokumentumban szereplő bejegyzések a [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)paramétereit képezik. A következő táblázat ismerteti a JSON-dokumentum entitásai és a metódus paramétereinek megfeleltetését:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. ACI esetén az értéknek a következőnek kell lennie `ACI`:. |
| `containerResourceRequirements` | NA | A processzor-és memória-entitások tárolója. |
| &emsp;&emsp;`cpu` | `cpu_cores` | A lefoglalni kívánt CPU-magok száma. Alapértelmezett`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A webszolgáltatás számára lefoglalható memória mennyisége (GB-ban). Alapértelmezett`0.5` |
| `location` | `location` | Az Azure-régió, amelybe telepíteni szeretné a webszolgáltatást. Ha nincs megadva, a rendszer a munkaterület helyét fogja használni. Az elérhető régiókról további részleteket itt találhat: [ACI-régiók](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Azt határozza meg, hogy engedélyezi-e az Auth szolgáltatást ehhez a webszolgáltatáshoz. Alapértelmezett értéke false |
| `sslEnabled` | `ssl_enabled` | Azt határozza meg, hogy engedélyezve van-e az SSL ehhez a webszolgáltatáshoz. Az alapértelmezett érték a false. |
| `appInsightsEnabled` | `enable_app_insights` | Azt határozza meg, hogy engedélyezi-e a AppInsights ehhez a webszolgáltatáshoz. Alapértelmezett értéke false |
| `sslCertificate` | `ssl_cert_pem_file` | Az SSL engedélyezése esetén szükséges a tanúsítvány fájlja |
| `sslKey` | `ssl_key_pem_file` | Az SSL engedélyezése esetén szükség van a kulcs fájlra |
| `cname` | `ssl_cname` | A CNAME, ha engedélyezve van az SSL |
| `dnsNameLabel` | `dns_name_label` | A pontozási végpont DNS-neve felirata Ha nincs megadva, a rendszer létrehoz egy egyedi DNS-név címkét a pontozási végponthoz. |

A következő JSON a parancssori felülettel való használatra példaként használható telepítési konfiguráció:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```