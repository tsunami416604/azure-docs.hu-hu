---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486101"
---
A dokumentum `deploymentconfig.json` bejegyzései az [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)paramétereire vannak leképezve. Az alábbi táblázat a JSON-dokumentumban szereplő entitások és a módszer paraméterei közötti leképezést ismerteti:

| JSON entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. Az AKS esetében az `aks`értéknek . |
| `autoScaler` | NA | Az automatikus skálázás konfigurációs elemeit tartalmazza. Tekintse meg az automatikus méretező táblát. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Engedélyezi-e az automatikus skálázást a webszolgáltatáshoz. `numReplicas`  = Ha `0` `True`, ; ellenkező `False`esetben . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | A webszolgáltatás automatikus skálázása során használandó tárolók minimális száma. Alapértelmezett `1`érték. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | A webszolgáltatás automatikus skálázása kor használandó tárolók maximális száma. Alapértelmezett `10`érték. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Milyen gyakran az automatikus skálázó megpróbálja méretezni ezt a webszolgáltatást. Alapértelmezett `1`érték. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A célkihasználtság (a 100 százalékban), amelyet az automatikus skálázónak meg kell kísérelnie fenntartani ehhez a webszolgáltatáshoz. Alapértelmezett `70`érték. |
| `dataCollection` | NA | Az adatgyűjtés konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Azt jelzi, hogy engedélyezi-e a modelladatgyűjtést a webszolgáltatásszámára. Alapértelmezett `False`érték. |
| `authEnabled` | `auth_enabled` | Azt jelzi, hogy engedélyezi-e a kulcshitelesítést a webszolgáltatásszámára. `tokenAuthEnabled` Mindkettő, `authEnabled` és `True`nem lehet . Alapértelmezett `True`érték. |
| `tokenAuthEnabled` | `token_auth_enabled` | Azt jelzi, hogy engedélyezi-e a tokenhitelesítést a webszolgáltatásszámára. `tokenAuthEnabled` Mindkettő, `authEnabled` és `True`nem lehet . Alapértelmezett `False`érték. |
| `containerResourceRequirements` | NA | A processzor- és memóriaentitások tárolója. |
| &emsp;&emsp;`cpu` | `cpu_cores` | A webszolgáltatásszámára lefoglalandó processzormagok száma. Alapértelmezett`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A webszolgáltatás számára lefoglalandó memória mennyisége (GB-ban). Alapértelmezett`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Engedélyezi-e az Application Insights naplózását a webszolgáltatáshoz. Alapértelmezett `False`érték. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Időkérés a webszolgáltatás hívásainak pontozási kényszerítéséhez. Alapértelmezett `60000`érték. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | A webszolgáltatás csomópontonkénti egyidejű kérelmek maximális száma. Alapértelmezett `1`érték. |
| `maxQueueWaitMs` | `max_request_wait_time` | A kérelem maximális várakozási ideje (ezredmásodpercben) marad az 503-as hiba visszaadása előtt. Alapértelmezett `500`érték. |
| `numReplicas` | `num_replicas` | A webszolgáltatás számára lefoglalandó tárolók száma. Nincs alapértelmezett érték. Ha ez a paraméter nincs beállítva, az automatikus méretező alapértelmezés szerint engedélyezve van. |
| `keys` | NA | Kulcsok konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`primaryKey` | `primary_key` | A webszolgáltatáshoz használandó elsődleges hitelesítési kulcs |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | A webszolgáltatáshoz használandó másodlagos hitelesítési kulcs |
| `gpuCores` | `gpu_cores` | A webszolgáltatás számára lefoglalandó GPU-magok száma (tárolónkénti replika). Az alapértelmezett érték 1. Csak a teljes számértékeket támogatja. |
| `livenessProbeRequirements` | NA | Az elevenségi mintavételi követelmények konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Milyen gyakran (másodpercben) az élősdő szonda végrehajtásához. Alapértelmezés szerint 10 másodperc. A minimális érték 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | A tároló indítása után a másodpercek száma az élősdő szondák megkezdése előtt. Alapértelmezés szerint 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Azon másodpercek száma, amelyek után az elevenségi szonda időtúljár. Alapértelmezés szerint 2 másodperc. A minimális érték 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minimális egymást követő sikerek az élősdő szonda sikeresnek tekinthető, miután nem sikerült. Alapértelmezés szerint 1. A minimális érték 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Amikor egy pod elindul, és a liveness-szonda sikertelen, Kubernetes megpróbálja failureThreshold alkalommal, mielőtt feladja. Alapértelmezés szerint 3. A minimális érték 1. |
| `namespace` | `namespace` | A Kubernetes névtér, amely a webszolgáltatás telepítve van. Legfeljebb 63 kisalfanumerikus ('a'-'z', '0'-'9') és kötőjel ('-') karakter. Az első és az utolsó karakter nem lehet kötőjel. |

A következő JSON egy példa a CLI-vel használható központi telepítési konfiguráció:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
