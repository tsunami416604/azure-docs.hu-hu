---
title: Lassú lekérdezési naplók - Azure Database for MySQL
description: Ismerteti a lassú lekérdezési naplók elérhető Azure Database for MySQL, és a rendelkezésre álló paramétereket a különböző naplózási szintek engedélyezéséhez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: f834ba3355d362e59e2e44f37eca0560b9bf4d7a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271981"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Lassú lekérdezési naplók az Azure Database for MySQL-ben
Az Azure Database for MySQL, a lassú lekérdezési napló érhető el a felhasználók számára. A tranzakciós naplóhoz való hozzáférés nem támogatott. A lassú lekérdezési napló segítségével azonosíthatók a hibaelhárítás teljesítménybeli szűk keresztmetszetei.

A MySQL lassú lekérdezési naplójáról további információt a MySQL kézikönyv [lassú lekérdezési naplója című szakaszban talál.](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)

## <a name="configure-slow-query-logging"></a>Lassú lekérdezésnaplózás konfigurálása 
Alapértelmezés szerint a lassú lekérdezési napló le van tiltva. Az engedélyezéshez `slow_query_log` állítsa be a beállítását. Ez az Azure Portalon vagy az Azure CLI használatával engedélyezhető. 

További beállítások a következők:

- **long_query_time:** ha egy lekérdezés long_query_time (másodpercben) tovább tart, a lekérdezés naplózása. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements:** ha az ON a slow_query_log írt nyilatkozatokban olyan adminisztratív nyilatkozatokat tartalmaz, mint a ALTER_TABLE és a ANALYZE_TABLE.
- **log_queries_not_using_indexes**: meghatározza, hogy az indexeket nem használó lekérdezések naplózva legyenek-e a slow_query_log
- **log_throttle_queries_not_using_indexes**: Ez a paraméter korlátozza a lassú lekérdezési naplóba írható nem indexelt lekérdezések számát. Ez a paraméter akkor lép érvénybe, ha log_queries_not_using_indexes be van kapcsolva.
- **log_output:** ha "Fájl", lehetővé teszi, hogy a lassú lekérdezési napló t írja mind a helyi kiszolgáló tárolóés az Azure Monitor diagnosztikai naplók. Ha "Nincs", a lassú lekérdezési napló csak az Azure Monitor diagnosztikai naplók írása lesz. 

> [!IMPORTANT]
> Ha a táblák nincsenek indexelve, a paraméterek ÉS `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` a paraméterek ON beállítása hatással lehet a MySQL teljesítményére, mivel a nem indexelt táblákon futó összes lekérdezés a lassú lekérdezési naplóba kerül.<br><br>
> Ha azt tervezi, hogy hosszabb ideig naplózza a lassú `log_output` lekérdezéseket, ajánlott "Nincs" beállítás. Ha a "Fájl" beállítás, ezek a naplók a helyi kiszolgáló tárolójába kerülnek, és hatással lehetnek a MySQL teljesítményére. 

A lassú lekérdezési napló paramétereinek teljes leírását a MySQL [lassú lekérdezési naplódokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) találja.

## <a name="access-slow-query-logs"></a>Lassú lekérdezési naplók elérése
Két lehetőség van a lassú lekérdezési naplók elérésére az Azure Database for MySQL-ben: helyi kiszolgálói tároló vagy Az Azure Monitor diagnosztikai naplók. Ez a `log_output` paraméter használatával van beállítva.

A helyi kiszolgáló tárolására, az Azure Portalon vagy az Azure CLI használatával listázhatja és letöltheti a lassú lekérdezési naplókat. Az Azure Portalon keresse meg a kiszolgálót az Azure Portalon. A **Figyelés** fejléc alatt válassza a **Kiszolgálónaplók** lapot. Az Azure CLI-ről további információt a [Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával című](howto-configure-server-logs-in-cli.md)témakörben talál. 

Az Azure Monitor diagnosztikai naplók lehetővé teszi, hogy lassú lekérdezési naplók az Azure Monitor Naplók (Log Analytics), Az Azure Storage vagy az Event Hubs. További [below](concepts-server-logs.md#diagnostic-logs) információt alább talál.

## <a name="local-server-storage-log-retention"></a>Helyi kiszolgálótároló naplójának megőrzése
A kiszolgáló helyi tárolójába való bejelentkezéskor a naplók a létrehozásuktól legfeljebb hét napig érhetők el. Ha a rendelkezésre álló naplók teljes mérete meghaladja a 7 GB-ot, a legrégebbi fájlok törlődnek, amíg nincs szabad hely.

A naplók at 24 óránként vagy 7 GB-onként forgatják el, attól függően, hogy melyik következik be előbb.

> [!Note]
> A fenti naplómegőrzés nem vonatkozik az Azure Monitor diagnosztikai naplók használatával vezetékes naplókra. Módosíthatja a kibocsátott adatgyűjtők megőrzési időszakát (pl. Azure Storage).

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
A MySQL Azure Database integrálva van az Azure Monitor diagnosztikai naplóival. Miután engedélyezte a lassú lekérdezési naplók at a MySQL-kiszolgálón, beállíthatja, hogy azokat az Azure Monitor naplók, Event Hubs vagy az Azure Storage. A diagnosztikai naplók engedélyezéséről a [diagnosztikai naplók dokumentációjának](../azure-monitor/platform/platform-logs-overview.md)útmutatójában olvashat bővebben.

Az alábbi táblázat az egyes naplókat ismerteti. A kimeneti módszertől függően a belefoglalt mezők és megjelenésük sorrendje eltérő lehet.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlőazonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated`-1, hogy az 1999. | Időbélyegző, amikor a naplót rögzítették UTC-ben |
| `Type` | A napló típusa. Mindig`AzureDiagnostics` |
| `SubscriptionId` | Guid az előfizetéshez, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Annak az erőforráscsoportnak a neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | A kiszolgáló neve |
| `start_time_t`-1, hogy az 1999. | A lekérdezés kezdete |
| `query_time_s` | A lekérdezés végrehajtásához szükséges teljes idő másodpercben |
| `lock_time_s` | A lekérdezés zárolt teljes ideje másodpercben |
| `user_host_s` | Felhasználónév |
| `rows_sent_s` | Elküldött sorok száma |
| `rows_examined_s` | Megvizsgált sorok száma |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Azonosító beszúrása |
| `sql_text_s` | Teljes lekérdezés |
| `server_id_s` | A kiszolgáló azonosítója |
| `thread_id_s` | Szál azonosítója |
| `\_ResourceId` | Erőforrás URI-ja |

> [!Note]
> A `sql_text`esetén a napló csonkul, ha az meghaladja a 2048 karaktert.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Naplók elemzése az Azure Figyelő naplóiban

Miután a lassú lekérdezési naplók az Azure Monitor naplók diagnosztikai naplókon keresztül, a lassú lekérdezések további elemzését végezheti el. Az alábbiakban néhány mintalekérdezést talál az első lépések hez. Győződjön meg róla, hogy frissítse az alábbi a szerver nevét.

- 10 másodpercnél hosszabb lekérdezések egy adott kiszolgálón

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Lista az 5 leghosszabb lekérdezés ek listázása egy adott kiszolgálón

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Lassú lekérdezések összegzése minimális, maximális, átlagos és szórásos lekérdezési idő szerint egy adott kiszolgálón

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Egy adott kiszolgálón a lassú lekérdezéseloszlás grafikonja

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 10 másodpercnél hosszabb lekérdezések megjelenítése az összes MySQL-kiszolgálón engedélyezve van a diagnosztikai naplók

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Következő lépések
- [Lassú lekérdezési naplók konfigurálása az Azure Portalról](howto-configure-server-logs-in-portal.md)
- [Lassú lekérdezési naplók konfigurálása az Azure CLI-ből.](howto-configure-server-logs-in-cli.md)
