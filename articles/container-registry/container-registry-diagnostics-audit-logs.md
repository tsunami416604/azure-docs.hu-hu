---
title: Erőforrás-naplók & gyűjtése
description: Az Azure Container Registry, például a hitelesítés, a képküldés és a képek lekéréséhez szükséges erőforrás-naplózási események rögzítése és elemzése.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 72d03149cd24636ba2086dfaaff0dbba16d30f1e
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748000"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz

Ez a cikk azt ismerteti, hogyan gyűjthetők be az Azure Container Registry naplózási adatai a [Azure monitor](../azure-monitor/overview.md)szolgáltatásainak használatával. A Azure Monitor [erőforrás-naplókat](../azure-monitor/platform/platform-logs-overview.md) (korábbi néven *diagnosztikai naplókat*) gyűjt a beállításjegyzékben lévő felhasználó által vezérelt eseményekhez. Gyűjtse össze és használja fel ezeket az adatokat az igények kielégítéséhez, például:

* A beállításjegyzék hitelesítési eseményeinek naplózása a biztonság és a megfelelőség biztosítása érdekében 

* Adjon meg egy teljes tevékenység-nyomvonalat a beállításjegyzék összetevőiről, például a lekéréses és lekéréses eseményekről, hogy diagnosztizálja a beállításjegyzék működési problémáit 

Az erőforrás-naplózási adatok Azure Monitor használatával történő összegyűjtése további költségeket eredményezhet. Lásd: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/). 


> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások](#preview-limitations) érvényesek. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

A rendszerképekhez és egyéb összetevőkhöz tartozó adattár-szintű események naplózása jelenleg a következő:

* **Leküldéses események**
* **Lekéréses események**
* **Jelölését események**
* **Események törlése** (beleértve a tárház törlési eseményeit)

A jelenleg nem naplózott adattár-szintű események: kiürítési események.

## <a name="registry-resource-logs"></a>Beállításjegyzék-erőforrások naplói

Az erőforrás-naplók olyan Azure-erőforrások által kibocsátott információkat tartalmaznak, amelyek leírják a belső műveleteket. Az Azure Container Registry esetében a naplók a következő táblázatokban tárolt hitelesítési és adattár-szintű eseményeket tartalmazzák. 

* **ContainerRegistryLoginEvents** – a beállításjegyzék hitelesítési eseményei és állapota, beleértve a bejövő identitást és IP-címet is
* **ContainerRegistryRepositoryEvents** – például leküldéses és lekéréses műveletek a beállításjegyzékbeli adattárakban található képekhez és egyéb összetevőkhöz
* A **AzureMetrics** - a [tároló beállításjegyzékének mérőszámait](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) , például az összesített leküldéses és lekérési számlálókat.

A műveletek esetében a naplózási adatok a következők:
  * Sikeres vagy sikertelen állapot
  * Kezdő és záró időbélyegző

Az Azure az erőforrás-naplók mellett egy [tevékenységi naplót](../azure-monitor/platform/platform-logs-overview.md)is biztosít, amely az Azure felügyeleti eseményeinek egyetlen előfizetési szintű rekordja, például egy tároló-beállításjegyzék létrehozása vagy törlése.

## <a name="enable-collection-of-resource-logs"></a>Erőforrás-naplók gyűjtésének engedélyezése

A tároló-beállításjegyzék erőforrás-naplófájljainak gyűjteménye alapértelmezés szerint nincs engedélyezve. Explicit módon engedélyezze a diagnosztikai beállításokat minden figyelni kívánt beállításjegyzékhez. A diagnosztikai beállítások engedélyezésének lehetőségeiért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák gyűjtéséhez az Azure-ban](../azure-monitor/platform/diagnostic-settings.md).

Ha például egy tároló-beállításjegyzék naplóit és metrikáit szeretné megtekinteni a Azure Monitor közel valós időben, Gyűjtse össze az erőforrás-naplókat egy Log Analytics munkaterületen. A diagnosztikai beállítás engedélyezése a Azure Portal használatával:

1. Ha még nem rendelkezik munkaterülettel, hozzon létre egy munkaterületet a [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)használatával. Az adatgyűjtés késleltetésének csökkentése érdekében ügyeljen arra, hogy a munkaterület ugyanabban a **régióban** legyen, mint a tároló-beállításjegyzék.
1. A portálon válassza ki a beállításjegyzéket, és válassza a **figyelés > diagnosztikai beállítások > diagnosztikai beállítás hozzáadása**elemet.
1. Adja meg a beállítás nevét, majd kattintson a **küldés log Analytics**lehetőségre.
1. Válassza ki a beállításjegyzék diagnosztikai naplóihoz tartozó munkaterületet.
1. Válassza ki a gyűjteni kívánt naplózási adatokat, majd kattintson a **Mentés**gombra.

Az alábbi ábrán egy beállításjegyzék diagnosztikai beállításainak létrehozása látható a portál használatával.

![Diagnosztikai beállítások engedélyezése](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Gyűjtsön csak a szükséges adatokat, kiegyensúlyozza a költségeket és a figyelési igényeket. Ha például csak a hitelesítési események naplózása szükséges, csak a **ContainerRegistryLoginEvents** -naplót válassza ki. 

## <a name="view-data-in-azure-monitor"></a>Azure Monitorban lévő adatmegjelenítés

Miután engedélyezte a diagnosztikai naplók gyűjtését Log Analyticsban, eltarthat néhány percig, amíg az adat megjelenik a Azure Monitorban. Ha meg szeretné tekinteni az adatait a portálon, válassza ki a beállításjegyzéket, és válassza a **figyelés > naplók**lehetőséget. Válassza ki az egyik táblázatot, amely a beállításjegyzék adatait tartalmazza. 

Lekérdezések futtatása az adatmegjelenítéshez. Több minta lekérdezés van megadva, vagy saját maga futtathatja. A következő lekérdezés például lekéri a legutóbbi 24 órás adatmennyiséget a **ContainerRegistryRepositoryEvents** táblából:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Az alábbi képen a minta kimenet látható:

![Naplóadatok lekérdezése](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

A Azure Portal Log Analytics használatáról szóló oktatóanyagért lásd: [Bevezetés a Azure Monitor log Analytics használatába](../azure-monitor/log-query/get-started-portal.md), vagy próbálja ki a log Analytics [bemutató környezetét](https://portal.loganalytics.io/demo). 

A naplók lekérdezésével kapcsolatos további információkért lásd: [a Azure monitorban található naplók áttekintése](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>További példák a lekérdezésekre

#### <a name="100-most-recent-registry-events"></a>100 a legfrissebb beállításjegyzék-események

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>További naplófájl-célhelyek

Amellett, hogy a naplókat Log Analyticsre küldi, vagy egy másik megoldásként egy Azure Storage-fiókot kell kiválasztania a napló célhelyként. Az Azure Storage-beli naplók archiválásához hozzon létre egy Storage-fiókot, mielőtt engedélyezi az archiválást a diagnosztikai beállításokon.

A diagnosztikai napló eseményeit egy [Azure Event hub](../event-hubs/event-hubs-what-is-event-hubs.md)-ba is továbbíthatja. A Event Hubs másodpercenként több millió eseményt képes befogadni, amelyet később bármilyen valós idejű elemzési szolgáltató használatával átalakíthat és tárolhat. 

## <a name="next-steps"></a>Következő lépések

* További információ a [log Analytics](../azure-monitor/log-query/get-started-portal.md) használatáról és a [naplók](../azure-monitor/log-query/get-started-queries.md)létrehozásáról.
* A különböző Azure-rétegekben elérhető platform-naplók megismeréséhez tekintse meg [Az Azure platform-naplók áttekintése](../azure-monitor/platform/platform-logs-overview.md) című témakört.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
