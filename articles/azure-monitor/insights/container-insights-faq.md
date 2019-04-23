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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 70712b68cc26a461f702850dbf6064b65b4ff130
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999722"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Az Azure Monitor-tárolókhoz gyakran ismételt kérdések

A Microsoft FAQ az összetevővel kapcsolatos gyakori kérdésekre az Azure Monitor tárolók listája. Ha a megoldásról a további kérdése van, lépjen a [fórum](https://feedback.azure.com/forums/34192--general-feedback) és felteheti kérdéseit. Egy kérdést gyakran ismételt, amikor hozzáadjuk ehhez a cikkhez, hogy gyorsan és könnyen megtalálhatók.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Miért nem látom adatokat a Log Analytics munkaterület?

Ha nem jelennek meg a Log Analytics-munkaterületet egy bizonyos idő mindennap, az adatok, lehet, hogy elérte a alapértelmezett 500 MB-os korlátot, vagy a napi korlát napi gyűjtendő adatok mennyisége szabályozásához. A korlát teljesül, a nap, amikor adatgyűjtés leáll, és folytatja, csak a következő napon. Tekintse át az adatokat, és frissíti a várható használati mintái alapján egy másik tarifacsomagra, lásd: [jelentkezzen be a használati adatok és](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Mik azok a tároló állapotok a ContainerInventory táblázatban megadott?

A ContainerInventory tábla leállított és a futó tárolók kapcsolatos információkat tartalmazza. A táblázat az ügynök, amely lekérdezi a docker számára az összes tárolót (fut, és leállt), és továbbítja az adatokat a Log Analytics-munkaterületen belül a munkafolyamat által van feltöltve.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hogyan oldhatom fel **hiányzik az előfizetés regisztrációjának** hiba?

Ha a hibaüzenetet kapja **Microsoft.OperationsManagement hiányzik az előfizetés regisztrációjának**, akkor is megoldhatja az erőforrás-szolgáltató regisztrálásával **Microsoft.OperationsManagement** a a előfizetés, ahol a munkaterület van definiálva. Ennek módjáról a dokumentációban található [Itt](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Nincs támogatási RBAC engedélyező AKS-fürtök esetében?

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

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hogyan oldhatom fel az Azure AD-hibák, amikor a engedélyezése élő naplók? 

A következő hiba jelenhet meg: **A válasz URL-címe a kérelemben megadott nem egyezik az alkalmazáshoz konfigurált válasz URL: "< Alkalmazásazonosító\>"**. A megoldás megoldhatja a problémát a cikkben található [tároló naplók valós időben az Azure Monitor szolgáltatással tárolók megtekintése](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Miért nem tudja frissíteni az előkészítés után fürt?

Miután engedélyezte az Azure Monitor-tárolókhoz az AKS-fürt, a Log Analytics-munkaterület törlése a fürt küldte-e az adatokat, a fürt frissítési kísérlet során nem fog működni. Ennek megoldásához be kell letiltani a figyelést, majd újra engedélyeznie azt egy másik érvényes munkaterületet az előfizetésében hivatkozik. Hajtsa végre újra a fürt frissítésének megkísérlésekor azt kell feldolgozni, és sikeresen befejeződik.  

## <a name="next-steps"></a>További lépések

Az AKS-fürt a figyelés megkezdése előtt tekintse át a [hogyan üzembe helyezni az Azure figyeli, hogy tárolók](container-insights-onboard.md) engedélyezése a figyelési követelmények és választható módszerek. 