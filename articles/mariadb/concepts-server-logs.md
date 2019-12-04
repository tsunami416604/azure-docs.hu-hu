---
title: Lassú lekérdezési naplók – Azure Database for MariaDB
description: Ismerteti a Azure Database for MariaDBban elérhető naplókat, valamint a különböző naplózási szintek engedélyezéséhez elérhető paramétereket.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8a451b06c8166b48fd892050e53204e2b65856c3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772104"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Lassú lekérdezési naplók a Azure Database for MariaDB
Azure Database for MariaDB a lassú lekérdezési napló elérhető a felhasználók számára. A tranzakciónaplóhoz való hozzáférés nem támogatott. A lassú lekérdezési napló használatával azonosíthatók a hibaelhárítási teljesítménybeli szűk keresztmetszetek.

A lassú lekérdezési naplóval kapcsolatos további információkért tekintse meg a [lassú lekérdezési napló](https://mariadb.com/kb/en/library/slow-query-log-overview/)MariaDB dokumentációját.

## <a name="access-slow-query-logs"></a>Lassú lekérdezési naplók elérése
A Azure Portal és az Azure CLI használatával Azure Database for MariaDB lassú lekérdezési naplókat listázhat és tölthet le.

A Azure Portal válassza ki a Azure Database for MariaDB kiszolgálót. A **figyelés** fejléc alatt válassza ki a **kiszolgálói naplók** lapot.

Az Azure CLI-vel kapcsolatos további információkért lásd: [kiszolgálók naplófájljainak konfigurálása és elérése az Azure CLI használatával](howto-configure-server-logs-cli.md).

## <a name="log-retention"></a>Napló megőrzése
A naplók akár hét napig is elérhetők a létrehozásuk után. Ha a rendelkezésre álló naplók teljes mérete meghaladja a 7 GB-ot, a rendszer törli a legrégebbi fájlokat, amíg a szabad terület elérhetővé válik.

A naplók 24 óránként vagy 7 GB-onként vannak elforgatva, attól függően, hogy melyik érkezik először.

## <a name="configure-slow-query-logging"></a>Lassú lekérdezések naplózásának konfigurálása
Alapértelmezés szerint a lassú lekérdezési napló le van tiltva. Az engedélyezéshez állítsa be slow_query_log a következőre:.

Az egyéb paraméterek a következők:

- **long_query_time**: Ha a lekérdezés a lekérdezés naplózása long_query_time (másodpercben) hosszabb időt vesz igénybe. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements**: Ha a on olyan felügyeleti utasítások is szerepelnek, mint például a ALTER_TABLE és a ANALYZE_TABLE a slow_query_logba írt utasításokban.
- **log_queries_not_using_indexes**: meghatározza, hogy az indexeket nem használó lekérdezések naplózva vannak-e a slow_query_log
- **log_throttle_queries_not_using_indexes**: Ez a paraméter korlátozza a lassú lekérdezési naplóba írható nem indexelt lekérdezések számát. Ez a paraméter akkor lép életbe, amikor a log_queries_not_using_indexes be van állítva.

A lassú lekérdezési napló paramétereinek teljes leírását a MariaDB [lassú lekérdezési napló dokumentációjában](https://mariadb.com/kb/en/library/slow-query-log-overview/) találja.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for MariaDB integrálva van Azure Monitor diagnosztikai naplókba. Ha engedélyezte a lassú lekérdezési naplókat a MariaDB-kiszolgálón, kiválaszthatja, hogy Azure Monitor naplók, Event Hubs vagy az Azure Storage szolgáltatásban. A diagnosztikai naplók engedélyezésével kapcsolatos további tudnivalókért tekintse meg a [diagnosztikai naplók dokumentációjának](../azure-monitor/platform/resource-logs-overview.md)útmutató szakaszát.

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
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | A kiszolgáló neve |
| `start_time_t` [UTC] | A lekérdezés elkezdésének ideje |
| `query_time_s` | A lekérdezés végrehajtásának teljes ideje |
| `lock_time_s` | A lekérdezés zárolásának teljes ideje |
| `user_host_s` | Felhasználónév |
| `rows_sent_s` | Elérkezett sorok száma |
| `rows_examined_s` | Megvizsgált sorok száma |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | AZONOSÍTÓ beszúrása |
| `sql_text_s` | Teljes lekérdezés |
| `server_id_s` | Kiszolgáló azonosítója |
| `thread_id_s` | Szál azonosítója |
| `\_ResourceId` | Erőforrás URI-ja |

## <a name="next-steps"></a>Következő lépések
- [A kiszolgálók naplófájljainak konfigurálása és elérése a Azure Portal](howto-configure-server-logs-portal.md).
