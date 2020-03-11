---
title: Azure Monitor integráció az Azure Red Hat OpenShift 4,3
description: Megtudhatja, hogyan engedélyezheti a Azure Monitort a Microsoft Azure Red Hat OpenShift-fürtön.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082846"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Monitor integráció az Azure Red Hat OpenShift 4,3

> [!IMPORTANT] 
> Vegye figyelembe, hogy az Azure Red Hat OpenShift 4,3 jelenleg csak privát előzetes verzióban érhető el az USA keleti régiójában. A privát előzetes verzió elfogadása csak meghívóval történik. Ügyeljen arra, hogy regisztrálja az előfizetését a funkció engedélyezése előtt: [Azure Red Hat OpenShift Private Preview regisztráció](https://aka.ms/aro-preview-register)

> [!NOTE]
> Az előzetes verziójú funkciók önkiszolgálást biztosítanak, és a rendelkezésre álló és elérhető módon érhetők el, és nem tartoznak a szolgáltatói szerződéssel (SLA) és a korlátozott jótállással. A funkciók ezért nem használhatók éles környezetben.

Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for containers for OpenShift 4,3-fürtök privát előzetes verziója a helyszíni vagy bármilyen felhőalapú környezetben. Ugyanezek az utasítások vonatkoznak az Azure Red Hat OpenShift (ARO) 4,3-fürtök figyelésének engedélyezésére is.  

## <a name="prerequisites"></a>Előfeltételek

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [3. Helm](https://helm.sh/docs/intro/install/)
- Hozzáférés a kubernetes-fürt kubeconfig
- Hozzáférés egy Azure-előfizetéshez
- Hozzáférés a OpenShift 4,3-fürthöz a Azure Monitor for containers Helm-diagram telepítéséhez
- Az Azure-előfizetéshez tartozó minimális közreműködői RBAC szerepkör engedélyezése  
- A figyelési ügynöknek a következő kimenő portokra és tartományokra van szüksége ahhoz, hogy a figyelési adatforrásokat a Azure Monitor háttérbe küldje (ha a proxy/tűzfal letiltotta):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Előkészítés

> [!TIP]
> A szkript bash 4 funkciót használ, ezért győződjön meg róla, hogy a bash naprakész. Az aktuális verziót `bash --version`segítségével is megtekintheti.

### <a name="download-the-onboarding-script"></a>A bevezetési parancsfájl letöltése

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Hajtsa végre a következő parancsfájlt a azureSubscriptionId, a munkaterület-régióval, a clusterName és a Kubernetes-fürt környezetével.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Például:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Az ügynök adatgyűjtésének konfigurálása

Alapértelmezés szerint a monitorozási ügynök a Kube kivételével az összes névtérben futó tároló ({StdOut; stderr}) tároló naplóit gyűjti.  Ha az adott névtérhez vagy névterekhez tartozó tároló-naplózási gyűjteményt szeretné konfigurálni, tekintse meg a [Container-bepillantási ügynök konfigurációját](../azure-monitor/insights/container-insights-agent-config.md). Itt konfigurálhatja a figyelési ügynököt a konfigurációs Térkép használatával a kívánt adatgyűjtési beállításokkal.

## <a name="configure-scraping-of-prometheus-metrics"></a>A Prometheus-metrikák selejtezésének konfigurálása

A tárolók Azure Monitor a Prometheus-metrikákat, és a Azure Monitor háttérbe kerülnek. A Prometheus-lekaparás konfigurálására vonatkozó utasításokért tekintse meg a [Container bepillantást tartalmazó Prometheus-konfigurációt](../azure-monitor/insights/container-insights-prometheus-integration.md) .

A sikeres előkészítést követően navigáljon a [hibrid monitorozás](https://aka.ms/azmon-containers-hybrid) elemre, és válassza a környezet lehetőséget az újonnan előkészített OpenShift **v4-fürt** megtekintéséhez.

## <a name="disable-monitoring"></a>Monitorozás letiltása

Ha le szeretné tiltani a figyelést, törölheti a Azure Monitor for containers Helm diagramot a következő parancs használatával, amelyekkel leállíthatja a figyelési adatok gyűjtését és betöltését a tárolók háttérbeli Azure Monitor.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Frissítés figyelése

Futtassa újra a bevezetési parancsfájlt a [bevezetési szakaszban](#onboarding) leírtak szerint, a legújabb Helm-diagramra való frissítéshez pedig ugyanazzal a paraméterrel.

## <a name="after-successful-onboarding"></a>Sikeres előkészítés után

Navigáljon a [hibrid monitorozás](https://aka.ms/azmon-containers-hybrid)elemre, és a **figyelt fürtök** lapon megtekintheti az újonnan engedélyezett OpenShift/ARO v4-fürtöt állapottal. A **fürt** oszlopra kattintva mélyebb elemzéseket kaphat, például mérőszámokat, leltározást és naplókat.

## <a name="supported-features"></a>Támogatott funkciók

További információ a támogatott szolgáltatásokról és funkciókról: a [Container-információk áttekintése](../azure-monitor/insights/container-insights-overview.md).

Visszajelzések és kérdések a askcoin@microsoft.comon keresztül léphetnek kapcsolatba velünk.

## <a name="next-steps"></a>Következő lépések

A figyeléssel kapcsolatos további tudnivalókért tekintse meg a következő témakört:
- [A Container-információk áttekintése](../azure-monitor/insights/container-insights-overview.md)

- [A napló lekérdezésének áttekintése](../azure-monitor/log-query/log-query-overview.md)
