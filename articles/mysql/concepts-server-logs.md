---
title: Az Azure Database for MySQL Server-naplók
description: Az Azure Database-ben elérhető naplók a MySQL és a különböző naplózási szintek engedélyezésének paramétereket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: bc3dd038a21786a75d6b519ef8523dc328829705
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192888"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Kiszolgálói naplók az Azure Database for MySQL-hez
Az Azure Database for MySQL-hez a lassú lekérdezések naplója a felhasználók számára érhető el. A tranzakciós naplóba való hozzáférés nem támogatott. A lassú lekérdezések naplója segítségével azonosíthatja a szűk keresztmetszeteket hibaelhárításhoz. 

A MySQL lassú lekérdezések naplója kapcsolatos további információkért tekintse meg a MySQL referencia manuális [lassú lekérdezési szakasz](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Hozzáférés a kiszolgálói naplókhoz
A listában, és az Azure-adatbázis le az MySQL server-naplók az Azure Portalon, és az Azure CLI használatával.

Az Azure Portalon válassza ki az Azure Database for MySQL-kiszolgálóhoz. Alatt a **figyelés** szakaszban kattintson a **kiszolgálónaplók** lapot.

Az Azure CLI-vel további információkért lásd: [Azure CLI-vel kiszolgálónaplók konfigurálása és a hozzáférés](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Napló megőrzése
Naplók létrehozás legfeljebb hét napig érhetők el. Naplók összesített mérete meghaladja a 7 GB, ha ezután a legrégebbi fájlok törlődnek mindaddig, amíg a lemezterület áll rendelkezésre. 

Naplók vannak-e forgatni minden 24 órás vagy azt 7 GB, amelyik először bekövetkezik.


## <a name="configure-logging"></a>Naplózás konfigurálása 
A lassú lekérdezések naplója alapértelmezés szerint le van tiltva. Annak engedélyezéséhez állítsa slow_query_log ON.

Egyéb úgy módosíthatja a paraméterek a következők:

- **long_query_time**: Ha a lekérdezés hosszabb időt vesz igénybe, mint a long_query_time (másodpercben), hogy a lekérdezés a rendszer naplózza. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements**: Ha ON az utasításokban a slow_query_log írt felügyeleti utasítások, mint például a ALTER_TABLE és ANALYZE_TABLE tartalmazza.
- **log_queries_not_using_indexes**: azt határozza meg, hogy lekérdezések, amelyek nem használnak az indexek a slow_query_log naplózza
- **log_throttle_queries_not_using_indexes**: Ezt a paramétert csak írható a lassú lekérdezések naplója nem index lekérdezések számát korlátozza. Ezt a paramétert akkor lép érvénybe, ha log_queries_not_using_indexes ON értékre van állítva.

Tekintse meg a MySQL [lassú lekérdezési napló dokumentáció](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) teljes leírását a lassú lekérdezések naplója paramétereket.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for MySQL integrálva van az Azure monitort, diagnosztikai naplók. A MySQL-kiszolgáló lassú lekérdezési naplóinak engedélyezése után kiválaszthatja azokat az Azure Monitor naplók, az Event Hubs vagy Azure Storage kibocsátott. Diagnosztikai naplók engedélyezésével kapcsolatos további tudnivalókért lásd: szakaszában az útmutató a [diagnosztikai naplók dokumentáció](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Ezt a diagnosztikai naplók funkciót csak érhető el az általános célú és memóriahasználatra optimalizált [tarifacsomagok](concepts-pricing-tiers.md).

A következő táblázat ismerteti, mi az egyes naplókhoz. A kimeneti módszertől függően a mezők és a megjelenési sorrendben eltérőek lehetnek.

| **Tulajdonság** | **Leírás** |
|---|---|---|
| TenantId | A bérlő azonosítója |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Időbélyeg mikor lett rögzítve a napló (UTC) |
| Typo | A napló típusa. Mindig `AzureDiagnostics` |
| SubscriptionId | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| ResourceGroup | A kiszolgáló tartozik az erőforráscsoport neve |
| ResourceProvider | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| ResourceType | `Servers` |
| ResourceId | Erőforrás-URI |
| Erőforrás | A kiszolgáló neve |
| Kategória | `MySqlSlowLogs` |
| OperationName | `LogEvent` |
| Logical_server_name_s | A kiszolgáló neve |
| start_time_t [UTC] | A lekérdezés kezdetének időpontja |
| query_time_s | A lekérdezés végrehajtásához szükséges idő összesen |
| lock_time_s | A lekérdezés zárolva volt idő összesen |
| user_host_s | Felhasználónév |
| rows_sent_s | Elküldött sorok száma |
| rows_examined_s | Vizsgált sorok száma |
| last_insert_id_s | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| insert_id_s | Id beszúrása |
| sql_text_s | Teljes lekérdezést |
| server_id_s | A kiszolgáló azonosítója |
| thread_id_s | Id vlákna |
| \_ResourceId | Erőforrás-URI |

## <a name="next-steps"></a>További lépések
- [Konfigurálása és a naplók eléréséhez az Azure parancssori felületen](howto-configure-server-logs-in-cli.md).
