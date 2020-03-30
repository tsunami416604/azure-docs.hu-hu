---
title: Lassú lekérdezési naplók – Azure-adatbázis a MariaDB-hez
description: A MariaDB Azure Database szolgáltatásban elérhető naplókat és a különböző naplózási szintek engedélyezéséhez rendelkezésre álló paramétereket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2c07e5eeedd2e4f42ec7b165bf161e142421df58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527894"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Lassú lekérdezési naplók a MariaDB Azure-adatbázisában
A MariaDB Azure Database-ben a lassú lekérdezési napló elérhető a felhasználók számára. A tranzakciós naplóhoz való hozzáférés nem támogatott. A lassú lekérdezési napló segítségével azonosíthatók a hibaelhárítás teljesítménybeli szűk keresztmetszetei.

A lassú lekérdezési naplóról a [Lassú lekérdezési naplóról](https://mariadb.com/kb/en/library/slow-query-log-overview/)a MariaDB dokumentációjában olvashat bővebben.

## <a name="access-slow-query-logs"></a>Lassú lekérdezési naplók elérése
Az Azure Portal és az Azure CLI használatával listázhatja és letöltheti a MariaDB lassú lekérdezési naplóit.

Az Azure Portalon válassza ki az Azure Database for MariaDB-kiszolgálót. A **Figyelés** fejléc alatt válassza a **Kiszolgálónaplók** lapot.

Az Azure CLI-ről további információt a [Kiszolgálónaplók konfigurálása és elérése az Azure CLI használatával című](howto-configure-server-logs-cli.md)témakörben talál.

Hasonlóképpen a naplók at az Azure Monitor diagnosztikai naplók használatával. További [below](concepts-server-logs.md#diagnostic-logs) információt alább talál.

## <a name="log-retention"></a>Naplómegőrzés
A naplók a létrehozásuktól legfeljebb hét napig állnak rendelkezésre. Ha a rendelkezésre álló naplók teljes mérete meghaladja a 7 GB-ot, a legrégebbi fájlok törlődnek, amíg nincs szabad hely.

A naplók at 24 óránként vagy 7 GB-onként forgatják el, attól függően, hogy melyik következik be előbb.

## <a name="configure-slow-query-logging"></a>Lassú lekérdezésnaplózás konfigurálása
Alapértelmezés szerint a lassú lekérdezési napló le van tiltva. Az engedélyezéshez állítsa slow_query_log BE beállításra.

További beállítások a következők:

- **long_query_time:** ha egy lekérdezés long_query_time (másodpercben) tovább tart, a lekérdezés naplózása. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements:** ha az ON a slow_query_log írt nyilatkozatokban olyan adminisztratív nyilatkozatokat tartalmaz, mint a ALTER_TABLE és a ANALYZE_TABLE.
- **log_queries_not_using_indexes**: meghatározza, hogy az indexeket nem használó lekérdezések naplózva legyenek-e a slow_query_log
- **log_throttle_queries_not_using_indexes**: Ez a paraméter korlátozza a lassú lekérdezési naplóba írható nem indexelt lekérdezések számát. Ez a paraméter akkor lép érvénybe, ha log_queries_not_using_indexes be van kapcsolva.
- **log_output:** ha "Fájl", lehetővé teszi, hogy a lassú lekérdezési napló t írja mind a helyi kiszolgáló tárolóés az Azure Monitor diagnosztikai naplók. Ha "Nincs", a lassú lekérdezési napló csak az Azure Monitor diagnosztikai naplók írása lesz. 

> [!IMPORTANT]
> Ha a táblák nincsenek indexelve, a paraméterek és `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` a paraméterek ON beállítása hatással lehet a MariaDB teljesítményére, mivel a nem indexelt táblákon futó összes lekérdezés a lassú lekérdezési naplóba kerül.<br><br>
> Ha azt tervezi, hogy hosszabb ideig naplózza a lassú `log_output` lekérdezéseket, ajánlott "Nincs" beállítás. Ha a beállítás "Fájl", ezek a naplók a helyi kiszolgáló tárolójába kerülnek, és hatással lehetnek a MariaDB teljesítményére. 

A lassú lekérdezési napló paramétereinek teljes leírását a MariaDB [lassú lekérdezési naplódokumentációjában](https://mariadb.com/kb/en/library/slow-query-log-overview/) találja.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
A MariaDB Azure Database integrálva van az Azure Monitor diagnosztikai naplóival. Miután engedélyezte a lassú lekérdezési naplókat a MariaDB-kiszolgálón, beállíthatja, hogy azokat az Azure Monitor-naplók, az Event Hubs vagy az Azure Storage számára adja ki. A diagnosztikai naplók engedélyezéséről a [diagnosztikai naplók dokumentációjának](../azure-monitor/platform/platform-logs-overview.md)útmutatójában olvashat bővebben.

Az alábbi táblázat az egyes naplókat ismerteti. A kimeneti módszertől függően a belefoglalt mezők és megjelenésük sorrendje eltérő lehet.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlőazonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated`-1, hogy az 1999. | Időbélyegző, amikor a naplót rögzítették UTC-ben |
| `Type` | A napló típusa. Mindig`AzureDiagnostics` |
| `SubscriptionId` | Guid az előfizetéshez, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Annak az erőforráscsoportnak a neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | A kiszolgáló neve |
| `start_time_t`-1, hogy az 1999. | A lekérdezés kezdete |
| `query_time_s` | A lekérdezés végrehajtásához szükséges teljes idő |
| `lock_time_s` | A lekérdezés zárolásának teljes ideje |
| `user_host_s` | Felhasználónév |
| `rows_sent_s` | Elküldött sorok száma |
| `rows_examined_s` | Megvizsgált sorok száma |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Azonosító beszúrása |
| `sql_text_s` | Teljes lekérdezés |
| `server_id_s` | Kiszolgáló azonosítója |
| `thread_id_s` | Szál azonosítója |
| `\_ResourceId` | Erőforrás URI-ja |

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

- 10 másodpercnél hosszabb lekérdezések megjelenítése az összes MariaDB-kiszolgálón engedélyezve van a diagnosztikai naplók

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Következő lépések
- [Lassú lekérdezési naplók konfigurálása az Azure Portalról](howto-configure-server-logs-portal.md)
- [Lassú lekérdezési naplók konfigurálása az Azure CLI-ből](howto-configure-server-logs-cli.md)
