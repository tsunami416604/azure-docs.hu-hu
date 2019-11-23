---
title: Azure Monitor konfigurálása a tárolók Prometheus-integrációhoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Azure Monitor for containers agentet a Prometheus-mérőszámok az Azure Kubernetes Service-fürttel való kaparása céljából.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 51bdf0cfedb30fbd95f9a44e8f4a0efe4e857104
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514341"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>A Prometheus-metrikák Azure Monitor for containers szolgáltatással történő selejtezésének konfigurálása

A [Prometheus](https://prometheus.io/) egy népszerű, nyílt forráskódú metrikafigyelési megoldás, mely a [Cloud Native Compute Foundation](https://www.cncf.io/) része. A tárolók Azure Monitor zökkenőmentes bevezetési élményt biztosít a Prometheus-metrikák gyűjtéséhez. A Prometheus használatához általában egy Prometheus-kiszolgálót kell beállítania és kezelnie egy tárolóval. A Azure Monitor integrálásával a Prometheus-kiszolgáló nem szükséges. Csak a Prometheus mérőszámok végpontját kell közzétennie az exportőrökön vagy a hüvelyeken (alkalmazáson) keresztül, és a tárolók Azure Monitor számára a tárolók számára a mérőszámokat fel lehet kaparni. 

![A Container monitoring architektúra a Prometheus számára](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>A Prometheus-metrikák ciprod07092019 vagy újabb verziójának minimális ügynöki verziója támogatott, és az ügynök verziószáma a konfiguráció és az ügynök hibáinak írására támogatott a `KubeMonAgentEvents` táblában ciprod10112019. További információ az ügynök verziójáról és az egyes kiadásokról: [ügynök kibocsátási megjegyzései](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Az ügynök verziójának ellenőrzéséhez a **csomópont** lapon válasszon ki egy csomópontot, majd a Tulajdonságok panelen jegyezze fel az **ügynök Képcímke** tulajdonságának értékét.

### <a name="prometheus-scraping-settings"></a>A Prometheus-karcolás beállításai

A Prometheus-metrikák aktív kaparása a következő két szempont egyikével végezhető el:

* A fürtre kiterjedő HTTP URL-cím és a célok felderítése egy szolgáltatás listázott végpontjai alapján. Például a k8s-szolgáltatások, például a Kube-DNS és az Kube-State-metrika, valamint az alkalmazáshoz tartozó Pod-megjegyzések. Az ebben a kontextusban gyűjtött metrikák a *[Prometheus data_collection_settings. cluster]* ConfigMap szakaszban lesznek meghatározva.
* A teljes körű HTTP URL-cím és a célok felderítése egy szolgáltatás listázott végpontjai alapján. Az ebben a kontextusban gyűjtött metrikák a *[Prometheus_data_collection_settings. node]* ConfigMap szakaszban lesznek meghatározva.

| Végpont | Hatókör | Példa |
|----------|-------|---------|
| Pod-jegyzet | Fürtre kiterjedő | Széljegyzetek <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes szolgáltatás | Fürtre kiterjedő | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/végpont | Csomópontok és/vagy fürtök széles skálája | `http://myurl:9101/metrics` |

URL-cím megadása esetén a tárolók Azure Monitor csak a végpontot kaparják le. Ha a Kubernetes szolgáltatás meg van adva, a rendszer feloldja a szolgáltatás nevét a fürt DNS-kiszolgálójának használatával az IP-cím lekéréséhez, majd a feloldott szolgáltatás selejtét.

|Hatókör | Paraméter | Data type | Érték | Leírás |
|------|-----|-----------|-------|-------------|
| Fürtre kiterjedő | | | | Az alábbi három módszer egyikének megadásával adhatja meg a metrikák végpontjait. |
| | `urls` | Sztring | Vesszővel tagolt tömb | HTTP-végpont (a megadott IP-cím vagy érvényes URL-elérési út). Például: `urls=[$NODE_IP/metrics]`. ($NODE _IP a tárolók paraméterhez megadott Azure Monitor, és a csomópont IP-címe helyett használható. Csak nagybetűnek kell lennie.) |
| | `kubernetes_services` | Sztring | Vesszővel tagolt tömb | Kubernetes-szolgáltatások tömbje, amely az Kube-State-mérőszámokból származó mérőszámokat lekaparja. Például`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Logikai | true vagy false | Ha a `true`re van beállítva a teljes fürtre vonatkozó beállításokban, Azure Monitor a tárolók ügynöke a Kubernetes-hüvelyt a teljes fürtön a következő Prometheus-megjegyzésekhez fogja lekaparni:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Logikai | true vagy false | Engedélyezi a hüvely leselejtezését. a `monitor_kubernetes_pods` `true`re kell beállítani. |
| | `prometheus.io/scheme` | Sztring | http vagy https | Az alapértelmezett érték a HTTP-n keresztüli selejtezés. Ha szükséges, állítsa `https`ra. | 
| | `prometheus.io/path` | Sztring | Vesszővel tagolt tömb | A HTTP-erőforrás elérési útja, amelyből a mérőszámokat be kell olvasni. Ha a metrikák elérési útja nem `/metrics`, akkor ezt a jegyzetet adja meg. |
| | `prometheus.io/port` | Sztring | 9102 | Itt adhatja meg a kaparni kívánt portot. Ha a port nincs beállítva, az alapértelmezett érték 9102 lesz. |
| | `monitor_kubernetes_pods_namespaces` | Sztring | Vesszővel tagolt tömb | A Kubernetes hüvelyek mérőszámait tartalmazó névterek engedélyezési listája.<br> Például: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Csomópont szintű | `urls` | Sztring | Vesszővel tagolt tömb | HTTP-végpont (a megadott IP-cím vagy érvényes URL-elérési út). Például: `urls=[$NODE_IP/metrics]`. ($NODE _IP a tárolók paraméterhez megadott Azure Monitor, és a csomópont IP-címe helyett használható. Csak nagybetűnek kell lennie.) |
| Csomópont-vagy fürt szintű | `interval` | Sztring | 60s | A gyűjtési időköz alapértelmezett értéke egy perc (60 másodperc). A (z) *[prometheus_data_collection_settings. node]* és/vagy *[prometheus_data_collection_settings. cluster]* gyűjteményt a következő időegységekhez módosíthatja: s, m, h. |
| Csomópont-vagy fürt szintű | `fieldpass`<br> `fielddrop`| Sztring | Vesszővel tagolt tömb | Az engedélyezés (`fieldpass`) és a letiltási (`fielddrop`) lista beállításával megadhat bizonyos mérőszámokat, amelyeket nem a végpontból lehet gyűjteni. Először be kell állítania az engedélyezési listát. |

A ConfigMaps egy globális lista, és csak egy ConfigMap alkalmazható az ügynökre. A gyűjtemények nem rendelkezhetnek más ConfigMaps.

## <a name="configure-and-deploy-configmaps"></a>A ConfigMaps konfigurálása és telepítése

A következő lépések végrehajtásával konfigurálja és telepítheti a ConfigMap konfigurációs fájlját a fürtön.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) a sablon ConfigMap YAML fájlt, és mentse azt tároló-Keresztesné Gréczi Ágnes-MS-agentconfig. YAML néven.

2. Szerkessze a ConfigMap YAML-fájlját a saját testreszabásával, hogy kaparja a Prometheus-metrikákat.

    - A Kubernetes Services-fürt széles körű gyűjtéséhez konfigurálja a ConfigMap-fájlt a következő példa használatával.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - A Prometheus-metrikák a fürtben megadott URL-címekről történő leselejtezésének konfigurálásához konfigurálja a ConfigMap-fájlt a következő példán keresztül.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - A Prometheus-metrikák a fürt minden egyes csomópontja esetében történő Daemonset elemet konfigurálásához konfigurálja a következőt a ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP a tárolók paraméterének egy konkrét Azure Monitor, és a csomópont IP-címe helyett használható. Minden nagybetűnek kell lennie. 

    - A Prometheus-metrikák kaparása egy Pod-Megjegyzés megadásával konfigurálható a következő lépések végrehajtásával:

       1. A ConfigMap a következőket kell megadnia:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. A következő konfiguráció megadása a pod-megjegyzésekhez:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Ha szeretné korlátozni a figyelést olyan hüvelyek esetében, amelyek megjegyzésekkel rendelkeznek, például csak éles számítási feladatokhoz dedikált hüvelyeket tartalmazhatnak, állítsa a `monitor_kubernetes_pod` `true`re a ConfigMap-ben, és adja hozzá a névtér-szűrőt `monitor_kubernetes_pods_namespaces` a a bekaparni kívánt névterek. Például: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Hozzon létre ConfigMap a következő kubectl-parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások befejezésekor megjelenik egy üzenet, amely a következőhöz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" created`.

## <a name="applying-updated-configmap"></a>Frissített ConfigMap alkalmazása

Ha már telepített egy ConfigMap a fürtön, és egy újabb konfigurációval szeretné frissíteni, akkor szerkesztheti a korábban használt ConfigMap-fájlt, majd alkalmazhatja ugyanazt a parancsot, mint korábban, `kubectl apply -f <configmap_yaml_file.yaml`.

A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások befejezésekor megjelenik egy üzenet, amely a következőhöz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése 

A konfiguráció sikeres alkalmazásának ellenőrzéséhez használja a következő parancsot a naplók áttekintéséhez egy ügynök pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Ha konfigurációs hibák vannak a omsagent hüvelyből, a kimenet az alábbihoz hasonló hibákat jelenít meg:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

A konfigurációs módosítások alkalmazásával kapcsolatos hibák a felülvizsgálathoz is elérhetők. A következő lehetőségek állnak rendelkezésre a konfigurációs változások további hibaelhárításához és a Prometheus-metrikák leselejtezéséhez:

- Egy Agent Pod-naplókból ugyanazzal a `kubectl logs` parancs használatával. 

- Élő naplókból. Az élő naplók az alábbihoz hasonló hibákat mutatnak:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- A Log Analytics munkaterület **KubeMonAgentEvents** táblájában. A rendszer óránként küldi el az adatmennyiséget, *Figyelmeztetési* súlyossággal a selejti hibák miatt *, és a* konfigurációs hibák súlyosságát. Ha nincsenek hibák, a táblázatban szereplő *bejegyzés súlyossági adatokkal*fog rendelkezni, ami nem jelent hibát. A **címkék** tulajdonság további információkat tartalmaz a pod és a Container azonosítóról, amelyen a hiba történt, valamint az első előfordulást, az utolsó előfordulást és a számlálást is az elmúlt órában.

Hibák miatt a omsagent nem elemezheti a fájlt, ezért az újraindítást és az alapértelmezett konfigurációt használja. Miután kijavította a hibát (ka) t a ConfigMap-ben, mentse a YAML-fájlt, és alkalmazza a frissített ConfigMaps a következő parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="query-prometheus-metrics-data"></a>A Prometheus-metrikai adatok lekérdezése

A Azure Monitor és az ügynök által jelentett összes konfigurációs/leselejtezési hiba miatt lekapart Prometheus-metrikák megtekintéséhez tekintse át a [lekérdezési Prometheus mérőszámok adatait](container-insights-log-search.md#query-prometheus-metrics-data) és a [lekérdezési konfiguráció vagy a selejt hibáit](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Prometheus-metrikák megtekintése a Grafana-ben

A tárolók Azure Monitor támogatja a Grafana-irányítópultokon a Log Analytics munkaterületen tárolt mérőszámok megtekintését. A Grafana [irányítópult-tárházból](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) letölthető sablon segítségével elsajátíthatja az első lépéseket, és megtudhatja, hogyan kérdezheti le a figyelt fürtök további adatait az egyéni Grafana-irányítópultok megjelenítéséhez. 

## <a name="review-prometheus-data-usage"></a>A Prometheus-adatok használatának áttekintése

Ha meg szeretné állapítani, hogy az egyes mérőszámok mekkora mennyisége GB/nap, hogy magas legyen, a következő lekérdezés van megadva.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A kimenet az alábbihoz hasonló eredményeket fog megjeleníteni:

![Adatfeldolgozási kötet lekérdezési eredményeinek naplózása](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

![Adatfeldolgozási kötet lekérdezési eredményeinek naplózása](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Az adatfelhasználás és az elemzési költségek figyelésével kapcsolatos további információk a [használat és a költségek kezelése Azure monitor naplók használatával](../platform/manage-cost-storage.md)című témakörben találhatók.

## <a name="next-steps"></a>Következő lépések

További információ az stdout, a stderr és a környezeti változók ügynök-gyűjtési beállításainak konfigurálásáról a tároló munkaterhelései [között.](container-insights-agent-config.md) 