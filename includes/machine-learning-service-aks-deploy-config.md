---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: 2124b5241015ca74ff6507767396b1a27bd1191d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935828"
---
A `deploymentconfig.json` dokumentum bejegyzései a [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)paramétereit képezik le. A következő táblázat ismerteti a JSON-dokumentum entitásai és a metódus paramétereinek megfeleltetését:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | n/a | A számítási cél. Az AK esetében az értéknek `aks`nak kell lennie. |
| `autoScaler` | n/a | Az automatikus skálázás konfigurációs elemeit tartalmazza. Tekintse meg az autoskálázási táblázatot. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Azt határozza meg, hogy engedélyezi-e az automatikus skálázást a webszolgáltatáshoz. Ha `numReplicas` = `0`, `True`; Ellenkező esetben `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | A webszolgáltatás automatikus skálázásakor használandó tárolók minimális száma. Alapértelmezett, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | A webszolgáltatás automatikus skálázásakor használandó tárolók maximális száma. Alapértelmezett, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Az autoskálázás milyen gyakran próbálja méretezni ezt a webszolgáltatást. Alapértelmezett, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A cél kihasználtsága (az 100-as százalékban kifejezve), amelyet az autoskálázásnak meg kell próbálnia fenntartani a webszolgáltatás számára. Alapértelmezett, `70`. |
| `dataCollection` | n/a | Az adatgyűjtés konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Azt határozza meg, hogy engedélyezi-e a modell adatgyűjtését a webszolgáltatáshoz. Alapértelmezett, `False`. |
| `authEnabled` | `auth_enabled` | Azt határozza meg, hogy engedélyezi-e a kulcsos hitelesítést a webszolgáltatáshoz. A `tokenAuthEnabled` és a `authEnabled` nem lehet `True`. Alapértelmezett, `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Azt határozza meg, hogy engedélyezve van-e a jogkivonat-hitelesítés a webszolgáltatáshoz. A `tokenAuthEnabled` és a `authEnabled` nem lehet `True`. Alapértelmezett, `False`. |
| `containerResourceRequirements` | n/a | A processzor-és memória-entitások tárolója. |
| &emsp;&emsp;`cpu` | `cpu_cores` | A webszolgáltatás számára lefoglalható CPU-magok száma. Alapértékek, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A webszolgáltatás számára lefoglalható memória mennyisége (GB-ban). Alapértelmezett, `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Azt határozza meg, hogy engedélyezi-e Application Insights naplózást a webszolgáltatáshoz. Alapértelmezett, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | A webszolgáltatásnak való pontozási hívások kényszerített időkorlátja. Alapértelmezett, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | A webszolgáltatáshoz tartozó csomópontok maximális egyidejű kérelmeinek száma. Alapértelmezett, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | Az a maximális időtartam, ameddig egy kérelem a 503-es hiba visszaadása előtt a várólistában marad (ezredmásodpercben). Alapértelmezett, `500`. |
| `numReplicas` | `num_replicas` | A webszolgáltatás számára lefoglalható tárolók száma. Nincs alapértelmezett érték. Ha ez a paraméter nincs beállítva, az automéretező alapértelmezés szerint engedélyezve van. |
| `keys` | n/a | A kulcsok konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`primaryKey` | `primary_key` | A webszolgáltatáshoz használandó elsődleges hitelesítési kulcs |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | A webszolgáltatáshoz használandó másodlagos hitelesítési kulcs |
| `gpuCores` | `gpu_cores` | A webszolgáltatás számára lefoglalható GPU-magok száma. Az alapértelmezett érték 1. Csak az egész szám értékeit támogatja. |
| `livenessProbeRequirements` | n/a | Az élettartam mintavételi követelményeinek konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Milyen gyakran (másodpercben) kell végrehajtani az élettartam-mintavételt. Az alapértelmezett érték 10 másodperc. A minimális érték 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Azon másodpercek száma, ameddig a tároló el lett indítva az élő mintavételek elindítása előtt. Alapértelmezett értéke 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Azon másodpercek száma, amely után az élettartam mintavétele időtúllépést mutat. Az alapértelmezett érték 2 másodperc. A minimális érték 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Az élettartam-mintavétel minimális egymást követő sikerei sikeresnek tekintendők a sikertelenség után. Az alapértelmezett érték 1. A minimális érték 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Ha egy Pod elindul, és az élettartam-mintavétel meghiúsul, a Kubernetes a failureThreshold időt fogja kipróbálni. Az alapértelmezett érték 3. A minimális érték 1. |
| `namespace` | `namespace` | Az a Kubernetes-névtér, amelyre a webszolgáltatás telepítve van. Akár 63 kisbetűs alfanumerikus ("név elején-kisbetűket", "0"-"9") és kötőjel ("-") karakter. Az első és az utolsó karakter nem lehet kötőjel. |

A következő JSON a parancssori felülettel való használatra példaként használható telepítési konfiguráció:

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
