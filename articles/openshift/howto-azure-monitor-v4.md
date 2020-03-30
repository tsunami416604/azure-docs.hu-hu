---
title: Azure Monitor-integráció az Azure Red Hat OpenShift 4.3-hoz
description: Megtudhatja, hogy miként engedélyezheti az Azure Monitort a Microsoft Azure Red Hat OpenShift-fürtön.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082846"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Monitor-integráció az Azure Red Hat OpenShift 4.3-hoz

> [!IMPORTANT] 
> Kérjük, vegye figyelembe, hogy az Azure Red Hat OpenShift 4.3 jelenleg csak privát előzetes verzióban érhető el az USA keleti részén. A privát előnézet elfogadása csak meghívással történik. Kérjük, regisztrálja az előfizetést, mielőtt megpróbálná engedélyezni ezt a funkciót: [Azure Red Hat OpenShift private preview registration](https://aka.ms/aro-preview-register)

> [!NOTE]
> Az előzetes verzió funkciói önkiszolgálóak, és a rendelkezésre álló mértékben érhetők el, és nem tartoznak a szolgáltatásiszint-szerződés (SLA) és a korlátozott jótállás hatálya alól. Ezért a funkciók nem éles használatra szántak.

Ez a cikk ismerteti, hogyan engedélyezheti az Azure Monitor privát előzetes verzióját az OpenShift 4.3-as fürtök tárolóihoz, amelyek et prem-en vagy bármilyen felhőalapú környezetben üzemeltetik. Ugyanezek az utasítások vonatkoznak az Azure Red Hat OpenShift (ARO) 4.3-as fürtjei figyelésének engedélyezésére is.  

## <a name="prerequisites"></a>Előfeltételek

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Kormányrúd 3](https://helm.sh/docs/intro/install/)
- Hozzáférés a kubernetes-fürt kubeconfigjához
- Hozzáférés egy Azure-előfizetéshez
- Hozzáférés az OpenShift 4.3-as fürthöz az Azure-figyelő tárolók sisakdiagramjának telepítéséhez
- Minimális közreműködői RBAC szerepkör-engedély az Azure-előfizetéshez  
- A figyelőügynök nek a következő kimenő portokra van szükség – és a tartományoknak el kell küldeniük a figyelési adatokat az Azure Monitor háttérrendszerébe (ha proxy/tűzfal blokkolja):
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Előkészítés

> [!TIP]
> A szkript bash 4 funkciókat használ, ezért győződjön meg róla, hogy a bash naprakész. A program ellenőrizheti `bash --version`az aktuális verziót a segítségével.

### <a name="download-the-onboarding-script"></a>A bevezetési parancsfájl letöltése

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Hajtsa végre a következő parancsfájlt az azureSubscriptionId, munkaterületi régióval, fürtnévvel és a Kubernetes-fürt környezetével.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Példa:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Ügynökadatgyűjtés konfigurálása

Alapértelmezés szerint a figyelési ügynök gyűjti a {stdout; stderr} tárolónaplókat az összes névtérben futó tárolókban, kivéve a kube-rendszert.  Ha az adott névtérre vagy névterekre vonatkozó tárolónapló-gyűjteményt szeretné konfigurálni, hivatkozhat a [Container Insights-ügynök konfigurációjára.](../azure-monitor/insights/container-insights-agent-config.md) Itt konfigurálhatja a figyelési ügynököt a kívánt adatgyűjtési beállításokkal a konfigurációs térkép használatával.

## <a name="configure-scraping-of-prometheus-metrics"></a>Prometheus mérőszámok kaparásának konfigurálása

Az Azure Monitor tárolók kaparja a Prometheus metrikákat, és az Azure Monitor háttérbefejezi. A Prometheus kaparás konfigurálásával kapcsolatos utasításokat a [Container Insights Prometheus konfigurációja](../azure-monitor/insights/container-insights-prometheus-integration.md) című dokumentumban találja.

Sikeres bevezetés után keresse meg a [Hibrid figyelés](https://aka.ms/azmon-containers-hybrid) gombot, és válassza a Környezet **"Minden"** lehetőséget az újonnan beszállt OpenShift v4-es fürt megtekintéséhez.

## <a name="disable-monitoring"></a>Monitorozás letiltása

Ha le szeretné tiltani a figyelést, törölheti az Azure Monitor tárolók helm diagram a következő parancs használatával leállíthatja a figyelési adatok gyűjtését és az Azure Monitor tárolók háttérrendszerbe.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Figyelés frissítése

Futtassa újra a bevezetési parancsfájlt a [Bevezetés](#onboarding) szakaszban leírtak szerint ugyanazzal a paraméterrel a legújabb Helm-diagramra való frissítéshez.

## <a name="after-successful-onboarding"></a>Sikeres bevezetés után

Keresse meg a [hibrid figyelés](https://aka.ms/azmon-containers-hybrid), és láthatja az újonnan engedélyezett OpenShift/ARO v4 fürt állapotú a **Figyelt fürtök** lapon. Itt a **Fürt** oszlopra kattintva mélyebb elemzéseket, például metrikákat, készleteket és naplókat érhet el.

## <a name="supported-features"></a>Támogatott funkciók

A támogatott funkciókról és funkciókról a [Container Insights áttekintése című témakörben olvashat bővebben.](../azure-monitor/insights/container-insights-overview.md)

Vegye fel askcoin@microsoft.com velünk a kapcsolatot keresztül visszajelzést és kérdéseket.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a figyelésről, olvassa el a következő témakört:
- [A Container Insights áttekintése](../azure-monitor/insights/container-insights-overview.md)

- [Lekérdezés naplózása – áttekintés](../azure-monitor/log-query/log-query-overview.md)
