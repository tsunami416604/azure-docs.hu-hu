---
title: Az Azure tevékenységnapló áttekintése
description: Ismerje meg, mi az az Azure-tevékenységnapló, és hogyan használhatja azt az Azure-előfizetésen belül bekövetkező események megértéséhez.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245029"
---
# <a name="overview-of-azure-activity-log"></a>Az Azure-tevékenységnapló áttekintése

A **Azure-tevékenységnapló** történt az Azure-beli előfizetés-szintű események betekintést nyújt. Ez magában foglalja az adatok az Azure Resource Manager frissítéseket a Service Health-események operatív adatok széles. A tevékenységnapló korábbi nevén _Auditnaplók_ vagy _műveleti naplók_, mivel a felügyeleti kategória vezérlősík események az előfizetésekre vonatkozó jelentéseket. 

A tevékenységnapló segítségével meghatározhatja, hogy a _mi_, _akik_, és _amikor_ írási műveletek (PUT, POST, DELETE) tett erőforrásokra az előfizetésben. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is. 

A tevékenységnapló nem tartalmaz olvasási (GET) műveleteket, illetve az erőforrások a klasszikus/RDFE modellt használó műveleteket.

## <a name="comparison-to-diagnostic-logs"></a>Diagnosztikai naplók és összehasonlítása
Nincs egyetlen tevékenység napló minden egyes Azure-előfizetés. A kívülről erőforrás-műveletekkel kapcsolatos adatokat biztosít (a "vezérlősík"). [Diagnosztikai naplók](diagnostic-logs-overview.md) az erőforrás által kibocsátott vannak, és adja meg a műveletet az erőforrás (az "adatsík") kapcsolatos információkat. Engedélyeznie kell az egyes erőforrások diagnosztikai beállításait.

![Diagnosztikai naplók képest tevékenységeket tartalmazó naplók](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Az Azure-tevékenységnapló elsősorban az Azure Resource Manager-tevékenységek szól. A klasszikus/RDFE modellel ugresources nem követi nyomon. Néhány klasszikus erőforrástípusok rendelkezik egy proxykiszolgáló erőforrás-szolgáltató az Azure Resource Manager (például Microsoft.ClassicCompute). Klasszikus erőforrástípust Azure Resource Manageren keresztül ezek proxy erőforrás-szolgáltatók használata kezelheti, ha a műveletek a tevékenységnaplóban jelennek meg. Egy klasszikus erőforrástípus kívül az Azure Resource Manager-proxyk dolgozhat, ha a műveletek csak a műveleti napló elszámolni. A műveleti napló egy külön szakaszban a portál tallózható.

## <a name="activity-log-retention"></a>Tevékenység napló megőrzése
Tevékenységnapló-események 90 napig tárolódnak. Az adatok tárolásához hosszabb ideig [gyűjteni az Azure monitorban](activity-log-collect.md) vagy [exportálhatja, és a storage vagy az Event Hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>A tevékenységnapló megtekintése
Az összes erőforrás a tevékenységnapló megtekintése a **figyelő** menü az Azure Portalon. Egy adott erőforráshoz, a tevékenységnapló megtekintése a **tevékenységnapló** lehetőség az adott erőforrás menüben. Tevékenységnapló rögzíti a PowerShell, CLI vagy REST API-t is lekérhet.  Lásd: [megtekintése és lekérése az Azure-tevékenység naplózása](activity-log-view.md).

![Tevékenységnapló megtekintése](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Az Azure Monitor tevékenységnapló gyűjtése
A tevékenységnapló gyűjtése az Azure Monitor más figyelési adatok elemzéséhez, és az adatok 90 napnál hosszabb ideig megőrzi a Log Analytics-munkaterületet. Lásd: [összegyűjteni és elemezni az Azure-Tevékenységnaplók Log Analytics-munkaterületet az Azure monitorban az](activity-log-collect.md).

![Lekérdezés tevékenységnapló](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Tevékenységnapló exportálása
A tevékenységnapló exportálása az Azure Storage alacsony költségű, vagy streamelése az Eseményközpontba támogatunk egy külső szolgáltatás vagy egyéni elemzési megoldást. Lásd: [az Azure tevékenységnapló exportálása](activity-log-export.md). A Power BI használatával tevékenységnapló eseményei is elemezheti a [ **Power BI-tartalomcsomag**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Riasztási tevékenységnapló
Ha adott eseményeket jönnek létre a tevékenységnapló-riasztást hozhat létre egy [tevékenységnapló-riasztás](activity-log-alerts.md). Egy riasztás használatával is létrehozhat egy [naplólekérdezés](alerts-log-query.md) amikor a tevékenységnapló csatlakoztatva van egy Log Analytics-munkaterületet, de van a bejelentkezni költség riasztások lekérdezése. Nincs ingyenesen a tevékenységnapló-riasztások esetén.

## <a name="categories-in-the-activity-log"></a>Kategóriák a tevékenységnaplóban
A tevékenységnapló szereplő minden egyes esemény egy adott kategória a következő táblázatban ismertetett rendelkezik. Részletes információ a sémák szerializálása e kategóriák közül, lásd: [Azure-tevékenységnapló eseménysémája](activity-log-schema.md). 

| Category | Leírás |
|:---|:---|
| Adminisztratív | Tartalmazza az összes rekordot létrehozni, frissítési, törlési és műveleti műveleteket hajtja végre a Resource Manageren keresztül. Felügyeleti események közé _hozzon létre virtuális gépet_ és _hálózati biztonsági csoport törlése_.<br><br>Minden felhasználó vagy alkalmazás használatával a Resource Manager által végrehajtott műveletek az egyes erőforrástípusok műveletként van modellezve. Ha a művelet típusa _írási_, _törlése_, vagy _művelet_, a rekordokat a kezdő és a sikeres vagy sikertelen a művelet rögzíti a felügyeleti kategória. Felügyeleti események is módosítania kellene a szerepköralapú hozzáférés-vezérlés az előfizetéshez. |
| Service Health | A service health az Azure-ban bekövetkezett események rekordot tartalmaz. A Service Health-esemény példát _SQL Azure, az USA keleti régiójában üzemen kívül van_. <br><br>Service Health-események az öt fajta származnak: _Szükséges művelet_, _helyreállítási támogatott_, _incidens_, _karbantartási_, _információk_, vagy  _Biztonsági_. Ezeket az eseményeket csak akkor jönnek létre, ha rendelkezik egy erőforrással lenne hatással lehet az esemény-előfizetés.
| Resource Health | A rekord a az Azure-erőforrások előfordult resource health események tartalmazza. A Resource Health-esemény, például _virtuális gép állapota módosult az nem érhető el_.<br><br>Közelmúltbeli állapotesemények hozhat létre négy egészségügyi állapotok egyike: _Rendelkezésre álló_, _nem érhető el_, _csökkentett teljesítményű_, és _ismeretlen_. Emellett a Resource Health-események csoportosíthatók, hogy _Platform által kezdeményezett_ vagy _felhasználó által kezdeményezett_. |
| Riasztás | Az Azure-riasztások aktiválások rekordot tartalmaz. Például egy riasztási eseménynek _a myVM százalékos processzorhasználatról már több mint 80-as az elmúlt 5 percben_.|
| Automatikus méretezés | A rekord a meghatározott az előfizetés automatikus skálázási beállítások alapján automatikus skálázási motor a művelethez kapcsolódó események tartalmazza. Például az automatikus skálázási esemény _automatikus vertikális felskálázási művelet nem sikerült_. |
| Ajánlás | Javaslattételi események az Azure Advisor tartalmazza. |
| Biztonság | Bármely Azure Security Center által létrehozott riasztások rekordot tartalmaz. Biztonsági esemény például _kettős kiterjesztésű gyanús fájlt futtatott_. |
| Szabályzat | Az Azure Policy által végrehajtott összes érvénybe művelet művelet rekordot tartalmaz. Házirend-események közé _naplózási_ és _Megtagadás_. Minden szabályzat által elvégzett művelet egy erőforrás-művelet van modellezve. |


## <a name="next-steps"></a>További lépések

* [Az Azure-tevékenységnapló exportálása log profil létrehozása](activity-log-export.md)
* [Az Event hubs Azure tevékenységnapló Stream](activity-logs-stream-event-hubs.md)
* [A Storage Azure-tevékenységnapló archiválása](archive-activity-log.md)

