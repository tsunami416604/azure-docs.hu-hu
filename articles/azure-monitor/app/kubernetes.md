---
title: Az Azure Monitor – Application Insights szolgáltatást a Kubernetes a háló Istio |} A Microsoft Docs
description: A Kubernetes Application Insights egy figyelési megoldás, amely lehetővé teszi a bejövő és kimenő kérelmek, illetve onnan nevű szolgáltatás háló technológia felhasználásával a Kubernetes-fürtöt futtató podok tartozó Application Insights telemetriai adatok gyűjtésére Istio.
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: f3b278c2678542ec127c1c644cc0267622ca39fa
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870687"
---
# <a name="application-insights-for-kubernetes-with-service-mesh"></a>Az Application Insights a Kubernetes-szolgáltatás háló

> [!IMPORTANT]
> Az Application Insights szolgáltatás háló keresztül kubernetes jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Monitor mostantól service háló tech a Kubernetes-fürtön kívül, a box alkalmazás figyelési üzemeltetett Kubernetes-alkalmazásaiban biztosít használja. Alapértelmezett Application Insights-funkciókat, mint [Alkalmazástérkép](../../azure-monitor/app/app-map.md) modellezésére a függőségek [élő metrikák Stream](../../azure-monitor/app/live-stream.md) valós idejű monitorozásra, a sokoldalú vizualizációkat a [alapértelmezett Irányítópult](../../azure-monitor/app/overview-dashboard.md), [Metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md), és [munkafüzetek](../../azure-monitor/app/usage-workbooks.md). Ez a funkció az összes kijelölt Kubernetes névtéren belül Kubernetes felskálázhatják felhasználók helyszíni teljesítmény szűk keresztmetszetei és a hiba elérési pontokhoz történő segítségével. A meglévő service háló beruházások technológiákkal, mint a Istio eseménykézbesítést, az Azure Monitor lehetővé teszi alkalmazások automatikus kialakítva monitorozása az alkalmazás kódjában, módosítás nélkül.

> [!NOTE]
> Ez az alkalmazás figyelése a Kubernetes szolgáltatásban számos módon egyik. Bármely olyan alkalmazásban, a Kubernetes használatával üzemeltetett is is kialakíthat a [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) szolgáltatás rácsvonal szükségessége nélkül. Anélkül, hogy az alkalmazás egy használható SDK-val való műszerezéséről monitorozható a Kubernetes az alábbi metódust.

## <a name="prerequisites"></a>Előfeltételek

- A [Kubernetes-fürt](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Konzol elérését a fürtön futtatni *kubectl*.
- Egy [Application Insights-erőforrás](create-new-resource.md)
- A szolgáltatás rácsvonal rendelkezik. Ha a fürt nem rendelkezik telepített Istio, megtudhatja hogyan [telepítheti és használhatja az Azure Kubernetes Service Istio](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funkciók

Az üzemeltetett Kubernetes-alkalmazáshoz az Application Insights használatával fogja tudni használni:

- [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
- [Élő Stream-metrikák](../../azure-monitor/app/live-stream.md)
- [Irányítópultok](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Elosztott nyomkövetést](../../azure-monitor/app/distributed-tracing.md)
- [Teljes körű tranzakciók figyelése](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Telepítési lépések

Engedélyezheti a megoldást, azt fogja hajt végre az alábbi lépéseket:
- (Ha még nincs telepítve vannak), helyezze üzembe az alkalmazást.
- Győződjön meg arról, az alkalmazás a szolgáltatás-háló része.
- Figyelje meg az összegyűjtött telemetriai adatokat.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Állítsa be alkalmazását rácsvonal szolgáltatás használata a

Istio támogatja a kétféle módon [való műszerezéséről podot](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
A legtöbb esetben azt a legegyszerűbb, jelölje meg a Kubernetes-névtér, amely tartalmazza az alkalmazás a *istio-injektálási* címkét:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Mivel a háló liftek szolgáltatásadatok kapcsolja ki a hálózaton, azt nem intercept a titkosított forgalmat. A fürt nem hagyja a forgalom egy titkosítatlan protokollt (például HTTP) használja. A külső forgalmat, amely titkosítva kell lennie, fontolja meg [beállítása az SSL-lezárást](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) , a bejövőforgalom-vezérlőt.

A szolgáltatás-hálón kívül futó alkalmazások nem érinti.

### <a name="deploy-your-application"></a>Az alkalmazás üzembe helyezése

- Az alkalmazás üzembe helyezéséhez *saját-alkalmazás – namespace* névtér. Ha az alkalmazás már telepítve van, és követte a fent leírt automatikus oldalkocsi injektálási módszer, akkor létre kell hoznia podok Istio kódtárba az oldalkocsi; biztosításához vagy a működés közbeni frissítés kezdeményezése vagy egyéni podok törlése, és várjon, amíg újra létre kell hozni.
- Győződjön meg arról, az alkalmazás megfelel-e az [Istio követelmények](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-application-insights-for-kubernetes"></a>Az Application Insights Kubernetes üzembe helyezése

1. Töltse le és csomagolja ki egy [ *Kubernetes készült Application Insights* kiadási](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navigáljon a */src/kubernetes/* a kiadási mappán belül.
3. Edit *application-insights-istio-mixer-adapter-deployment.yaml*
    - az érték szerkesztése *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* környezeti változó tartalmazza a kialakítási kulcsot, az Application Insights-erőforrást az Azure Portalon, a telemetriai adatokat tartalmazza.
    - Szükség esetén szerkessze értékét *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* környezeti változót, amelynek szeretné engedélyezni a figyelést névterek vesszővel elválasztott listáját tartalmazza. Hagyja üresen a mezőt az összes névterek figyelése.
4. Alkalmazása *minden* alatt található fájl YAML *src/kubernetes/* a következő futtatásával (belül továbbra is kell */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-application-insights-for-kubernetes-deployment"></a>Ellenőrizze az Application Insights a Kubernetes-telepítéshez

- Ellenőrizze, hogy az Application Insights Kubernetes adapter van telepítve:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Bizonyos esetekben finomhangoló hangolása szükség. Vagy egy egyéni pod telemetriai kizárja a gyűjtött, használjon *appinsights/monitoring.enabled* a pod címkéje. Ez elsőbbséget élveznek az összes névtér-konfigurációt. Állítsa be *appinsights/monitoring.enabled* való *igaz* a pod tartalmazza, az *hamis* ki.

### <a name="view-application-insights-telemetry"></a>Az Application Insights telemetria megtekintése

- Hozzon létre egy minta kérelmet, győződjön meg arról, hogy figyelése megfelelően működik-e az alkalmazás.
- 3. és 5 percen belül akkor kezdenek telemetriai adatok jelennek meg az Azure Portalon. Ügyeljen arra, hogy tekintse meg a *Alkalmazástérkép* szakaszában az Application Insights-erőforrást a portálon.

## <a name="troubleshooting"></a>Hibaelhárítás

Alább a hibaelhárítási folyamat során a telemetriai adatok nem jelenik meg, mint az Azure Portalon használandó várható.

1. Győződjön meg arról, az alkalmazást terhelés alatt áll és a küldő/fogadó egyszerű HTTP kérelmeket. Telemetria kikapcsolása az átviteli van feloldását, mivel a titkosított forgalom nem támogatott. Ha nincsenek bejövő vagy kimenő kérelmek, kell nem működik a telemetria vagy.
2. Győződjön meg arról, a megfelelő kialakítási kulcs megtalálható az *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* környezeti változót *application-insights-istio-mixer-adapter-deployment.yaml*. A kialakítási kulcs megtalálható az *áttekintése* lapján az Application Insights-erőforrást az Azure Portalon.
3. Győződjön meg arról, a megfelelő Kubernetes-névtér van megadva a *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* környezeti változót *application-insights-istio-mixer-adapter-deployment.yaml*. Hagyja üresen a mezőt az összes névterek figyelése.
4. Győződjön meg arról, az alkalmazás podok oldalkocsi beszúrta Istio által lett-e. Gondoskodjon arról, hogy a Istio oldalkocsi egyes pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Győződjön meg arról, hogy nevű tárolóban *istio-proxy* a podot futtat.

5. Nézet *Kubernetes készült Application Insights* adapter nyomkövetéseket.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A fogadott telemetriát elemek száma percenként egyszer frissülnek. Ha azt nem növelhető perc - percnyi nem működik a telemetria az adapter által küldött Istio.
   Keresse meg a naplóban szereplő hibák.
6. Ha megállapította, hogy *Application Insights a Kubernetes* adapter nincs folyamatban megváltoztat telemetriai adatokat, ellenőrizze a Istio a Mixer naplókat, döntse el, miért, nem küld adatokat a adapter:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Keresse meg a hibákat, különösen vonatkozó folytatott kommunikáció *applicationinsightsadapter* adapter.

## <a name="faq"></a>GYIK

A projekt előrehaladását legújabb információkért látogasson el a [Istio Mixer projekt Githubhoz készült Application Insights adapter](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Eltávolítás

A termék eltávolítása a *minden* alatt található fájl YAML *src/kubernetes/* futtatása:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>További lépések

További információ az Azure Monitor és tárolók együttműködésének látogatás [tárolók áttekintése az Azure Monitor](../../azure-monitor/insights/container-insights-overview.md)