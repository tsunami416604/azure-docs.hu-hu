---
title: Erőforrásnaplók gyűjtése & elemzése
description: Rögzítse és elemezze az erőforrásnapló-eseményeket az Azure Container Registry számára, például a hitelesítést, a lemezkép leküldéses és a lemezképlekéréset.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409643"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Az Azure Container registry naplók diagnosztikai kiértékeléshez és naplózáshoz

Ez a cikk bemutatja, hogyan gyűjthet naplóadatokat egy Azure-tároló beállításjegyzékéhez az [Azure Monitor](../azure-monitor/overview.md)szolgáltatásainak használatával. Az Azure Monitor [erőforrásnaplókat](../azure-monitor/platform/platform-logs-overview.md) (korábbi nevén *diagnosztikai naplókat)* gyűjt a rendszerleíró adatbázisban lévő felhasználó által vezérelt eseményekhez. Gyűjtse össze és használja fel ezeket az adatokat, hogy megfeleljen az igényeknek, mint például:

* A rendszerleíró adatbázis hitelesítési eseményeinek naplózása a biztonság és a megfelelőség biztosítása érdekében 

* Teljes tevékenységnyomvonal biztosítása a rendszerleíró adatbázis-összetevőkön, például lekéréses és lekéréses eseményeken, hogy diagnosztizálhassa a működési problémákat a beállításjegyzékben 

Az erőforrásnapló-adatok Azure Monitor használatával történő gyűjtése további költségekkel járhat. Lásd: [Az Azure Monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Tárház események

A rendszer jelenleg a következő tárházszintű eseményeket naplózza a rendszer a rendszer hez:

* **Leküldéses események**
* **Események lekérése**
* **Nem tagesemények**
* **Események törlése** (beleértve a tárház törlési eseményeit)

Tárházszintű események, amelyek jelenleg nem naplózva: Események törlése.

## <a name="registry-resource-logs"></a>Rendszerleíró adatbázis erőforrásnaplói

Az erőforrásnaplók az Azure-erőforrások által kibocsátott adatokat tartalmazzák, amelyek leírják a belső működésüket. Az Azure-tároló beállításjegyzék, a naplók hitelesítési és tárházszintű események et a következő táblázatokban tárolt. 

* **ContainerRegistryLoginEvents** – Rendszerleíró adatbázis hitelesítési eseményei és állapota, beleértve a bejövő identitást és az IP-címet
* **ContainerRegistryRepositoryEvents** - Műveletek, mint a leküldéses és lekéréses a lemezképek és egyéb összetevők registry tárolók
* **Az AzureMetrics** - [Container beállításjegyzék-metrikák,](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) például az összesített leküldéses és lekéréses számok.

A műveletek esetében a naplóadatok a következőket tartalmazzák:
  * Sikeres vagy sikertelen állapot
  * Kezdő és záró időbélyegzők

Az erőforrás-naplók mellett az Azure [egy tevékenységnaplót](../azure-monitor/platform/platform-logs-overview.md)is biztosít, amely egyetlen előfizetés-szintű rekordot biztosít az Azure felügyeleti eseményeiről, például egy tároló beállításjegyzékének létrehozásáról vagy törléséről.

## <a name="enable-collection-of-resource-logs"></a>Erőforrásnaplók gyűjtésének engedélyezése

A tárolók beállításjegyzékéhez való erőforrásnaplók gyűjtése alapértelmezés szerint nincs engedélyezve. Explicit módon engedélyezze a diagnosztikai beállításokat minden figyelni kívánt beállításjegyzékhez. A diagnosztikai beállítások engedélyezésének lehetőségeiről a [Diagnosztikai beállítás létrehozása platformnaplók és metrikák gyűjtéséhez az Azure-ban című témakörben olvashat.](../azure-monitor/platform/diagnostic-settings.md)

Ha például közel valós időben szeretné megtekinteni egy tárolóbeállítás-jegyzék naplóit és metrikákat az Azure Monitorban, gyűjtse össze az erőforrásnaplókat egy Log Analytics-munkaterületen. A diagnosztikai beállítás engedélyezése az Azure Portal használatával:

1. Ha még nem rendelkezik munkaterülettel, hozzon létre egy munkaterületet az [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)használatával. Az adatgyűjtés késésének minimalizálása érdekében győződjön meg arról, hogy a munkaterület ugyanabban a **régióban** van, mint a tároló beállításjegyzékében.
1. A portálon jelölje ki a rendszerleíró adatbázist, és válassza **a Figyelés > diagnosztikai beállítások > Diagnosztikai beállítás hozzáadása lehetőséget.**
1. Adja meg a beállítás nevét, és válassza a **Küldés a Log Analytics szolgáltatásba**lehetőséget.
1. Válassza ki a rendszerleíró adatbázis diagnosztikai naplóinak munkaterületét.
1. Jelölje ki az összegyűjteni kívánt naplóadatokat, majd kattintson a **Mentés gombra.**

Az alábbi képen egy beállításjegyzék diagnosztikai beállításának létrehozása látható a portál használatával.

![Diagnosztikai beállítások engedélyezése](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Csak a szükséges adatokat gyűjtse össze, kiegyensúlyozva a költségeket és a figyelési igényeket. Ha például csak hitelesítési eseményeket kell naplóznia, csak a **ContainerRegistryInEvents** naplót válassza ki. 

## <a name="view-data-in-azure-monitor"></a>Adatok megtekintése az Azure Monitorban

Miután engedélyezte a diagnosztikai naplók gyűjtését a Log Analytics szolgáltatásban, eltarthat néhány percig, amíg az adatok megjelennek az Azure Monitorban. A portálon lévő adatok megtekintéséhez jelölje ki a rendszerleíró adatbázist, és válassza **a Naplók figyelése >** lehetőséget. Jelölje ki a rendszerleíró adatbázis adatait tartalmazó táblák egyikét. 

Futtasson lekérdezéseket az adatok megtekintéséhez. Számos mintalekérdezés biztosított, vagy futtassa a saját. A következő lekérdezés például lekéri a legutóbbi 24 órányi adatot a **ContainerRegistryRepositoryEvents** táblából:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Az alábbi képen a mintakimenet látható:

![Naplóadatok lekérdezése](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

A Log Analytics Azure Portalon való használatáról az [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)című témakörben vagy a Log Analytics [bemutatókörnyezetének](https://portal.loganalytics.io/demo)kipróbálásával című témakörben talál. 

A naplólekérdezésekkel kapcsolatos további információt [az Azure Monitor naplólekérdezéseinek áttekintése](../azure-monitor/log-query/log-query-overview.md)című témakörben talál.

### <a name="additional-query-examples"></a>További lekérdezési példák

#### <a name="100-most-recent-registry-events"></a>100 legutóbbi rendszerleíró esemény

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>További naplócélok

A naplók küldése a Log Analytics, vagy alternatívaként gyakori forgatókönyv, hogy válasszon ki egy Azure Storage-fiókot, mint a napló cél. A naplók archiválásához az Azure Storage-ban hozzon létre egy tárfiókot, mielőtt engedélyezne a diagnosztikai beállításokon keresztül történő archiválást.

Diagnosztikai naplóeseményeket is streamelhet egy [Azure Event Hub-ba.](../event-hubs/event-hubs-what-is-event-hubs.md) Az Event Hubs másodpercenként több millió eseményt tud befizetni, amelyeket aztán bármely valós idejű elemzési szolgáltató használatával átalakíthat és tárolhat. 

## <a name="next-steps"></a>További lépések

* További információ a [Log Analytics](../azure-monitor/log-query/get-started-portal.md) használatáról és [a naplólekérdezések](../azure-monitor/log-query/get-started-queries.md)létrehozásáról.
* Az [Azure platformnaplók áttekintése című](../azure-monitor/platform/platform-logs-overview.md) témakörben olvashat az Azure különböző rétegein elérhető platformnaplókról.

