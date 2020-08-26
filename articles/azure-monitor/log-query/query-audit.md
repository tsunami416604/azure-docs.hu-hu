---
title: Lekérdezések naplózása Azure Monitor naplózási lekérdezésekben
description: A naplózási lekérdezések azon naplófájljainak részletei, amelyek a Azure Monitorban futtatott telemetria nyújtanak.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/25/2020
ms.openlocfilehash: cb38dcba2f61a432decb56164b816688ad3192d8
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893754"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Lekérdezések naplózása Azure Monitor naplókban (előzetes verzió)
A napló lekérdezési naplói a Azure Monitorban futtatott telemetria kapcsolatos információkat biztosítanak. Ez olyan információkat tartalmaz, mint például a lekérdezés futtatása, a futtatásuk, a használt eszköz, a lekérdezés szövege és a lekérdezés végrehajtását leíró teljesítmény-statisztika.

## <a name="current-limitations"></a>Aktuális korlátozások
A nyilvános előzetes verzióban a következő korlátozások érvényesek:

- Csak a munkaterület-központú lekérdezések lesznek naplózva. A lekérdezések erőforrás-központú módban futnak, vagy egy olyan Application Insightson futnak, amely nem munkaterület-alapúként van konfigurálva. a rendszer nem naplózza.


## <a name="configure-query-auditing"></a>Lekérdezés naplózásának konfigurálása
A lekérdezés naplózása a Log Analytics munkaterületen [diagnosztikai beállítással](../platform/diagnostic-settings.md) engedélyezhető. Ez lehetővé teszi, hogy naplózási adatait a jelenlegi munkaterületre vagy az előfizetése bármely más munkaterületére küldje az Azure Event Hubs az Azure-on kívülre vagy az Azure Storage-ba való archiválásra. 

### <a name="azure-portal"></a>Azure Portal
Egy Log Analytics munkaterület diagnosztikai beállításának elérése a Azure Portal a következő helyekről:

- A **Azure monitor** menüben válassza a **diagnosztikai beállítások**elemet, majd keresse meg és válassza ki a munkaterületet.

    [![Diagnosztikai beállítások Azure Monitor ](media/log-query-audit/diagnostic-setting-monitor.png)](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- A **log Analytics munkaterületek** menüben válassza ki a munkaterületet, majd válassza a **diagnosztikai beállítások**lehetőséget.

    [![Diagnosztikai beállítások Log Analytics munkaterület ](media/log-query-audit/diagnostic-setting-workspace.png)](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Resource Manager-sablon
Egy példa Resource Manager-sablonra a [log Analytics munkaterület diagnosztikai beállításainál](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Naplózási adatgyűjtés
A rendszer minden alkalommal létrehoz egy naplózási rekordot, amikor egy lekérdezés fut. Ha Log Analytics-munkaterületre küldi az adatküldést, azt egy *LAQueryLogs*nevű táblában tárolja. A következő táblázat a naplózási adatok egyes rekordjainak tulajdonságait ismerteti.

| Mező | Leírás |
|:---|:---|
| TimeGenerated         | A lekérdezés elküldésének UTC-ideje. |
| CorrelationId         | A lekérdezés azonosítására szolgáló egyedi azonosító. Hibaelhárítási forgatókönyvekben használható, ha segítségre van szükség a Microsofttal való kapcsolatfelvételhez. |
| AADObjectId           | A lekérdezést indító felhasználói fiók Azure Active Directory azonosítója.  |
| AADTenantId           | A lekérdezést indító felhasználói fiók bérlője.  |
| AADEmail              | A lekérdezést elindító felhasználói fiók bérlője e-mail-címe.  |
| AADClientId           | A lekérdezés elindításához használt alkalmazás azonosítója és feloldott neve. |
| RequestClientApp      | A lekérdezés elindításához használt alkalmazás feloldott neve. |
| QueryTimeRangeStart   | A lekérdezéshez kiválasztott időtartomány kezdete. Ez bizonyos helyzetekben nem tölthető fel, például akkor, ha a lekérdezés Log Analyticsból indul el, és az időtartomány a lekérdezésen belül van megadva az időválasztó helyett. |
| QueryTimeRangeEnd     | A lekérdezéshez kiválasztott időtartomány vége. Ez bizonyos helyzetekben nem tölthető fel, például akkor, ha a lekérdezés Log Analyticsból indul el, és az időtartomány a lekérdezésen belül van megadva az időválasztó helyett.  |
| QueryText             | A futtatott lekérdezés szövege. |
| RequestTarget         | A lekérdezés elküldéséhez használt API URL-cím.  |
| RequestContext        | Azoknak az erőforrásoknak a listája, amelyeken a lekérdezésre kérték. Legfeljebb három karakterlánc-tömböt tartalmaz: munkaterületeket, alkalmazásokat és erőforrásokat. Az előfizetés vagy az erőforráscsoport által megadott lekérdezések *erőforrásokként*jelennek meg. A RequestTarget által vélelmezett célként megadott célt is tartalmazza. |
| RequestContextFilters | A lekérdezés meghívásának részeként megadott szűrők halmaza. Legfeljebb három lehetséges karakterlánc-tömböt tartalmaz:<br>-ResourceTypes – a lekérdezés hatókörének korlátozására szolgáló erőforrás típusa<br>– Munkaterületek – a lekérdezésre korlátozni kívánt munkaterületek listája<br>-WorkspaceRegions – a lekérdezés korlátozására szolgáló munkaterület-régiók listája |
| ResponseCode          | A lekérdezés elküldésekor visszaadott HTTP-válasz kódja. |
| ResponseDurationMs    | A válasz eredményének időpontja.  |
| StatsCPUTimeMs       | A számítási, elemzési és beolvasási számítási idő összesen. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsDataProcessedKB | A lekérdezés feldolgozásához elért adatmennyiség. A cél tábla mérete, a használt időtartomány, az alkalmazott szűrők és a hivatkozott oszlopok száma befolyásolja. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsDataProcessedStart | A lekérdezés feldolgozásához elért legrégebbi adatmennyiség. Befolyásolja a lekérdezés explicit időtartománya és a szűrők alkalmazása. Ez nagyobb lehet, mint az adatparticionálás miatti explicit időtartomány. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsDataProcessedEnd  |A lekérdezés feldolgozásához elért legújabb adatmennyiség. Befolyásolja a lekérdezés explicit időtartománya és a szűrők alkalmazása. Ez nagyobb lehet, mint az adatparticionálás miatti explicit időtartomány. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsWorkspaceCount | A lekérdezés által elért munkaterületek száma. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsRegionCount | A lekérdezés által elért régiók száma. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |



## <a name="next-steps"></a>További lépések

- További információ a [diagnosztikai beállításokról](../platform/diagnostic-settings.md).
- További információ a [naplók lekérdezésének optimalizálásáról](query-optimization.md).