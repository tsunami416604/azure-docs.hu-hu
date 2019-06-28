---
title: Az Azure Monitor konfigurálása az ügynök adatgyűjtés tárolók |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Monitor szabályozhatja az stdout/stderr-tárolók ügynök és a környezeti változók naplót.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341648"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Tárolókhoz az Azure Monitor ügynök adatok gyűjtésének konfigurálása

A tárolók az Azure Monitor tárolóalapú számítási feladatok üzembe helyezett Kubernetes-fürtök Azure Kubernetes Service (AKS) lévő üzemeltetett a tárolóalapú ügynök által felügyelt környezeti változókat, az stdout és stderr gyűjti. Konfigurálhatja az ügynök adatgyűjtési beállítások hozzon létre egy egyéni Kubernetes ConfigMaps szabályozhatja a felhasználói élményt. Ez a cikk bemutatja, hogyan ConfigMap létrehozni és konfigurálni az adatgyűjtés igényei alapján.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>A fürt egyéni adatgyűjtési beállítások konfigurálása

A sablonfájl ConfigMap kerül, amely lehetővé teszi, hogy könnyen szerkeszthetik a testreszabásokat az előzmények létrehozása nélkül. Mielőtt hozzákezdene, tekintse át a Kubernetes dokumentációja kapcsolatos [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) , és ismerje meg, hogyan hozhat létre, konfigurálása és üzembe helyezése ConfigMaps. Ez lehetővé teszi, hogy a szűrő stderr és névterenként vagy az egész fürt és a környezeti változók bármilyen podok/fürt összes csomópontján a futó tároló stdout.

>[!IMPORTANT]
>Ez a szolgáltatás által támogatott ügynök minimális verzió a microsoft / oms:ciprod06142019 vagy újabb. 

### <a name="overview-of-configurable-data-collection-settings"></a>Konfigurálható adatgyűjtési beállítások áttekintése

Az alábbiakban a beállításokat, amelyek konfigurálhatók az adatgyűjtés szabályozására.

|Kulcs |Adattípus |Érték |Leírás |
|----|----------|------|------------|
|`schema-version` |Karakterlánc (megkülönbözteti a kis-és nagybetűk megkülönböztetése) |v1 |Ez az a séma verziója, az ügynök által használt, ez ConfigMap elemzésekor. Jelenleg támogatott sémaverzió v1. Ez az érték módosítása nem támogatott, és a rendszer elutasítja ConfigMap kiértékelésekor.|
|`config-version` |String | | Nyomon követheti, a verziókövetési rendszer/tárházat a konfigurációs fájl verzióját támogatja. Megengedett maximális karakterszámot, 10, és minden egyéb, karaktereket csonkolva lesznek. |
|`[log_collection_settings.stdout] enabled =` |Boolean | IGAZ vagy hamis | Ez vezérli, hogy az stdout tároló naplógyűjtés engedélyezve van. Ha beállítása `true` és nem névterek nem tartoznak a stdout naplógyűjtés (`log_collection_settings.stdout.exclude_namespaces` beállítást alább), stdout naplókat gyűjt be az összes tárolót a fürt összes podok/csomópont. Ha nincs megadva ConfigMaps, az alapértelmezett értéke `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Tömböt vesszővel elválasztva |Kubernetes-névtér az melyik stdout naplók nem lesznek összegyűjtve tömbje. Ez a beállítás érvényben csak akkor, ha `log_collection_settings.stdout.enabled` értékre van állítva `true`. Ha nincs megadva ConfigMap, az alapértelmezett értéke `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | IGAZ vagy hamis |Ez vezérli, ha engedélyezve van a stderr tároló naplógyűjtést. Ha a beállítása `true` nem névterek nem tartoznak a stdout naplógyűjtés és (`log_collection_settings.stderr.exclude_namespaces` beállítás), stderr naplókat gyűjt be az összes tárolót a fürt összes podok/csomópont. Ha nincs megadva ConfigMaps, az alapértelmezett értéke `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Tömböt vesszővel elválasztva |Kubernetes-névterek számára mely stderr naplók nem lesznek összegyűjtve tömbje. Ez a beállítás érvényben csak akkor, ha `log_collection_settings.stdout.enabled` értékre van állítva `true`. Ha nincs megadva ConfigMap, az alapértelmezett értéke `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | IGAZ vagy hamis | Ez vezérli, ha engedélyezve van a környezeti változó gyűjtemény. Ha a beállítása `false`, nincsenek környezeti változók gyűjtött összes podok/csomópont a fürtben futó bármely tároló. Ha nincs megadva ConfigMap, az alapértelmezett értéke `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Konfigurálhatja és telepítheti a ConfigMaps

A következő lépésekkel konfigurálásához és üzembe helyezése a fürtön a ConfigMap konfigurációs fájlban.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) a sablon ConfigMap yaml fájlt, és tároló-azm-ms-agentconfig.yaml mentse.  
1. Szerkessze a testre szabást a ConfigMap yaml-fájlt. 

    - Zárhat ki adott névterek az stdout naplógyűjtés, konfigurálja az alábbi példa a kulcs/érték: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Környezeti változó gyűjtemény egy adott tároló letiltásához állítsa be a kulcs/érték `[log_collection_settings.env_var] enabled = true` változógyűjteményben globálisan engedélyezéséhez, és kövesse a lépéseket [Itt](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) az adott tároló konfigurálásának befejezéséhez.
    - Kívánja letiltani az stderr naplók összegyűjtése fürtre kiterjedő, konfigurálja az alábbi példa a kulcs/érték: `[log_collection_settings.stderr] enabled = false`.

1. Hozzon létre ConfigMap a következő kubectl-parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A konfiguráció módosításának befejezése előtt életbe pár percet is igénybe vehet, és a fürt összes omsagent podok újraindul. Az újraindítás összes omsagent podok működés közbeni újraindítása, a nem az összes újraindítani egyszerre. Az újraindítás befejeződése után megjelenik egy üzenet, amely a következőhöz hasonló, és az eredmény tartalmazza: `configmap "container-azm-ms-agentconfig" created`.

Annak ellenőrzéséhez, hogy a konfiguráció sikeresen alkalmazva lett, a következő parancs használatával egy ügynök pod naplóinak áttekintése: `kubectl logs omsagent-fdf58 -n=kube-system`. Ha a osmagent podok konfigurációs hibák, a kimenet megjeleníti hibák a következőhöz hasonló:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Hibák omsagent megakadályozhatja a fájl okozza, hogy indítsa újra, és az alapértelmezett konfiguráció elemzésekor. Miután ConfigMap a hibá (ka), mentse a yaml-fájlt, és a frissített ConfigMaps alkalmazni a parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>ConfigMap alkalmazásának frissítése

Ha korábban már telepítettek egy ConfigMap a fürt számára, és frissíti a újabb konfigurációval, a ConfigMap fájl korábban már használta, és akkor alkalmazható, mint korábban, használja ugyanazt a parancsot egyszerűen szerkesztheti `kubectl apply -f <configmap_yaml_file.yaml`.

A konfiguráció módosításának befejezése előtt életbe pár percet is igénybe vehet, és a fürt összes omsagent podok újraindul. Az újraindítás összes omsagent podok működés közbeni újraindítása, a nem az összes újraindítani egyszerre. Az újraindítás befejeződése után megjelenik egy üzenet, amely a következőhöz hasonló, és az eredmény tartalmazza: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Sémaverzió ellenőrzése

Támogatott konfigurációs séma verziók jegyzetként pod (séma-verzió) az a omsagent pod érhetők el. Azokat a következő kubectl-paranccsal tekintheti meg: `kubectl describe pod omsagent-fdf58 -n=kube-system`

A kimenet megjeleníti a jegyzet séma-verziókkal rendelkező a következőhöz hasonló:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan kell használni az Azure Monitor és egyéb monitorozhatja az AKS-fürt a folytatáshoz tekintse meg a [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
- Nézet [lekérdezés példák jelentkezzen](container-insights-log-search.md#search-logs-to-analyze-data) , előre definiált lekérdezések és példák kiértékelése vagy riasztási, megjelenítése és elemzése a fürtök testreszabásához.