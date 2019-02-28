---
title: A tárolók gyakran ismételt kérdések az Azure Monitor |} A Microsoft Docs
description: A tárolók az Azure Monitor olyan megoldás, amely az AKS-fürt és az Azure Container Instances állapotát figyeli. Ez a cikk gyakori kérdésekre ad választ.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: 250bf6ede3a54b26b6e9c68850f8e7260020002e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960503"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Az Azure Monitor-tárolókhoz gyakran ismételt kérdések
A Microsoft FAQ az összetevővel kapcsolatos gyakori kérdésekre az Azure Monitor tárolók listája. Ha a megoldásról a további kérdése van, lépjen a [fórum](https://feedback.azure.com/forums/34192--general-feedback) és felteheti kérdéseit. Egy kérdést gyakran ismételt, amikor hozzáadjuk ehhez a cikkhez, hogy gyorsan és könnyen megtalálhatók.

## <a name="i-am-unable-to-see-any-data-in-the-log-analytics-workspace-at-a-certain-time-everyday-how-do-i-resolve-this"></a>Nem jelennek meg a Log Analytics-munkaterület az adatok bizonyos időpontban mindennapi vagyok. Hogyan oldhatom fel ez? 

Előfordulhat, hogy elérte a alapértelmezett 500 MB-os korlátot, vagy a napi korlát napi gyűjtendő adatok mennyisége szabályozásához. A korlát teljesül, a nap, amikor adatgyűjtés leáll, és folytatja, csak a következő napon. Tekintse át az adatokat, és frissíti a várható használati mintái alapján egy másik tarifacsomagra, lásd: [jelentkezzen be a használati adatok és](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Mik azok a tartományvezérlők állapotait az tárolók a ContainerInventory táblázatban megadott?
A ContainerInventory tábla leállított és a futó tárolók kapcsolatos információkat tartalmazza. A táblázat az ügynök, amely lekérdezi a docker számára az összes tárolót (fut, és leállt), és továbbítja az adatokat a Log Analytics-munkaterületen belül a munkafolyamat által van feltöltve.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Hogyan oldhatom fel kapcsolódó hibák **Microsoft.OperationsManagement hiányzik az előfizetés regisztrációjának**?
Az erőforrás-szolgáltató regisztrálása a hiba elhárításához **Microsoft.OperationsManagement** az előfizetésben, ahol a munkaterület van definiálva. Ennek módjáról a dokumentációban található [Itt](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Az Azure Monitor-tárolókhoz tartalmaz támogatási RBAC engedélyező AKS-fürtök esetében?
A Tárolómonitorozási megoldás nem támogatja az RBAC, de van támogatott, és az Azure Monitor for containers szolgáltatásban. A megoldást ismertető lapon lehet, hogy nem jelenik meg a megfelelő információt, amely az ilyen fürtök adatok megjelenítése a paneleket.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hogyan engedélyezhetem a kube rendszer névtér keresztül Helm-tárolókhoz naplógyűjtés?
A tárolók a kube rendszer névtér a naplógyűjtő alapértelmezés szerint le van tiltva. Naplók gyűjtése az omsagent a környezeti változók beállításával is engedélyezhetők. További információkért lásd: a [-tárolókhoz az Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub-oldalon. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hogyan frissíthetem a omsagent a legújabb elérhető verzióra?
Az ügynök frissítésével kapcsolatban lásd: [ügynökfelügyeleti](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Hogyan engedélyezhetem többsoros naplózási?
Jelenleg az Azure Monitor-tárolókhoz nem támogatja a többsoros naplózás, de nincsenek elérhető megkerülő megoldásokat. Írhat JSON formátumban összes szolgáltatás konfigurálható, és majd Docker/Moby fog kiírni, azokat egy vonal jelöli.

Például akkor futtathatja a napló JSON-objektumként a minta node.js-alkalmazás az alábbi példában látható módon:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Ezeket az adatokat a naplók az Azure monitorban az alábbi példához hasonlóan fog kinézni, lekérdezheti, ha:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

A probléma részletes tekintse meg, tekintse át a következő [github-hivatkozás](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Hogyan oldhatom fel az Azure Active Directory-hibák, amikor a engedélyezése élő naplók? 
A következő hiba jelenhet meg: **A válasz URL-címe a kérelemben megadott nem egyezik az alkalmazáshoz konfigurált válasz URL: "< Alkalmazásazonosító\>"**. A megoldás megoldhatja a problémát a cikkben található [tároló naplók valós időben az Azure Monitor szolgáltatással tárolók megtekintése](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>További lépések
Az AKS-fürt a figyelés megkezdése előtt tekintse át a [hogyan üzembe helyezni az Azure figyeli, hogy tárolók](container-insights-onboard.md) engedélyezése a figyelési követelmények és választható módszerek. 