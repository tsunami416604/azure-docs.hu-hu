---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486105"
---
A dokumentum `deploymentconfig.json` bejegyzései az [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)paramétereire vannak leképezve. Az alábbi táblázat a JSON-dokumentumban szereplő entitások és a módszer paraméterei közötti leképezést ismerteti:

| JSON entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. Az ACI esetében az `ACI`értéknek . |
| `containerResourceRequirements` | NA | A processzor- és memóriaentitások tárolója. |
| &emsp;&emsp;`cpu` | `cpu_cores` | A leosztandó processzormagok száma. Alapértelmezett`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A webszolgáltatás számára lefoglalandó memória mennyisége (GB-ban). Alapértelmezett`0.5` |
| `location` | `location` | Az Azure-régió, ahol ezt a webszolgáltatást telepíti. Ha nincs megadva, a program a munkaterület helyét fogja használni. További részletek a rendelkezésre álló régiókról itt találhatók: [ACI Regions](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Engedélyezi-e a webszolgáltatás hitelesítési szolgáltatását. Alapértelmezés szerint hamis |
| `sslEnabled` | `ssl_enabled` | Engedélyezi-e az SSL-t ehhez a webszolgáltatáshoz. Alapértelmezés szerint hamis. |
| `appInsightsEnabled` | `enable_app_insights` | Azt jelzi, hogy engedélyezi-e az AppInsights szolgáltatást ehhez a webszolgáltatáshoz. Alapértelmezés szerint hamis |
| `sslCertificate` | `ssl_cert_pem_file` | Az SSL engedélyezése esetén szükséges tanúsítványfájl |
| `sslKey` | `ssl_key_pem_file` | Az SSL engedélyezése esetén szükséges kulcsfájl |
| `cname` | `ssl_cname` | A cname, ha az SSL engedélyezve van |
| `dnsNameLabel` | `dns_name_label` | A pontozási végpont dns-névcímkéje. Ha nincs megadva, egy egyedi DNS-névcímke jön létre a pontozási végponthoz. |

A következő JSON egy példa a CLI-vel használható központi telepítési konfiguráció:

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