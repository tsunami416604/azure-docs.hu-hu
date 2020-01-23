---
title: Lassú lekérdezési naplók – Azure Database for MySQL
description: A Azure Database for MySQLban elérhető lassú lekérdezési naplókat, valamint a különböző naplózási szintek engedélyezéséhez elérhető paramétereket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: e0c58c5c3fef41a472fe791f66292c9280531493
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514680"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Lassú lekérdezési naplók a Azure Database for MySQL
Azure Database for MySQL a lassú lekérdezési napló elérhető a felhasználók számára. A tranzakciónaplóhoz való hozzáférés nem támogatott. A lassú lekérdezési napló használatával azonosíthatók a hibaelhárítási teljesítménybeli szűk keresztmetszetek.

További információ a MySQL lassú lekérdezési naplóról: a MySQL-hivatkozás manuális [lassú lekérdezési napló szakasza](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-slow-query-logs"></a>Lassú lekérdezési naplók elérése
A Azure Portal és az Azure CLI használatával Azure Database for MySQL lassú lekérdezési naplókat listázhat és tölthet le.

A Azure Portal válassza ki a Azure Database for MySQL kiszolgálót. A **figyelés** fejléc alatt válassza ki a **kiszolgálói naplók** lapot.

Az Azure CLI-vel kapcsolatos további információkért lásd: [lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával](howto-configure-server-logs-in-cli.md).

Hasonlóképpen, a naplók Azure Monitor a diagnosztikai naplók használatával is átadhatók. További információért lásd [alább](concepts-server-logs.md#diagnostic-logs) .

## <a name="log-retention"></a>Napló megőrzése
A naplók akár hét napig is elérhetők a létrehozásuk után. Ha a rendelkezésre álló naplók teljes mérete meghaladja a 7 GB-ot, a rendszer törli a legrégebbi fájlokat, amíg a szabad terület elérhetővé válik. 

A naplók 24 óránként vagy 7 GB-onként vannak elforgatva, attól függően, hogy melyik érkezik először.

## <a name="configure-slow-query-logging"></a>Lassú lekérdezések naplózásának konfigurálása 
Alapértelmezés szerint a lassú lekérdezési napló le van tiltva. Az engedélyezéshez állítsa be slow_query_log a következőre:.

Az egyéb paraméterek a következők:

- **long_query_time**: Ha a lekérdezés a lekérdezés naplózása long_query_time (másodpercben) hosszabb időt vesz igénybe. Az alapértelmezett érték a 10 másodperc.
- **log_slow_admin_statements**: Ha a on olyan felügyeleti utasítások is szerepelnek, mint például a ALTER_TABLE és a ANALYZE_TABLE a slow_query_logba írt utasításokban.
- **log_queries_not_using_indexes**: meghatározza, hogy az indexeket nem használó lekérdezések naplózva vannak-e a slow_query_log
- **log_throttle_queries_not_using_indexes**: Ez a paraméter korlátozza a lassú lekérdezési naplóba írható nem indexelt lekérdezések számát. Ez a paraméter akkor lép életbe, amikor a log_queries_not_using_indexes be van állítva.
- **log_output**: Ha a "fájl", lehetővé teszi a lassú lekérdezési napló írását a helyi kiszolgáló tárterületére és a diagnosztikai naplók Azure Monitorére. Ha "nincs", a lassú lekérdezési napló csak Azure Monitor diagnosztikai naplókba lesz írva. 

> [!IMPORTANT]
> Ha a táblák nem indexelve vannak, akkor a (z) `log_queries_not_using_indexes` és `log_throttle_queries_not_using_indexes` paraméterek a (z) értékre való beállítása hatással lehet a MySQL-teljesítményre, mivel a nem indexelt táblákon futó összes lekérdezés a lassú lekérdezési naplóba kerül.<br><br>
> Ha hosszabb ideig szeretné naplózni a lassú lekérdezéseket, ajánlott `log_output` "None" értékre állítani. Ha a "file" értékre van állítva, a rendszer a helyi kiszolgáló tárolóba írja a naplókat, és hatással lehet a MySQL teljesítményére. 

A lassú lekérdezési napló paramétereinek teljes leírását a MySQL [lassú lekérdezési napló dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) találja.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for MySQL integrálva van Azure Monitor diagnosztikai naplókba. Ha engedélyezte a lassú lekérdezési naplókat a MySQL-kiszolgálón, kiválaszthatja, hogy a naplókat, Event Hubsokat vagy az Azure Storage-t Azure Monitor. A diagnosztikai naplók engedélyezésével kapcsolatos további tudnivalókért tekintse meg a [diagnosztikai naplók dokumentációjának](../azure-monitor/platform/platform-logs-overview.md)útmutató szakaszát.

> [!IMPORTANT]
> A kiszolgálói naplókhoz tartozó diagnosztikai funkció csak a általános célú és a memória optimalizált [díjszabási szintjein](concepts-pricing-tiers.md)érhető el.

Az alábbi táblázat az egyes naplókat ismerteti. A kimeneti módszertől függően a befoglalt mezők és a megjelenő sorrend eltérő lehet.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | A kiszolgáló neve |
| `start_time_t` [UTC] | A lekérdezés elkezdésének ideje |
| `query_time_s` | A lekérdezés végrehajtásának teljes időtartama (másodpercben) |
| `lock_time_s` | A lekérdezés zárolásának teljes ideje másodpercben |
| `user_host_s` | Felhasználónév |
| `rows_sent_s` | Elérkezett sorok száma |
| `rows_examined_s` | Megvizsgált sorok száma |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | AZONOSÍTÓ beszúrása |
| `sql_text_s` | Teljes lekérdezés |
| `server_id_s` | A kiszolgáló azonosítója |
| `thread_id_s` | Szálazonosító |
| `\_ResourceId` | Erőforrás URI-ja |

> [!Note]
> `sql_text`esetén a rendszer csonkolja a naplót, ha az meghaladja a 2048 karaktert.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Naplók elemzése Azure Monitor naplókban

Ha a lassú lekérdezési naplók a diagnosztikai naplókon keresztül Azure Monitor naplókba, a lassú lekérdezések további elemzését is elvégezheti. Az alábbiakban néhány példa olvasható, amely segítséget nyújt az első lépésekhez. Győződjön meg arról, hogy az alábbit frissíti a kiszolgáló nevével.

- 10 másodpercnél hosszabb lekérdezések egy adott kiszolgálón

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Egy adott kiszolgálón az 5 leggyakoribb leghosszabb lekérdezés listázása

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Lassú lekérdezések összefoglalása egy adott kiszolgálón a minimális, a maximális, az átlag és a szórásos lekérdezési idő alapján

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Egy adott kiszolgáló lassú lekérdezési eloszlásának diagramja

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 10 másodpercnél hosszabb lekérdezések megjelenítése az összes olyan MySQL-kiszolgálón, amelyen engedélyezve vannak a diagnosztikai naplók

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Következő lépések
- [Lassú lekérdezési naplók konfigurálása a Azure Portal](howto-configure-server-logs-in-portal.md)
- [Lassú lekérdezések naplóinak konfigurálása az Azure CLI-ből](howto-configure-server-logs-in-cli.md).
