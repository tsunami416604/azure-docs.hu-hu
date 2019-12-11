---
title: Lassú lekérdezési naplók – Azure Database for MySQL
description: A Azure Database for MySQLban elérhető lassú lekérdezési naplókat, valamint a különböző naplózási szintek engedélyezéséhez elérhető paramétereket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 6bd99a200a8f9e6be6d155a334b9b06ac05eacc3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972183"
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
- **log_output**: Ha a "fájl", lehetővé teszi a lassú lekérdezési napló írását a helyi kiszolgáló tárterületére és a diagnosztikai naplók Azure Monitorére. Ha "nincs", a lassú lekérdezési napló csak a helyi kiszolgáló tárterületére lesz írva. 

> [!Note]
> `sql_text`esetén a rendszer csonkolja a naplót, ha az meghaladja a 2048 karaktert.

A lassú lekérdezési napló paramétereinek teljes leírását a MySQL [lassú lekérdezési napló dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) találja.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for MySQL integrálva van Azure Monitor diagnosztikai naplókba. Ha engedélyezte a lassú lekérdezési naplókat a MySQL-kiszolgálón, kiválaszthatja, hogy a naplókat, Event Hubsokat vagy az Azure Storage-t Azure Monitor. A diagnosztikai naplók engedélyezésével kapcsolatos további tudnivalókért tekintse meg a [diagnosztikai naplók dokumentációjának](../azure-monitor/platform/resource-logs-overview.md)útmutató szakaszát.

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

## <a name="next-steps"></a>Következő lépések
- [A kiszolgálói naplók konfigurálása és elérése az Azure CLI-](howto-configure-server-logs-in-cli.md)vel.
