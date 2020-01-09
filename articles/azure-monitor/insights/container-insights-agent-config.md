---
title: Azure Monitor konfigurálása a containers Agent adatgyűjtéshez | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Azure Monitor for containers Agent az stdout/stderr és a környezeti változók naplózási gyűjteményének vezérléséhez.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 0bde696f39af22f864500e0c79b5e03ca66cc7f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405684"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Az ügynök adatgyűjtésének konfigurálása a tárolók számára Azure Monitor

A tárolók Azure Monitor az stdout, a stderr és a környezeti változókat gyűjtik az Azure Kubernetes Service-ben (ak) üzemeltetett felügyelt Kubernetes-fürtökön üzembe helyezett tároló munkaterhelésekről a tároló ügynökből. Az ügynök adatgyűjtési beállításainak konfigurálásához létrehozhat egy egyéni Kubernetes-ConfigMaps a felhasználói élmény szabályozása érdekében. 

Ez a cikk bemutatja, hogyan hozhat létre ConfigMap, és hogyan konfigurálhatja az adatgyűjtést a követelmények alapján.

## <a name="configmap-file-settings-overview"></a>A ConfigMap-fájlok beállításai – áttekintés

A sablon ConfigMap fájlja lehetővé teszi, hogy egyszerűen szerkessze a testreszabásokkal anélkül, hogy teljesen létre kellene hoznia. A Kezdés előtt tekintse át a [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) Kubernetes dokumentációját, és ismerkedjen meg a ConfigMaps létrehozásával, konfigurálásával és üzembe helyezésével. Ez lehetővé teszi a stderr és az stdout szűrését a névtérben vagy a teljes fürtön, valamint a fürtben található összes hüvely/csomóponton futó tároló környezeti változóit.

>[!IMPORTANT]
>Az ügynök minimális verziója támogatja az stdout, a stderr és a környezeti változók begyűjtését a tároló munkaterhelésekről ciprod06142019 vagy újabb verzióra. Az ügynök verziójának ellenőrzéséhez a **csomópont** lapon válasszon ki egy csomópontot, majd a Tulajdonságok panelen jegyezze fel az **ügynök Képcímke** tulajdonságának értékét. További információ az ügynök verziójáról és az egyes kiadásokról: [ügynök kibocsátási megjegyzései](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Adatgyűjtés beállításai

Az alábbi beállításokkal konfigurálhatja az adatgyűjtés vezérlését.

|Jelmagyarázat |Data type |Value (Díj) |Leírás |
|----|----------|------|------------|
|`schema-version` |Karakterlánc (megkülönbözteti a kis-és nagybetűket) |v1 |Ez az ügynök által a ConfigMap elemzésekor használt séma verziója. A jelenleg támogatott séma verziója v1. Az érték módosítása nem támogatott, és a rendszer elutasítja a ConfigMap kiértékelése után.|
|`config-version` |Sztring | | A támogatja a konfigurációs fájl verziószámának nyomon követését a verziókövetés rendszerében/adattárában. A megengedett karakterek maximális száma 10, az összes többi karakter pedig csonkolt. |
|`[log_collection_settings.stdout] enabled =` |Logikai | true (igaz) vagy false (hamis) | Ez szabályozza, ha az stdout-tároló naplójának gyűjteménye engedélyezve van. Ha `true` értékre van állítva, és a rendszer nem zárja ki a névtereket az stdout log-gyűjteményhez (az alábbi`log_collection_settings.stdout.exclude_namespaces`-beállításnál), az stdout-naplók az összes tárolóból lesznek gyűjtve a fürt összes hüvelye/csomópontja között. Ha nincs megadva a ConfigMaps-ben, az alapértelmezett érték `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Sztring | Vesszővel tagolt tömb |Azon Kubernetes-névterek tömbje, amelyek esetében a rendszer nem gyűjti az stdout-naplókat. Ez a beállítás csak akkor érvényes, ha a `log_collection_settings.stdout.enabled` `true`ra van beállítva. Ha nincs megadva a ConfigMap-ben, az alapértelmezett érték `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Logikai | true (igaz) vagy false (hamis) |Ez szabályozza, hogy engedélyezve van-e a stderr-tároló naplójának gyűjtése. Ha `true` értékre van állítva, és a rendszer nem zárja ki az stdout log Collection (`log_collection_settings.stderr.exclude_namespaces` beállítás) névtereit, a rendszer az összes tárolóból gyűjti össze a stderr-naplókat a fürt összes hüvelye/csomópontjai között. Ha nincs megadva a ConfigMaps-ben, az alapértelmezett érték `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Sztring |Vesszővel tagolt tömb |Azon Kubernetes-névterek tömbje, amelyek esetében a rendszer nem gyűjti össze a stderr-naplókat. Ez a beállítás csak akkor érvényes, ha a `log_collection_settings.stdout.enabled` `true`ra van beállítva. Ha nincs megadva a ConfigMap-ben, az alapértelmezett érték `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Logikai | true (igaz) vagy false (hamis) | Ezzel a beállítással szabályozható a környezeti változók gyűjteménye a fürt összes hüvelye/csomópontjai között, és az alapértelmezett érték `enabled = true`, ha nincs megadva a ConfigMaps. Ha a környezeti változók gyűjteménye globálisan engedélyezve van, letilthatja egy adott tárolóra vonatkozóan, ha a környezeti `AZMON_COLLECT_ENV` változót **hamis** értékre állítja, vagy egy Docker beállítással vagy az **env:** szakaszban található [Pod konfigurációs fájljában](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) . Ha a környezeti változók gyűjteménye globálisan le van tiltva, akkor a gyűjtemény nem engedélyezhető egy adott tárolónál (azaz a tároló szintjén alkalmazható egyetlen felülbírálás a gyűjtemény letiltása, ha az már engedélyezve van a globálisan.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Logikai | true (igaz) vagy false (hamis) | Ezzel a beállítással szabályozható a tároló naplójának bővítése, hogy feltöltse a ContainerLog táblába írt összes naplóbejegyzés nevét és képtulajdonságának értékét a fürt összes tároló-naplójába. Alapértelmezés szerint `enabled = false`, ha nincs megadva a ConfigMap. |

A ConfigMaps egy globális lista, és csak egy ConfigMap alkalmazható az ügynökre. A gyűjtemények nem rendelkezhetnek más ConfigMaps.

## <a name="configure-and-deploy-configmaps"></a>A ConfigMaps konfigurálása és telepítése

A következő lépések végrehajtásával konfigurálja és telepítheti a ConfigMap konfigurációs fájlját a fürtön.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) a sablon ConfigMap YAML fájlt, és mentse azt tároló-Keresztesné Gréczi Ágnes-MS-agentconfig. YAML néven.  

2. Szerkessze a ConfigMap YAML-fájlt a testreszabásokkal az stdout, a stderr és/vagy a környezeti változók összegyűjtéséhez.

    - Ha ki szeretné zárni az stdout-naplók adott névtereit, a következő példa alapján konfigurálja a kulcsot/értéket: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - A környezeti változók egy adott tárolóhoz való letiltásához állítsa be a kulcs/érték `[log_collection_settings.env_var] enabled = true` a változó gyűjtemény globális engedélyezéséhez, majd kövesse az [itt](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) leírt lépéseket az adott tároló konfigurációjának befejezéséhez.
    
    - A stderr-naplók fürtre kiterjedő letiltásához konfigurálja a kulcsot/értéket a következő példa használatával: `[log_collection_settings.stderr] enabled = false`.

3. Hozzon létre ConfigMap a következő kubectl-parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások befejezésekor megjelenik egy üzenet, amely a következőhöz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése 

A konfiguráció sikeres alkalmazásának ellenőrzéséhez használja a következő parancsot a naplók áttekintéséhez egy ügynök pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Ha konfigurációs hibák vannak a omsagent hüvelyből, a kimenet az alábbihoz hasonló hibákat jelenít meg:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

A konfigurációs módosítások alkalmazásával kapcsolatos hibák a felülvizsgálathoz is elérhetők. A konfigurációs változások további hibaelhárításához a következő lehetőségek érhetők el:

- Egy Agent Pod-naplókból ugyanazzal a `kubectl logs` parancs használatával. 

- Élő naplókból. Az élő naplók az alábbihoz hasonló hibákat mutatnak:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- A Log Analytics munkaterület **KubeMonAgentEvents** táblájában. Az adatküldés óránként, a konfigurációs *hibák súlyossága miatt.* Ha nincsenek hibák, a táblázatban szereplő *bejegyzés súlyossági adatokkal*fog rendelkezni, ami nem jelent hibát. A **címkék** tulajdonság további információkat tartalmaz a pod és a Container azonosítóról, amelyen a hiba történt, valamint az első előfordulást, az utolsó előfordulást és a számlálást is az elmúlt órában.

Hibák miatt a omsagent nem elemezheti a fájlt, ezért az újraindítást és az alapértelmezett konfigurációt használja. Miután kijavította a hibát (ka) t a ConfigMap-ben, mentse a YAML-fájlt, és alkalmazza a frissített ConfigMaps a következő parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Frissített ConfigMap alkalmazása

Ha már telepített egy ConfigMap a fürtön, és egy újabb konfigurációval szeretné frissíteni, akkor szerkesztheti a korábban használt ConfigMap-fájlt, majd alkalmazhatja ugyanazt a parancsot, mint korábban, `kubectl apply -f <configmap_yaml_file.yaml`.

A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások befejezésekor megjelenik egy üzenet, amely a következőhöz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Séma verziójának ellenőrzése

A konfigurációs sémák támogatott verziói a omsagent Pod-on található Pod megjegyzésként (Schema-Versions) érhetők el. Ezeket a következő kubectl-paranccsal tekintheti meg: `kubectl describe pod omsagent-fdf58 -n=kube-system`

A kimenet az alábbihoz hasonlóan fog megjelenni a Megjegyzés sémája – verziók:

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

## <a name="next-steps"></a>Következő lépések

- A tárolók Azure Monitor nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre ajánlott riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások támogatásához

- A figyelés lehetővé teszi, hogy az AK-beli vagy hibrid fürtök és a rajtuk futó munkaterhelések állapotának és erőforrás-felhasználásának összegyűjtésével [hogyan használhatók](container-insights-analyze.md) a Azure monitor a tárolók számára.

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.
