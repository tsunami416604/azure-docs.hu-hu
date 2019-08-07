---
title: Azure Monitor a tárolók gyakran ismételt kérdéseiről | Microsoft Docs
description: A tárolók Azure Monitor egy olyan megoldás, amely figyeli az AK-fürtök állapotát, és Container Instances az Azure-ban. Ez a cikk a gyakori kérdésekre ad választ.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/02/2019
ms.author: magoedte
ms.openlocfilehash: 3644b40311c037df800eb89ca26d1285fbf1e082
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741511"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor a tárolók számára – gyakori kérdések

Ez a Microsoft gyakori kérdések listája a Azure Monitor for containers szolgáltatással kapcsolatos gyakori kérdésekre mutat. Ha további kérdései vannak a megoldással kapcsolatban, látogasson el a [vitafórumra](https://feedback.azure.com/forums/34192--general-feedback) , és tegye fel kérdéseit. Egy kérdést gyakran ismételt, amikor hozzáadjuk ehhez a cikkhez, hogy gyorsan és könnyen megtalálhatók.

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Megfigyelhető az AK-motor fürtje Azure Monitor for containers használatával?

A tárolók Azure Monitor támogatja az Azure-ban üzemeltetett, AK-motor (korábbi nevén ACS-motor) fürt (ek) ben üzembe helyezett, a tároló munkaterheléseit. További részletek és áttekintés a forgatókönyv figyelésének engedélyezéséhez szükséges lépésekről: a [Azure monitor használata a tárolók számára az AK-motorhoz](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Miért nem láthatók a Log Analytics munkaterület adatai?

Ha a Log Analytics munkaterületen nem tud adatokat látni a mindennapi időpontokban, előfordulhat, hogy elérte az alapértelmezett 500 MB-os korlátot, vagy a naponta begyűjthető adatok mennyiségének szabályozására megadott napi korlátot. Ha a napi korlát teljesül, az adatgyűjtés csak a következő napon leáll, és folytatja a műveletet. Tekintse át az adatfelhasználást, és frissítsen egy másik díjszabási csomagra a várt használati szokások alapján: az [adatok használatának és költségének naplózása](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Mik a ContainerInventory táblában megadott tárolók állapotai?

A ContainerInventory tábla a leállított és futó tárolókkal kapcsolatos információkat tartalmaz. A tábla az ügynökön belüli munkafolyamattal van feltöltve, amely lekérdezi a Docker-t az összes tárolónál (futó és leállított), és továbbítja az adatokat a Log Analytics munkaterületen.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hogyan a **hiányzó előfizetés-regisztrációs** hiba elhárítása?

Ha a **Microsoft. OperationsManagement előfizetés-regisztrációja hiányzik**, akkor a Microsoft. OperationsManagement erőforrás-szolgáltató regisztrálása az előfizetésben, ahol a munkaterület meg van adva, a **Microsoft.** . Ennek a dokumentációja [itt](../../azure-resource-manager/resource-manager-register-provider-errors.md)található.

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Támogatja a RBAC-kompatibilis AK-fürtöket?

A tároló-figyelési megoldás nem támogatja a RBAC, de a tárolók esetében Azure Monitor is támogatott. Előfordulhat, hogy a megoldás részleteit tartalmazó lap nem jeleníti meg a megfelelő információkat a fürtök adatait megjelenítő pengék között.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hogyan lehetővé teszi a naplók gyűjtését a Kube-System névtérben a Helm használatával?

Alapértelmezés szerint le van tiltva a Kube-rendszernévtérben lévő tárolók naplójának gyűjteménye. A omsagent egy környezeti változó beállításával engedélyezhető a naplók gyűjteménye. További információ: [Azure monitor for containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub oldal. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hogyan frissíteni a omsagent a legújabb kiadású verzióra?

Az ügynök frissítésének megismeréséhez tekintse meg az [ügynökök kezelése](container-insights-manage-agent.md)című témakört.

## <a name="how-do-i-enable-multi-line-logging"></a>Hogyan a többsoros naplózás engedélyezése?

A tárolók számára jelenleg Azure Monitor nem támogatja a többsoros naplózást, de vannak elérhető áthidaló megoldások. Az összes szolgáltatást JSON formátumban is konfigurálhatja, majd a Docker/Moby egyetlen sorba írja azokat.

Például becsomagolhatja a naplót JSON-objektumként, ahogy az alábbi példában is látható egy példa Node. js-alkalmazáshoz:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Az alábbi példához hasonlóan a naplók esetében a következő példa jelenik meg Azure Monitorban:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

A probléma részletes megtekintéséhez tekintse meg a következő GitHub- [hivatkozást](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Az élő naplók engedélyezésekor Hogyan az Azure AD-hibák elhárítása? 

A következő hibaüzenet jelenhet meg: A **kérelemben megadott válasz URL-cím nem egyezik az alkalmazáshoz konfigurált válasz URL-címekkel: "<\>Application id"** . A megoldás megoldása a következő cikkben található: a tárolók [valós idejű megtekintése Azure monitor a tárolók](container-insights-live-logs.md#configure-aks-with-azure-active-directory)használatával. 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Miért nem tudom frissíteni a fürtöt a bevezetést követően?

Ha egy AK-fürthöz engedélyezte a Azure Monitort a tárolók számára, akkor törölje azt a Log Analytics-munkaterületet, amelyhez a fürt az adatokat küldi, amikor a fürt frissítésére tett kísérlet során sikertelen lesz. Ennek megkerüléséhez le kell tiltania a figyelést, majd újra engedélyeznie kell, hogy az előfizetés egy másik érvényes munkaterületre hivatkozik. Ha újra megpróbálja végrehajtani a fürt frissítését, akkor a folyamatnak sikeresen fel kell dolgoznia és el kell végeznie a műveletet.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Mely portokra és tartományokra van szükségem az ügynök megnyitásához/engedélyezési listához?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *. blob.core.windows.net 443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>További lépések

Az AKS-fürt a figyelés megkezdése előtt tekintse át a [hogyan üzembe helyezni az Azure figyeli, hogy tárolók](container-insights-onboard.md) engedélyezése a figyelési követelmények és választható módszerek. 
