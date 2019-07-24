---
title: Azure Monitor konfigurálása a containers Agent adatgyűjtéshez | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Azure Monitor for containers Agent az stdout/stderr és a környezeti változók naplózási gyűjteményének vezérléséhez.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 12010aaa7bc90bd200264549ad3efb79f46576c6
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867642"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Az ügynök adatgyűjtésének konfigurálása a tárolók számára Azure Monitor

A tárolók Azure Monitor az stdout, a stderr és a környezeti változókat gyűjtik az Azure Kubernetes Service-ben (ak) üzemeltetett felügyelt Kubernetes-fürtökön üzembe helyezett tároló munkaterhelésekről a tároló ügynökből. Ez az ügynök az idősoros adatokat (más néven metrikákat) is összegyűjtheti a Prometheus-ből a tároló ügynök használatával anélkül, hogy egy Prometheus-kiszolgálót és-adatbázist kellene beállítania és kezelnie. Az ügynök adatgyűjtési beállításainak konfigurálásához létrehozhat egy egyéni Kubernetes-ConfigMaps a felhasználói élmény szabályozása érdekében. 

Ez a cikk bemutatja, hogyan hozhat létre ConfigMap, és hogyan konfigurálhatja az adatgyűjtést a követelmények alapján.

>[!NOTE]
>A Prometheus támogatása jelenleg a nyilvános előzetes verzióban érhető el.
>

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>A fürt konfigurálása egyéni adatgyűjtési beállításokkal

A sablon ConfigMap fájlja lehetővé teszi, hogy egyszerűen szerkessze a testreszabásokkal anélkül, hogy teljesen létre kellene hoznia. A Kezdés előtt tekintse át a [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) Kubernetes dokumentációját, és ismerkedjen meg a ConfigMaps létrehozásával, konfigurálásával és üzembe helyezésével. Ez lehetővé teszi a stderr és az stdout szűrését a névtérben vagy a teljes fürtön, valamint a fürtben található összes hüvely/csomóponton futó tároló környezeti változóit.

>[!IMPORTANT]
>Az ügynök minimális verziója támogatja az stdout, a stderr és a környezeti változók begyűjtését a tároló munkaterhelésekről ciprod06142019 vagy újabb verzióra. A Prometheus-metrikák ciprod07092019 vagy újabb verziójának minimális ügynök-verziója támogatott. Az ügynök verziójának ellenőrzéséhez a **csomópont** lapon válasszon ki egy csomópontot, majd a Tulajdonságok panelen jegyezze fel az **ügynök Képcímke** tulajdonságának értékét.  

### <a name="overview-of-configurable-data-collection-settings"></a>Konfigurálható adatgyűjtési beállítások áttekintése

Az alábbi beállításokkal konfigurálhatja az adatgyűjtés vezérlését.

|Kulcs |Adattípus |Value |Leírás |
|----|----------|------|------------|
|`schema-version` |Karakterlánc (megkülönbözteti a kis-és nagybetűket) |v1 |Ez az ügynök által a ConfigMap elemzésekor használt séma verziója. A jelenleg támogatott séma verziója v1. Az érték módosítása nem támogatott, és a rendszer elutasítja a ConfigMap kiértékelése után.|
|`config-version` |Sztring | | A támogatja a konfigurációs fájl verziószámának nyomon követését a verziókövetés rendszerében/adattárában. A megengedett karakterek maximális száma 10, az összes többi karakter pedig csonkolt. |
|`[log_collection_settings.stdout] enabled =` |Logikai | igaz vagy hamis | Ez szabályozza, ha az stdout-tároló naplójának gyűjteménye engedélyezve van. Ha a értékre van állítva `true` , és a rendszer nem zárja ki a névtereket az stdout log-gyűjteményhez (`log_collection_settings.stdout.exclude_namespaces` az alábbi beállításnál), az stdout-naplók az összes tárolóból lesznek összegyűjtve a fürt összes hüvelye/csomópontjai között. Ha nincs megadva a ConfigMaps-ben, az alapértelmezett `enabled = true`érték:. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Sztring | Vesszővel tagolt tömb |Azon Kubernetes-névterek tömbje, amelyek esetében a rendszer nem gyűjti az stdout-naplókat. Ez a beállítás csak akkor érvényes `log_collection_settings.stdout.enabled` , ha a `true`be van állítva. Ha nincs megadva a ConfigMap-ben, az alapértelmezett `exclude_namespaces = ["kube-system"]`érték:.|
|`[log_collection_settings.stderr] enabled =` |Logikai | igaz vagy hamis |Ez szabályozza, hogy engedélyezve van-e a stderr-tároló naplójának gyűjtése. Ha a értékre van állítva `true` , és a rendszer nem zárja ki a névtereket az stdout log Collection (`log_collection_settings.stderr.exclude_namespaces` beállítás) számára, a rendszer az összes tárolóból gyűjti össze a stderr-naplókat a fürt összes hüvelye/csomópontjai között. Ha nincs megadva a ConfigMaps-ben, az alapértelmezett `enabled = true`érték:. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Karakterlánc |Vesszővel tagolt tömb |Azon Kubernetes-névterek tömbje, amelyek esetében a rendszer nem gyűjti össze a stderr-naplókat. Ez a beállítás csak akkor érvényes `log_collection_settings.stdout.enabled` , ha a `true`be van állítva. Ha nincs megadva a ConfigMap-ben, az alapértelmezett `exclude_namespaces = ["kube-system"]`érték:. |
| `[log_collection_settings.env_var] enabled =` |Logikai | igaz vagy hamis | Ez a beállítás azt szabályozza, hogy engedélyezve van-e a környezeti változók gyűjteménye. Ha a értékre `false`van állítva, a rendszer nem gyűjt környezeti változókat a fürt összes hüvelyén vagy csomópontjain futó tárolóhoz. Ha nincs megadva a ConfigMap-ben, az alapértelmezett `enabled = true`érték:. |

## <a name="overview-of-configurable-prometheus-scraping-settings"></a>Konfigurálható Prometheus-lekaparás beállításainak áttekintése

A Prometheus-metrikák aktív kaparása a következő két szempont egyikével végezhető el:

* A fürtre kiterjedő HTTP URL-cím és a célok felderítése egy szolgáltatás felsorolt végpontjai közül, a k8s-szolgáltatások, például a Kube-DNS és a Kube-mérőszámok, valamint az alkalmazáshoz tartozó Pod-megjegyzések. Az ebben a kontextusban gyűjtött metrikák a *[Prometheus data_collection_settings. cluster]* ConfigMap szakaszban lesznek meghatározva.
* A teljes körű HTTP URL-cím és a célok felderítése egy szolgáltatás listázott végpontjai alapján. Az ebben a kontextusban gyűjtött metrikák a *[Prometheus_data_collection_settings. node]* ConfigMap szakaszban lesznek meghatározva.

|Scope | Kulcs | Adattípus | Érték | Leírás |
|------|-----|-----------|-------|-------------|
| Fürtre kiterjedő | | | | Az alábbi három módszer egyikének megadásával adhatja meg a metrikák végpontjait. |
| | `urls` | Sztring | Vesszővel tagolt tömb | HTTP-végpont (a megadott IP-cím vagy érvényes URL-elérési út). Például: `urls=[$NODE_IP/metrics]`. ($NODE a _IP egy adott Azure Monitor a tárolók paraméterhez, és a csomópontok IP-címe helyett lehet használni. Csak nagybetűnek kell lennie.) |
| | `kubernetes_services` | Karakterlánc | Vesszővel tagolt tömb | Kubernetes-szolgáltatások tömbje, amely az Kube-State-mérőszámokból származó mérőszámokat lekaparja. Például`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`:.|
| | `monitor_kubernetes_pods` | Logikai | igaz vagy hamis | Ha a a `true` fürtre kiterjedő beállításokban van beállítva, Azure monitor a tárolók ügynöke a Kubernetes-hüvelyt a teljes fürtön megkarcolja a következő Prometheus-megjegyzésekkel:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Logikai | igaz vagy hamis | Engedélyezi a hüvely leselejtezését. |
| | `prometheus.io/scheme` | Sztring | http vagy https | Az alapértelmezett érték a HTTP-n keresztüli selejtezés. Ha szükséges, állítsa a `https`következőre:. | 
| | `prometheus.io/path` | Karakterlánc | Vesszővel tagolt tömb | A HTTP-erőforrás elérési útja, amelyből a mérőszámokat be kell olvasni. Ha a metrikák elérési útja `/metrics`nem, adja meg ezt a jegyzetet. |
| | `prometheus.io/port` | Sztring | 9102 | Itt adhatja meg a figyelni kívánt portot. Ha a port nincs beállítva, az alapértelmezett érték 9102 lesz. |
| Csomópont szintű | `urls` | Karakterlánc | Vesszővel tagolt tömb | HTTP-végpont (a megadott IP-cím vagy érvényes URL-elérési út). Például: `urls=[$NODE_IP/metrics]`. ($NODE a _IP egy adott Azure Monitor a tárolók paraméterhez, és a csomópontok IP-címe helyett lehet használni. Csak nagybetűnek kell lennie.) |
| Csomópont-vagy fürt szintű | `interval` | Sztring | 60s | A gyűjtési időköz alapértelmezett értéke egy perc (60 másodperc). A (z) *[prometheus_data_collection_settings. node]* és/vagy *[prometheus_data_collection_settings. cluster]* gyűjteményt a következő időegységekhez módosíthatja: NS, US (vagy Âμs), MS, s, m, h. |
| Csomópont-vagy fürt szintű | `fieldpass`<br> `fielddrop`| Sztring | Vesszővel tagolt tömb | Az engedélyezés (`fieldpass`) és a tiltás (`fielddrop`) listaelem beállításával megadhat bizonyos mérőszámokat, amelyeket nem a végpontból lehet gyűjteni. Először be kell állítania az engedélyezési listát. |

A ConfigMap egy globális lista, és csak egy ConfigMap alkalmazható az ügynökre. A gyűjtemények nem rendelkezhetnek más ConfigMap.

### <a name="configure-and-deploy-configmaps"></a>A ConfigMaps konfigurálása és telepítése

A következő lépések végrehajtásával konfigurálja és telepítheti a ConfigMap konfigurációs fájlját a fürtön.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) a sablon ConfigMap YAML fájlt, és mentse azt tároló-Keresztesné Gréczi Ágnes-MS-agentconfig. YAML néven.  
1. Szerkessze a ConfigMap YAML-fájlt a testreszabott beállításokkal.

    - Ha ki szeretné zárni az stdout log Collection adott névtereit, a kulcs/érték konfigurálásához a következő példát `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`kell használni:.
    - A környezeti változók egy adott tárolóhoz való letiltásához állítsa be a kulcs `[log_collection_settings.env_var] enabled = true` /érték beállítást a változó globális begyűjtésének engedélyezéséhez, majd kövesse az [itt](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) leírt lépéseket az adott tároló konfigurációjának befejezéséhez.
    - A stderr-naplók fürtre kiterjedő letiltásához konfigurálja a kulcsot/értéket a következő példa használatával `[log_collection_settings.stderr] enabled = false`:.

1. Hozzon létre ConfigMap a következő kubectl-parancs `kubectl apply -f <configmap_yaml_file.yaml>`futtatásával:.
    
    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások végeztével megjelenik egy üzenet, amely az alábbihoz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" created`.

A konfiguráció sikeres alkalmazásának ellenőrzéséhez használja a következő parancsot a naplók áttekintéséhez egy ügynök pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Ha konfigurációs hibák vannak a omsagent hüvelyből, a kimenet az alábbihoz hasonló hibákat jelenít meg:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

A Prometheus-re vonatkozó konfigurációs változások alkalmazásával kapcsolatos hibák is megtekinthetők.  Vagy az ügynök Pod naplóiból ugyanazzal `kubectl logs` a paranccsal vagy élő naplókból. Az élő naplók az alábbihoz hasonló hibákat jelenítenek meg:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Hibák miatt a omsagent nem elemezheti a fájlt, ezért az újraindítást és az alapértelmezett konfigurációt használja. Miután kijavította a hibát (ka) t a ConfigMap-ben, mentse a YAML-fájlt, és alkalmazza a frissített `kubectl apply -f <configmap_yaml_file.yaml`ConfigMaps a következő parancs futtatásával:.

## <a name="applying-updated-configmap"></a>Frissített ConfigMap alkalmazása

Ha már telepített egy ConfigMap a fürtön, és egy újabb konfigurációval szeretné frissíteni, egyszerűen szerkesztheti a korábban használt ConfigMap-fájlt, majd alkalmazhatja ugyanazt a parancsot, `kubectl apply -f <configmap_yaml_file.yaml`mint korábban.

A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások végeztével megjelenik egy üzenet, amely az alábbihoz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Séma verziójának ellenőrzése

A konfigurációs sémák támogatott verziói a omsagent Pod-on található Pod megjegyzésként (Schema-Versions) érhetők el. Ezeket a következő kubectl-paranccsal tekintheti meg:`kubectl describe pod omsagent-fdf58 -n=kube-system`

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

## <a name="review-prometheus-data-usage"></a>A Prometheus-adatok használatának áttekintése

Ha meg szeretné állapítani, hogy az egyes mérőszámok mekkora mennyisége GB/nap, hogy magas legyen, a következő lekérdezés van megadva.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A kimenet az alábbihoz hasonló eredményeket fog megjeleníteni:

![Adatfeldolgozási kötet lekérdezési eredményeinek naplózása](./media/container-insights-agent-config/log-query-example-usage-03.png)

Ha azt szeretné megbecsülni, hogy a GB-ban hány metrikai méret van egy hónapig, hogy megtudja, a munkaterületen fogadott adatok mennyisége magas-e, a következő lekérdezés van megadva.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

A kimenet az alábbihoz hasonló eredményeket fog megjeleníteni:

![Adatfeldolgozási kötet lekérdezési eredményeinek naplózása](./media/container-insights-agent-config/log-query-example-usage-02.png)

Az adatfelhasználás és az elemzési költségek figyelésével kapcsolatos további információk a [használat és a költségek kezelése Azure monitor naplók használatával](../platform/manage-cost-storage.md)című témakörben találhatók.

## <a name="next-steps"></a>További lépések

A tárolók Azure Monitor nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre ajánlott riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások támogatásához

- Megtudhatja, hogyan kell használni az Azure Monitor és egyéb monitorozhatja az AKS-fürt a folytatáshoz tekintse meg a [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.