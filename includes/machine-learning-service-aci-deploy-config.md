---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: 4f861d5e7832512e2c2732f1ce5dbf6ac76b91b1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935807"
---
A `deploymentconfig.json` dokumentum bejegyzései a [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)paramétereit képezik le. A következő táblázat ismerteti a JSON-dokumentum entitásai és a metódus paramétereinek megfeleltetését:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | n/a | A számítási cél. ACI esetén az értéknek `ACI`nak kell lennie. |
| `containerResourceRequirements` | n/a | A processzor-és memória-entitások tárolója. |
| &emsp;&emsp;`cpu` | `cpu_cores` | A lefoglalni kívánt CPU-magok száma. Alapértékek, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A webszolgáltatás számára lefoglalható memória mennyisége (GB-ban). Alapértelmezett, `0.5` |
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