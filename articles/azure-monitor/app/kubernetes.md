---
title: Az Application Insights segítségével figyelheti az Azure Kubernetes-szolgáltatást (AKS) vagy más Kubernetes-alkalmazásokat – Azure Monitor | Microsoft dokumentumok
description: Az Azure Monitor az Istio szolgáltatásháló-technológiát használja a Kubernetes-fürtön, hogy alkalmazásfigyelést biztosítson bármely Kubernetes üzemeltetett alkalmazáshoz. Ez lehetővé teszi, hogy a fürtben futó podok bejövő és kimenő kéréseire vonatkozó Application Insights-telemetriai adatok gyűjtése.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132346"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Nulla instrumentation alkalmazás figyelése Kubernetes üzemeltetett alkalmazások

> [!IMPORTANT]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure Monitor mostantól a Kubernetes-fürt szolgáltatásháló-technológiáját használja ki, hogy minden Kubernetes-üzemeltetett alkalmazásfigyelést biztosíthasson. Az alapértelmezett Application Insight funkciók, mint [például az Application Map](../../azure-monitor/app/app-map.md) modellezi a függőségek, [élő metrikák Stream](../../azure-monitor/app/live-stream.md) valós idejű figyelés, hatékony vizualizációk az alapértelmezett [irányítópult](../../azure-monitor/app/overview-dashboard.md), [Metric Explorer](../../azure-monitor/platform/metrics-getting-started.md), és [munkafüzetek.](../../azure-monitor/app/usage-workbooks.md) Ez a funkció segít a felhasználóknak a teljesítményszűk keresztmetszetek és a hibahotspotok észlelésében a Kubernetes-munkaterheléseikben a kiválasztott Kubernetes névtéren belül. Kihasználva a meglévő szolgáltatás háló beruházások technológiák, például istio, az Azure Monitor lehetővé teszi az automatikusan műszeres alkalmazásfigyelés módosítása nélkül az alkalmazás kódját.

> [!NOTE]
> Ez az egyik módja az alkalmazásfigyelés kubernetes végrehajtásához.A Kubernetes-ben üzemeltetett bármely alkalmazást is bevetheti az [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) használatával, anélkül, hogy egy szolgáltatáshálóra lenne szükség. A Kubernetes figyelése anélkül, hogy az alkalmazás egy SDK használhatja az alábbi módszert.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Kubernetes-fürt](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Konzolhozzáférést a fürthöz a *kubectl*futtatásához.
- [Alkalmazásinsight-erőforrás](create-new-resource.md)
- Van egy szolgáltatás háló. Ha a fürt nem rendelkezik Istio telepítve, megtudhatja, hogyan [telepítheti és használhatja az Istio az Azure Kubernetes szolgáltatás.](https://docs.microsoft.com/azure/aks/istio-install)

## <a name="capabilities"></a>Funkciók

A Kubernetes által üzemeltetett alkalmazások nulla instrumentation alkalmazásfigyelésének használatával a következőket használhatja:

- [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
- [Élő közvetítés mutatói](../../azure-monitor/app/live-stream.md)
- [Irányítópultok](../../azure-monitor/app/overview-dashboard.md)
- [Metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md)
- [Elosztott nyomkövetés](../../azure-monitor/app/distributed-tracing.md)
- [Végpontok között a tranzakciók figyelése](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>A telepítés lépései

A megoldás engedélyezéséhez a következő lépéseket hajtjuk végre:
- Telepítse az alkalmazást (ha még nem telepített).
- Győződjön meg arról, hogy az alkalmazás a szolgáltatásháló része.
- Figyelje meg az összegyűjtött telemetriai adatokat.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Az alkalmazás konfigurálása szolgáltatáshálóval való együttműködésre

Istio támogatja a kétféle [műszerek egy pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
A legtöbb esetben a legegyszerűbb az alkalmazást tartalmazó Kubernetes-névteret az *istio-injection* címkével megjelölni:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Mivel a szolgáltatásháló leemeli az adatokat a drótról, nem tudjuk elfogni a titkosított forgalmat. A forgalmat, amely nem hagyja el a fürtöt, használjon titkosítatlan protokollt (például HTTP). A titkosítandó külső forgalom esetén fontolja meg a [TLS-végződtetés beállítását](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) a be- és écses vezérlőnél.

A szolgáltatáshálón kívül futó alkalmazásokat ez nem érinti.

### <a name="deploy-your-application"></a>Az alkalmazás üzembe helyezése

- Telepítse az alkalmazást a *my-app-namespace* névtérbe. Ha az alkalmazás már telepítve van, és követte a fent leírt automatikus oldalkocsi-befecskendezési módszert, újra létre kell hoznia a hüvelyeket annak érdekében, hogy az Istio befecskendezze az oldalkocsiját; vagy kezdeményezzen egy működés közbeni frissítést, vagy törölje az egyes podokat, és várja meg, amíg újra létrejönnek.
- Győződjön meg arról, hogy az alkalmazás megfelel az [Istio követelményeinek.](https://istio.io/docs/setup/kubernetes/prepare/requirements/)

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Zéró instrumentációs alkalmazásfigyelés telepítése a Kubernetes által üzemeltetett alkalmazásokhoz

1. Az Application [ *Insights-adapter* kiadásának](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)letöltése és kibontása.
2. Keresse meg a */src/kubernetes/* kapcsolót a kiadási mappában.
3. *Alkalmazás-insights-istio-mixer-adapter-deployment.yaml szerkesztése*
    - ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY környezeti változó értékét *úgy,* hogy az Az Application Insights-erőforrás instrumentációs kulcsát tartalmazza az Azure Portalon a telemetriai adatokat.
    - Ha szükséges, szerkesztsd *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* környezeti változó értékét, hogy az vesszővel tagolt névterek listáját tartalmazza, amelyekfigyelést engedélyezni szeretné. Hagyja üresen az összes névtér figyeléséhez.
4. Alkalmazzon *minden* YAML fájlt, amely *az src/kubernetes/* alatt található, a következő futtatásával (még mindig a */src/kubernetes/ fájlban*kell lennie):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

- Győződjön meg arról, hogy az Application Insights-adapter telepítve van:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Bizonyos esetekben finomhangolásszükséges. Ha egy adott pod telemetriai adatait szeretné bevonni vagy kizárni a gyűjtésből, használja *az appinsights/monitoring.enabled* címkét az adott podon. Ez elsőbbséget élvez az összes névtér-alapú konfigurációval szemben. Állítsa be *az appinsights/monitoring.enabled értéket* *true* értékre a pod felvételéhez, és *hamisra,* hogy kizárja azt.

### <a name="view-application-insights-telemetry"></a>Az Application Insights telemetriai adatainak megtekintése

- Hozzon létre egy mintakérelmet az alkalmazás sal szemben, hogy ellenőrizze, hogy a figyelés megfelelően működik-e.
- 3–5 percen belül meg kell kezdenie az Azure Portalon megjelenő telemetriai adatok at. Győződjön meg róla, hogy tekintse meg az *Application Map* szakaszaz Application Insights-erőforrás a portálon.

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban a hibaelhárítási folyamat használata, ha telemetriai adatok nem jelennek meg az Azure Portalon a várt módon.

1. Győződjön meg arról, hogy az alkalmazás be van töltve, és egyszerű HTTP-n küld/fogad kéréseket. Mivel a telemetriai adatokat a rendszer leemeli a vezetékről, a titkosított forgalom nem támogatott. Ha nincsenek bejövő vagy kimenő kérelmek, akkor nem lesz telemetriai adatok sem.
2. Győződjön meg arról, hogy a megfelelő instrumentation kulcs a *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* környezeti változóban található *az application-insights-istio-mixer-adapter-deployment.yaml.* A műszerezési kulcs az Azure-portál Application Insights-erőforrás *áttekintése lapján* található.
3. Győződjön meg arról, hogy a megfelelő Kubernetes névtér található a *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* környezeti változó ban *application-insights-istio-mixer-adapter-deployment.yaml.* Hagyja üresen az összes névtér figyeléséhez.
4. Győződjön meg arról, hogy az alkalmazás podok már sidecar-injektált Istio. Ellenőrizze, hogy Istio oldalkocsija létezik-e minden egyes kabinon.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Ellenőrizze, hogy *van-e egy istio-proxy* nevű tároló a podon.

5. Tekintse meg az Application Insights-adapter nyomait.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A fogadott telemetriai elemek száma percenként egyszer frissül. Ha nem nő perc alatt perc alatt – nem telemetriai adatokat küld az adapterre Istio.
   Keresse meg a hibákat a naplóban.
6. Ha megállapítást nyert, hogy a *Kubernetes-adapterhez való Application Insight* nem kerül adagolásra, ellenőrizze az Istio Mixer naplóit, hogy megtudja, miért nem küld adatokat az adapterre:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Keresse meg a hibákat, különösen az *applicationinsightsadapter adapterrel* folytatott kommunikációval kapcsolatban.

## <a name="faq"></a>GYIK

A projekt előrehaladásával kapcsolatos legfrissebb információkért látogasson el az [Istio Mixer projekt GitHub-jához készült Application Insights-adapterre.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Eltávolítás

A termék eltávolításához *az* *src/kubernetes/* run alatt található minden YAML fájlhoz:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogyan működik együtt az Azure Monitor és a tárolók, látogasson el [az Azure Monitor tárolók áttekintésére](../../azure-monitor/insights/container-insights-overview.md)
