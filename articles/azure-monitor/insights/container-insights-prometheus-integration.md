---
title: Az Azure Monitor konfigurálása a Prometheus-integráció tárolóihoz | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan konfigurálhatja az Azure Monitor tárolók ügynök kaparja metrikák prometheus a Kubernetes-fürt.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: b774bf042778ca9118a7bc9f051655b200d87659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931423"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>A Prometheus-metrikák kaparásának konfigurálása az Azure Monitor tárolókkal

[A Prometheus](https://prometheus.io/) egy népszerű nyílt forráskódú metrikus monitorozási megoldás, és a [Cloud Native Compute Foundation](https://www.cncf.io/)része. Az Azure Monitor tárolók zökkenőmentes bevezetési élményt biztosít a Prometheus metrikák összegyűjtéséhez. A Prometheus használatához általában létre kell hoznia és kezelnie kell egy üzlettel rendelkező Prometheus kiszolgálót. Az Azure Monitorlal való integrációval nincs szükség prometheus kiszolgálóra. Csak meg kell adnia a Prometheus metrikák végpont az exportőrök vagy a podok (alkalmazás), és a tárolók az Azure Monitor tárolók számára a tárolók kaparja a metrikák at az Ön számára. 

![A Prometheus konténerfigyelési architektúrája](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>A Prometheus metrikák kaparásolásához támogatott minimális ügynökverzió a ciprod07092019 vagy `KubeMonAgentEvents` újabb, és a táblában a konfiguráció és az ügynökhibák írásához támogatott ügynökverzió a ciprod10112019. Az ügyintézői verziókról és az egyes kiadásokban szereplő információkról az [ügynökkiadási megjegyzések című témakörben talál](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)további információt. Az ügynök verziójának ellenőrzéséhez a **Csomópont** lapon jelöljön ki egy csomópontot, és a Tulajdonságok ablaktáblán az **Agent Image Tag** tulajdonság tulajdonságai jegyzetértékében.

A Prometheus metrikák kaparását a következő kubernetes-fürtök támogatják:

- Azure Kubernetes Service (AKS)
- Azure Container Instances
- Azure Stack vagy helyszíni
- Azure Red Hat OpenShift

>[!NOTE]
>Az Azure Red Hat OpenShift esetében egy sablon ConfigMap fájl jön létre az *openshift-azure-logging* névtérben. Nincs konfigurálva, hogy aktívan kaparja metrikák vagy adatgyűjtés az ügynök.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Az Azure Red Hat OpenShift előfeltételei

Mielőtt elkezdené, ellenőrizze, hogy az Azure Red Hat OpenShift-fürt ügyfélfürt-felügyeleti szerepkörének tagja-e a tárolóügynök és a Prometheus kaparási beállítások konfigurálásához. Annak ellenőrzéséhez, hogy tagja-e az *osa-customer-admins csoportnak, futtassa* a következő parancsot:

``` bash
  oc get groups
```

A kimenet a következőhöz hasonlít:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Ha tagja az *OSA-customer-admins csoportnak,* a következő `container-azm-ms-agentconfig` paranccsal listázhatja a ConfigMap-et:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A kimenet a következőhöz hasonlít:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Prometheus kaparás beállításai

A prometheus mérőszámainak aktív kaparása két nézőpont ból történik:

* Fürtszintű – HTTP URL-cím, és a szolgáltatások felsorolt végpontjaiból származó célok felderítése. Például k8s szolgáltatások, például a kube-dns és a kube-state-metrikák, és pod-annotations egy alkalmazásra jellemző. Az ebben a környezetben gyűjtött mérőszámok a ConfigMap szakaszban *[Prometheus data_collection_settings.cluster]* lesznek definiálva.
* Csomópont-szerte – HTTP URL-cím, és a szolgáltatások felsorolt végpontjaiból származó célok felderítése. Az ebben a környezetben gyűjtött mérőszámok a ConfigMap *szakaszban [Prometheus_data_collection_settings.node]* lesznek definiálva.

| Végpont | Hatókör | Példa |
|----------|-------|---------|
| Pod jegyzet | Fürtszintű | Széljegyzetek: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes szolgáltatás | Fürtszintű | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/végpont | Csomópontonként és/vagy fürtszintű | `http://myurl:9101/metrics` |

URL-cím megadása esetén az Azure-figyelő a tárolók csak kaparja a végpontot. Ha a Kubernetes szolgáltatás meg van adva, a szolgáltatás neve feloldódik a fürt DNS-kiszolgálójával az IP-cím leéséhez, majd a feloldott szolgáltatás kaparódik.

|Hatókör | Kulcs | Adattípus | Érték | Leírás |
|------|-----|-----------|-------|-------------|
| Fürtszintű | | | | Adja meg az alábbi három módszer egyikét a metrikák végpontjainak kaparására. |
| | `urls` | Sztring | Vesszővel tagolt tömb | HTTP-végpont (IP-cím vagy érvényes URL-elérési út megadva). Például: `urls=[$NODE_IP/metrics]`. ($NODE_IP egy adott Azure Monitor tárolók paraméter, és a csomópont IP-cím helyett használható. Az összes nagybetűsnek kell lennie.) |
| | `kubernetes_services` | Sztring | Vesszővel tagolt tömb | Kubernetes-szolgáltatások egy tömbje a kube-state-metrikák metrikák kaparására. `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`Például.|
| | `monitor_kubernetes_pods` | Logikai | true (igaz) vagy false (hamis) | Ha a `true` fürtszintű beállításokat, az Azure Monitor tárolók ügynök kaparja Kubernetes podok a teljes fürtön a következő Prometheus-jegyzetek:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Logikai | true (igaz) vagy false (hamis) | Lehetővé teszi a hüvely kaparását. `monitor_kubernetes_pods`a beállítását `true`a beállításra kell állítani. |
| | `prometheus.io/scheme` | Sztring | http vagy https | Alapértelmezés szerint a HTTP-n keresztüli selejtezés. Szükség esetén állítsa `https`a beállítását. | 
| | `prometheus.io/path` | Sztring | Vesszővel tagolt tömb | A HTTP-erőforrás elérési útja, amelyről metrikákat szeretne lekérni. Ha a metrikák elérési útja nem, `/metrics`adja meg ezzel a jegyzeteléssel. |
| | `prometheus.io/port` | Sztring | 9102 | Adja meg azt a portot, amelyből kaparni szeretne. Ha a port nincs beállítva, akkor az alapértelmezett érték 9102 lesz. |
| | `monitor_kubernetes_pods_namespaces` | Sztring | Vesszővel tagolt tömb | A névterek listájának lehetővé teszi a metrikák kaparását a Kubernetes-podokból.<br> Például: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Csomópontszintű | `urls` | Sztring | Vesszővel tagolt tömb | HTTP-végpont (IP-cím vagy érvényes URL-elérési út megadva). Például: `urls=[$NODE_IP/metrics]`. ($NODE_IP egy adott Azure Monitor tárolók paraméter, és a csomópont IP-cím helyett használható. Az összes nagybetűsnek kell lennie.) |
| Csomópont-szintű vagy fürtszintű | `interval` | Sztring | 60s | A gyűjtési időköz alapértelmezett beállítása egy perc (60 másodperc). A gyűjteményt a *[prometheus_data_collection_settings.node]* és/vagy *a [prometheus_data_collection_settings.cluster]* időegységekre módosíthatja, például s, m, h. |
| Csomópont-szintű vagy fürtszintű | `fieldpass`<br> `fielddrop`| Sztring | Vesszővel tagolt tömb | Megadhatja, hogy bizonyos metrikákat kell gyűjteni, vagy`fieldpass`nem a végpont`fielddrop`beállításával az allow ( ) és letiltotta ( ) lista. Először be kell állítania az engedélyezési listát. |

A ConfigMaps egy globális lista, és csak egy ConfigMap alkalmazható az ügynökre. Nem lehet másik ConfigMaps felülbírálja a gyűjtemények.

## <a name="configure-and-deploy-configmaps"></a>A ConfigMaps konfigurálása és telepítése

Hajtsa végre az alábbi lépéseket a ConfigMap konfigurációs fájl kubernetes-fürtökhöz való konfigurálásához.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) a sablonconfigMap yaml fájlt, és mentse a container-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Ez a lépés nem szükséges az Azure Red Hat OpenShift használatakor, mivel a ConfigMap sablon már létezik a fürtön.

2. A Prometheus mérőszámai kaparásításához szerkesztheti a ConfigMap yaml fájlt a testreszabásokkal. Ha az Azure Red Hat OpenShift ConfigMap yaml fájlját `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` szerkeszti, először futtassa a parancsot a fájl szövegszerkesztőben való megnyitásához.

    >[!NOTE]
    >Az egyeztetés megakadályozásának megakadályozása érdekében a következő jegyzetet `openshift.io/reconcile-protect: "true"` hozzá kell adni a *container-azm-ms-agentconfig* ConfigMap metaadatai hoz. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - A Kubernetes-szolgáltatások fürtszintű gyűjtéséhez konfigurálja a ConfigMap fájlt az alábbi példával.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - A Prometheus metrikáinak a fürt ön által imázsából történő kaparásának konfigurálásához konfigurálja a ConfigMap-fájlt a következő példával.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Ha a Prometheus metrikák kaparását szeretné konfigurálni egy ügynök DaemonSet-jéből a fürt minden egyes csomópontjára vonatkozóan, konfigurálja a következőket a ConfigMap-ben:
    
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
        >$NODE_IP egy adott Azure-figyelő tárolók paraméter, és a csomópont IP-címe helyett használható. Az egésznek nagybetűsnek kell lennie. 

    - A Prometheus metrikák kaparásának pod-jegyzet megadásával történő konfigurálásához hajtsa végre a következő lépéseket:

       1. A ConfigMap alkalmazásban adja meg a következőket:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Adja meg a pod-jegyzetek következő konfigurációját:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Ha a figyelést a jegyzeteket tartalmazó podok adott névtereire szeretné korlátozni, például `monitor_kubernetes_pod` csak `true` az éles számítási feladatokhoz dedikált `monitor_kubernetes_pods_namespaces` podokat szeretne, állítsa be a készletet a ConfigMap-ben, és adja hozzá a névtérszűrőt, amely megadja a kaparandó névtereket. Például: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Az Azure Red Hat OpenShift-en kívüli fürtök esetén futtassa a következő kubectl parancsot: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Az Azure Red Hat OpenShift esetén mentse a módosításokat a szerkesztőben.

A konfiguráció módosítása néhány percet is igénybe vehet, mielőtt életbe lépne, és a fürt összes omsagent podja újraindul. Az újraindítás egy folyamatos újraindítás az összes omsagent podok, nem minden újraindítás egy időben. Amikor az újraindítások befejeződtek, egy üzenet jelenik meg, amely `configmap "container-azm-ms-agentconfig" created`hasonló a következőhöz, és tartalmazza az eredményt: .

A frissített ConfigMap for Azure Red Hat OpenShift `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`a parancs futtatásával tekinthető meg. 

## <a name="applying-updated-configmap"></a>Frissített ConfigMap alkalmazása

Ha már telepített egy ConfigMap-et a fürtre, és újabb konfigurációval szeretné frissíteni, szerkesztheti a korábban használt ConfigMap-fájlt, majd ugyanazokat a parancsokat használhatja, mint korábban.

Az Azure Red Hat OpenShifttől eltérő Kubernetes-fürtök esetén futtassa a parancsot. `kubectl apply -f <configmap_yaml_file.yaml` 

Az Azure Red Hat OpenShift fürt, futtassa a parancsot, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` nyissa meg a fájlt az alapértelmezett szerkesztőben, hogy módosítsa, majd mentse azt.

A konfiguráció módosítása néhány percet is igénybe vehet, mielőtt életbe lépne, és a fürt összes omsagent podja újraindul. Az újraindítás egy folyamatos újraindítás az összes omsagent podok, nem minden újraindítás egy időben. Amikor az újraindítások befejeződtek, egy üzenet jelenik meg, amely `configmap "container-azm-ms-agentconfig" updated`hasonló a következőhöz, és tartalmazza az eredményt: .

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

A konfiguráció fürtre való sikeres alkalmazásának ellenőrzéséhez használja a következő parancsot `kubectl logs omsagent-fdf58 -n=kube-system`az ügynöki pod naplóinak áttekintéséhez: . 

>[!NOTE]
>Ez a parancs nem alkalmazható az Azure Red Hat OpenShift fürtre.
> 

Ha az omsagent podok konfigurációs hibákat tartalmaznak, a kimenet a következőhöz hasonló hibákat jelenít meg:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

A konfigurációs módosítások alkalmazásával kapcsolatos hibák is ellenőrizhetővé vehetők. A következő lehetőségek érhetők el a konfigurációs módosítások további hibaelhárításához és a Prometheus metrikák kaparásának elvégzéséhez:

- Ügynöki pod naplókból ugyanazzal `kubectl logs` a paranccsal 
    >[!NOTE]
    >Ez a parancs nem alkalmazható az Azure Red Hat OpenShift fürtre.
    > 

- Élő adatokból (előzetes verzió). A Live Data (előzetes verzió) naplók az alábbihoz hasonló hibákat mutatnak:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- A **KubeMonAgentEvents** táblából a Log Analytics-munkaterületen. Az adatok óránként küldik *a figyelmeztetés* súlyossága a kaparás hibák és *a hiba* súlyossága konfigurációs hibák. Ha nincsenek hibák, a táblában szereplő bejegyzés súlyossági *adatokat tartalmaz,* amelyek nem jelentenek hibákat. A **Címkék** tulajdonság további információkat tartalmaz a pod és a tároló azonosítóját, amelyen a hiba történt, valamint az első előfordulás, utolsó előfordulása és száma az elmúlt órában.

- Az Azure Red Hat OpenShift, ellenőrizze az omsagent naplók a **ContainerLog-táblában,** hogy ellenőrizze, ha az openshift-azure-naplózás naplógyűjteménye engedélyezve van.

A hibák megakadályozzák, hogy az omsagent elemezze a fájlt, ami újraindítást és az alapértelmezett konfiguráció használatát eredményezi. Miután kijavította a hibát(oka)t a ConfigMap-ben az Azure Red Hat OpenShift-től eltérő fürtökön, mentse a yaml fájlt, és alkalmazza a frissített ConfigMaps-ot a következő parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml`. 

Az Azure Red Hat OpenShift esetén a következő paranccsal `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`szerkesztheti és mentheti a frissített ConfigMaps-ot: .

## <a name="query-prometheus-metrics-data"></a>Prometheus-mérőszámok adatainak lekérdezése

Az Azure Monitor által lekapart prometheus metrikák és az ügynök által jelentett konfigurációs/kaparási hibák megtekintéséhez tekintse át a [Query Prometheus metrikák adatait](container-insights-log-search.md#query-prometheus-metrics-data) és [a Query konfigurációs vagy kaparási hibáit.](container-insights-log-search.md#query-config-or-scraping-errors)

## <a name="view-prometheus-metrics-in-grafana"></a>Prometheus mérőszámok megtekintése a Grafanában

Az Azure Monitor tárolók támogatja a Grafana irányítópultokon a Log Analytics-munkaterületen tárolt metrikák megtekintését. A kezdéshez egy sablont is betölthet a Grafana [irányítópult-tárházáról,](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) és hivatkozást nyújtunk be, amely segítséget nyújt a figyelt fürtök további adatainak lekérdezéséhez az egyéni Grafana-irányítópultokon való megjelenítéshez. 

## <a name="review-prometheus-data-usage"></a>Prometheus adathasználat áttekintése

Az egyes metrikák méretének töltési kötetének azonosításához GB-ban naponta, hogy megértse, magas-e, a következő lekérdezés érhető el.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A kimenet a következőhöz hasonló eredményeket jelenít meg:

![Adatbetöltési kötet lekérdezési eredményeinek naplózása](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Ha meg szeretné becsülni, hogy az egyes metrikák mérete GB-ben egy hónapig, hogy megértsük, ha a munkaterületen fogadott adatok mennyisége magas, a következő lekérdezés érhető el.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

A kimenet a következőhöz hasonló eredményeket jelenít meg:

![Adatbetöltési kötet lekérdezési eredményeinek naplózása](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Az adathasználat figyelésével és a költségek elemzésével kapcsolatos további információk az [Azure Monitor-naplók segítségével](../platform/manage-cost-storage.md)a Használat és a költségek kezelése című részben találnak további információt.

## <a name="next-steps"></a>További lépések

Az ügynökgyűjtemény beállításainak konfigurálásáról a tárolószámítási feladatokból az stdout, stderr és környezeti változók számára [itt olvashat bővebben.](container-insights-agent-config.md) 
