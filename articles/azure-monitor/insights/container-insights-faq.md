---
title: Azure Monitor a tárolók gyakran ismételt kérdéseiről | Microsoft Docs
description: A tárolók Azure Monitor egy olyan megoldás, amely figyeli az AK-fürtök állapotát, és Container Instances az Azure-ban. Ez a cikk a gyakori kérdésekre ad választ.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: d3779a2d48db82bfccdc0f047119a36ef56c3bdf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477420"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor a tárolók számára – gyakori kérdések

Ez a Microsoft gyakori kérdések listája a Azure Monitor for containers szolgáltatással kapcsolatos gyakori kérdésekre mutat. Ha további kérdései vannak a megoldással kapcsolatban, látogasson el a [vitafórumra](https://feedback.azure.com/forums/34192--general-feedback) , és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Megtekinthetem a Grafana összegyűjtött mérőszámokat?

A tárolók Azure Monitor támogatja a Grafana-irányítópultokon a Log Analytics munkaterületen tárolt mérőszámok megtekintését. A Grafana [irányítópult-tárházból](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) letölthető sablon segítségével elsajátíthatja az első lépéseket, és megtudhatja, hogyan kérdezheti le a figyelt fürtök további adatait az egyéni Grafana-irányítópultok való megjelenítéshez. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Megfigyelhető az AK-motor fürtje Azure Monitor for containers használatával?

A tárolók Azure Monitor támogatja az Azure-ban üzemeltetett, AK-motor (korábbi nevén ACS-motor) fürt (ek) ben üzembe helyezett, a tároló munkaterheléseit. További részletek és áttekintés a forgatókönyv figyelésének engedélyezéséhez szükséges lépésekről: a [Azure monitor használata a tárolók számára az AK-motorhoz](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Miért nem láthatók a Log Analytics munkaterület adatai?

Ha a Log Analytics munkaterületen nem tud adatokat látni a mindennapi időpontokban, előfordulhat, hogy elérte az alapértelmezett 500 MB-os korlátot, vagy a naponta begyűjthető adatok mennyiségének szabályozására megadott napi korlátot. Ha a napi korlát teljesül, az adatgyűjtés csak a következő napon leáll, és folytatja a műveletet. Tekintse át az adatfelhasználást, és frissítsen egy másik díjszabási csomagra a várt használati szokások alapján: az [adatok használatának és költségének naplózása](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Mik a ContainerInventory táblában megadott tárolók állapotai?

A ContainerInventory tábla a leállított és futó tárolókkal kapcsolatos információkat tartalmaz. A tábla az ügynökön belüli munkafolyamattal van feltöltve, amely lekérdezi a Docker-t az összes tárolónál (futó és leállított), és továbbítja az adatokat a Log Analytics munkaterületen.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hogyan a *hiányzó előfizetés-regisztrációs* hiba elhárítása?

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

A következő hibaüzenet jelenhet meg: a **kérelemben megadott válasz URL-cím nem egyezik az alkalmazáshoz konfigurált válasz URL-címekkel: "< Application ID\>"** . A megoldás megoldása a következő cikkben található: a [tárolók információinak valós idejű megtekintése Azure monitor a tárolók](container-insights-livedata-setup.md#configure-ad-integrated-authentication)használatával. 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Miért nem tudom frissíteni a fürtöt a bevezetést követően?

Ha egy AK-fürthöz engedélyezte a Azure Monitort a tárolók számára, akkor törölje azt a Log Analytics-munkaterületet, amelyhez a fürt az adatokat küldi, amikor a fürt frissítésére tett kísérlet során sikertelen lesz. Ennek megkerüléséhez le kell tiltania a figyelést, majd újra engedélyeznie kell, hogy az előfizetés egy másik érvényes munkaterületre hivatkozik. Ha újra megpróbálja végrehajtani a fürt frissítését, akkor a folyamatnak sikeresen fel kell dolgoznia és el kell végeznie a műveletet.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Mely portokra és tartományokra van szükségem az ügynök megnyitásához/engedélyezési listához?

Tekintse meg a [hálózati tűzfalra vonatkozó követelményeket](container-insights-onboard.md#network-firewall-requirements) a proxy-és tűzfal-konfigurációs információkhoz, amelyek a tároló ügynökhöz szükségesek az Azure-ban, az Azure US governmentben és az Azure China felhőkben.

## <a name="next-steps"></a>További lépések

Az AK-fürt figyelésének megkezdéséhez tekintse át az [Azure monitor a tárolók számára című témakört](container-insights-onboard.md) , és Ismerje meg a figyelés engedélyezésének követelményeit és rendelkezésre álló módszereit. 
