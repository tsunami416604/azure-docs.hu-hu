---
title: Az Azure Kubernetes szolgáltatás (ak) vagy más Kubernetes üzemeltetett alkalmazások figyelése a Application Insights használatával – Azure Monitor | Microsoft Docs
description: A Azure Monitor a Kubernetes-fürtön a Service Mesh Technology, a Istio használatával biztosítja az alkalmazások figyelését bármely Kubernetes által üzemeltetett alkalmazás számára. Ez lehetővé teszi, hogy összegyűjtse Application Insights telemetria, amely a fürtben futó hüvelyek bejövő és kimenő kéréseire vonatkozik.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 4bb1af6ca2126b7ae58a6c836624ec78a071a5a5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075285"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Zero Instrumentation-alkalmazás figyelése Kubernetes által üzemeltetett alkalmazásokhoz Istio-elavult

> [!IMPORTANT]
> Ez a funkció jelenleg elavult, és a továbbiakban nem lesz támogatott a 2020. augusztus 1-től.
> Jelenleg a kód nélküli figyelés csak a [Java önálló ügynökön keresztül](./java-in-process-agent.md)engedélyezhető. Más nyelvek esetében az SDK-k segítségével figyelheti az alkalmazásokat az AK-ban: [ASP.net Core](./asp-net-core.md), [ASP.net](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md)és [Python](./opencensus-python.md).

A Azure Monitor mostantól kihasználja a Service Mesh Tech szolgáltatást a Kubernetes-fürtön, így biztosítva a Box-alkalmazások figyelését bármely Kubernetes üzemeltetett alkalmazáshoz. Az alapértelmezett alkalmazás-betekintési funkciókkal (például az [alkalmazás-hozzárendeléssel](../../azure-monitor/app/app-map.md) ) a függőségek modellezéséhez [élő metrikastream](../../azure-monitor/app/live-stream.md) a valós idejű figyeléshez, az [alapértelmezett irányítópulttal](../../azure-monitor/app/overview-dashboard.md), [metrikus Explorerrel](../../azure-monitor/platform/metrics-getting-started.md)és [munkafüzetekhez](../../azure-monitor/platform/workbooks-overview.md)tartozó hatékony vizualizációkkal. Ezzel a funkcióval a felhasználók a kiválasztott Kubernetes-névtérben lévő összes Kubernetes-munkafolyamaton belül érhetik el a szűk keresztmetszeteket és a meghibásodási pontokat. Ha a meglévő Service Mesh-beruházásokat a Istio hasonló technológiákkal szeretné kihasználni, Azure Monitor az alkalmazás kódjának módosítása nélkül lehetővé teszi az automatikus műszeres alkalmazások figyelését.

> [!NOTE]
> Ez a Kubernetes-alkalmazások figyelésének számos módja.A Kubernetes-ben üzemeltetett összes alkalmazást a [Application INSIGHTS SDK](../../azure-monitor/azure-monitor-app-hub.yml) -val is kialakíthatja, anélkül, hogy a szolgáltatás hálóját kellene használnia. A Kubernetes monitorozásához anélkül, hogy az alkalmazást egy SDK-val tenné, használhatja az alábbi módszert.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Kubernetes-fürt](../../aks/concepts-clusters-workloads.md).
- Konzol hozzáférése a fürthöz a *kubectl*futtatásához.
- [Alkalmazás-betekintési erőforrás](create-new-resource.md)
- Rendelkeznie kell egy szolgáltatás hálóval. Ha a fürtön nincs telepítve a Istio, megtudhatja, hogyan [telepítheti és használhatja a Istio az Azure Kubernetes Service-ben](../../aks/servicemesh-istio-install.md).

## <a name="capabilities"></a>Képességek

A Kubernetes által üzemeltetett alkalmazások nulla rendszerállapot-figyelési szolgáltatásával a következőket használhatja:

- [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
- [Élő stream metrikák](../../azure-monitor/app/live-stream.md)
- [Irányítópultok](../../azure-monitor/app/overview-dashboard.md)
- [Metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md)
- [Elosztott – nyomkövetés](../../azure-monitor/app/distributed-tracing.md)
- [Végpontok közötti tranzakciók figyelése](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>A telepítés lépései

A megoldás engedélyezéséhez végezze el a következő lépéseket:
- Telepítse az alkalmazást (ha még nincs telepítve).
- Győződjön meg arról, hogy az alkalmazás része a szolgáltatás hálójának.
- Begyűjtött telemetria megfigyelése.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Az alkalmazás konfigurálása a Service Mesh szolgáltatással való együttműködésre

A Istio két módszert támogat [egy Pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)kialakításához.
A legtöbb esetben a legegyszerűbb, ha megjelöli az alkalmazást tartalmazó Kubernetes-névteret a *istio-befecskendező* címkével:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Mivel a Service Mesh kiemeli az adatokat a huzalból, nem tudjuk feloldani a titkosított forgalmat. A fürtöt nem elhagyó forgalom esetén használjon titkosítatlan protokollt (például HTTP). A titkosítani kívánt külső adatforgalom esetében érdemes lehet beállítani a [TLS-leállítást](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) a beáramló vezérlőn.

A szolgáltatás hálóján kívül futó alkalmazások nem érintettek.

### <a name="deploy-your-application"></a>Az alkalmazás üzembe helyezése

- Az alkalmazás üzembe helyezése az *app-Namespace* névtérben. Ha az alkalmazás már telepítve van, és követte a fent ismertetett automatikus oldalkocsi-injektálási módszert, újra létre kell hoznia a hüvelyeket annak érdekében, hogy a Istio beinjektálja az oldalkocsit; kezdeményezheti a működés közbeni frissítést vagy az egyes hüvelyek törlését, és megvárhatja, hogy újra létre lehessen hozni őket.
- Győződjön meg arról, hogy az alkalmazás megfelel a [Istio követelményeinek](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Nulla rendszerállapot-figyelés üzembe helyezése a Kubernetes által üzemeltetett alkalmazásokhoz

1. [ *Application Insights-adapter* kiadásának](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)letöltése és kinyerése.
2. Navigáljon a */src/kubernetes/* a kiadási mappában.
3. Alkalmazás szerkesztése – *istio-mixer-adapter-Deployment. YAML*
    - szerkessze *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* környezeti változó értékét, hogy tartalmazza a Azure Portal Application Insights erőforrásának rendszerállapot-kulcsát, hogy tartalmazza a telemetria.
    - Ha szükséges, módosítsa *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* környezeti változó értékét úgy, hogy az tartalmazza a névterek vesszővel tagolt listáját, amely esetében engedélyezni szeretné a figyelést. Hagyja üresen az összes névtér figyelését.
4. Alkalmazza a *src/kubernetes/* YAML *összes* fájlját a következő futtatásával (még a */src/kubernetes/* belül kell lennie):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

- Application Insights adapter üzembe helyezésének ellenőrzése:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Bizonyos esetekben a finomhangolás finomhangolása szükséges. Az egyes Pod-telemetria belefoglalásához vagy kizárásához használja a *appinsights/monitoring. enabled* címkét az adott Pod-on. Ez elsőbbséget élvez az összes névtér-alapú konfigurációval szemben. Állítsa be a *appinsights/monitoring. enabled* értéket az *igaz* értékre, hogy tartalmazza a hüvelyt, és *false (hamis* ) értékre való kizárni.

### <a name="view-application-insights-telemetry"></a>Application Insights telemetria megtekintése

- Készítse elő a mintavételt az alkalmazáson, hogy ellenőrizze, a figyelés megfelelően működik-e.
- 3-5 percen belül el kell indítania a telemetria megjelenését a Azure Portalban. Ügyeljen rá, hogy a portálon tekintse meg az Application Insights-erőforrás *alkalmazás-hozzárendelési* szakaszát.

## <a name="troubleshooting"></a>Hibaelhárítás

Alább látható az a hibaelhárítási folyamat, amelyet akkor kell használni, ha a telemetria nem jelenik meg a Azure Portal a várt módon.

1. Győződjön meg arról, hogy az alkalmazás terhelés alatt van, és a kérelmek küldése és fogadása egyszerű HTTP-n keresztül történik. Mivel a telemetria le van állítva a huzalból, a titkosított forgalom nem támogatott. Ha nincsenek bejövő vagy kimenő kérelmek, akkor nem lesz telemetria sem.
2. Győződjön meg arról, hogy a *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* környezeti változóban van megadva a megfelelő kialakítási kulcs az *Application-ininsights-ISTIO-mixer-adapter-Deployment. YAML*. A rendszerállapot-kulcs a Azure Portal Application Insights erőforrásának *Áttekintés* lapján található.
3. Győződjön meg arról, hogy a megfelelő Kubernetes-névtér van megadva a *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* környezeti változóban az *Application-ininsight-ISTIO-mixer-adapter-Deployment. YAML*. Hagyja üresen az összes névtér figyelését.
4. Győződjön meg róla, hogy az alkalmazás hüvelyei a Istio által befecskendezve lettek. Győződjön meg arról, hogy a Istio oldalkocsija létezik az egyes Pod gépeken.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Ellenőrizze, hogy létezik-e a pod-on futó *istio-proxy* nevű tároló.

5. Tekintse meg az Application Insights adapter nyomkövetéseit.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A fogadott telemetria-elemek száma percenként frissül. Ha nem nő a percben, akkor a Istio nem kap telemetria az adapternek.
   Keresse meg az esetleges hibákat a naplóban.
6. Ha létrejött, hogy az Kubernetes-adapterhez *tartozó alkalmazás-betekintés* nem telemetria, tekintse meg a Istio keverő naplóit, és állapítsa meg, hogy miért nem küld adatokat az adapternek:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Keresse meg az esetleges hibákat, különösen az *applicationinsightsadapter* -adapterrel való kommunikációra vonatkozó adatokat.

## <a name="faq"></a>GYIK

A projekt előrehaladásával kapcsolatos legfrissebb információkért látogasson el a Istio- [keverő projekt GitHub-Application Insights adapterére](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Eltávolítás

A termék eltávolítása a *src/kubernetes/* Run alatt található *minden* YAML fájl esetében:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Monitor és a tárolók együttműködéséről, látogasson el [a Azure monitor for containers – áttekintés](../../azure-monitor/insights/container-insights-overview.md)
