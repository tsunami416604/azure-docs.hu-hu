---
title: Naplók naplózása – Azure-adatbázis a MySQL-hez
description: Ismerteti a naplózási szintek engedélyezéséhez rendelkezésre álló naplózási naplók at az Azure Database for MySQL-ben elérhető naplókat.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062539"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Naplók naplózása a MySQL Azure-adatbázisában

Az Azure Database for MySQL, a napló a felhasználók számára elérhető. A napló adatbázis-szintű tevékenységek nyomon követésére használható, és gyakran használják a megfelelőséghez.

> [!IMPORTANT]
> A napló funkciója jelenleg előzetes verzióban érhető el.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

Alapértelmezés szerint a napló le van tiltva. Az engedélyezéshez `audit_log_enabled` állítsa be a beállítását.

További beállítások a következők:

- `audit_log_events`: szabályozza a naplózandó eseményeket. Az alábbi táblázatban konkrét naplózási eseményeket talál.
- `audit_log_include_users`: A MySQL felhasználók at kell venni a naplózáshoz. A paraméter alapértelmezett értéke üres, amely a naplózáshoz szükséges összes felhasználót tartalmazza. Ez magasabb prioritást élvez a . `audit_log_exclude_users` A paraméter maximális hossza 512 karakter.
> [!Note]
> `audit_log_include_users`elsőbbséget élvez `audit_log_exclude_users`. Ha például `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`a felhasználó szerepelni fog a naplónaplókban, mert `audit_log_include_users` magasabb prioritású.
- `audit_log_exclude_users`: A MySQL-felhasználókat ki kell zárni a naplózásból. A paraméter maximális hossza 512 karakter.

> [!Note]
> A `sql_text`esetén a napló csonkul, ha az meghaladja a 2048 karaktert.

| **Esemény** | **Leírás** |
|---|---|
| `CONNECTION` | - Csatlakozás kezdeményezése (sikeres vagy sikertelen) <br> - Felhasználó újrahitelesítése különböző felhasználó / jelszó a munkamenet során <br> - Kapcsolat leállítása |
| `DML_SELECT`| SELECT lekérdezések |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE lekérdezések |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Lekérdezések, mint a "DROP DATABASE" |
| `DCL` | Lekérdezések, mint a "GRANT PERMISSION" |
| `ADMIN` | Lekérdezések, mint a "SHOW STATUS" |
| `GENERAL` | Minden DML_SELECT, DML_NONSELECT, DML, DDL, DCL és ADMIN |
| `TABLE_ACCESS` | - Csak a MySQL 5.7 <br> - Táblázat olvasási utasítások, mint például a SELECT vagy insert into ... Válassza ki <br> - Táblázat törlése utasítások, mint a DELETE vagy csonka táblázat <br> - Táblázat beszúrási utasítások, például INSERT vagy REPLACE <br> - Táblázat frissítési utasítások, mint például az UPDATE |

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

A naplónaplók integrálva vannak az Azure Monitor diagnosztikai naplóival. Miután engedélyezte a naplózási naplókat a MySQL-kiszolgálón, kibocsáthatja őket az Azure Monitor naplóiba, az Event Hubs-ba vagy az Azure Storage-ba. Ha többet szeretne tudni arról, hogyan engedélyezheti a diagnosztikai naplókat az Azure Portalon, tekintse meg a [naplózási portál cikket.](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)

## <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémája

A következő szakaszok ismertetik, mi a MySQL naplózási naplók az esemény típusa alapján. A kimeneti módszertől függően a belefoglalt mezők és megjelenésük sorrendje eltérő lehet.

### <a name="connection"></a>Kapcsolat

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlőazonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Időbélyegző, amikor a naplót rögzítették UTC-ben |
| `Type` | A napló típusa. Mindig`AzureDiagnostics` |
| `SubscriptionId` | Guid az előfizetéshez, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Annak az erőforráscsoportnak a neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` `CHANGE USER` , (csak a MySQL 5.7 esetén érhető el) |
| `connection_id_d` | A MySQL által létrehozott egyedi kapcsolatazonosító |
| `host_s` | Blank |
| `ip_s` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `db_s` | A kapcsolódó adatbázis neve |
| `\_ResourceId` | Erőforrás URI-ja |

### <a name="general"></a>Általános kérdések

Az alábbi séma az ÁLTALÁNOS, DML_SELECT, DML_NONSELECT, DML, DDL, DCL és ADMIN eseménytípusokra vonatkozik.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlőazonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Időbélyegző, amikor a naplót rögzítették UTC-ben |
| `Type` | A napló típusa. Mindig`AzureDiagnostics` |
| `SubscriptionId` | Guid az előfizetéshez, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Annak az erőforráscsoportnak a neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` `RESULT` , (csak a MySQL 5.6 esetén érhető el) |
| `event_time` | Lekérdezés indítási időpontja UTC-időbélyegben |
| `error_code_d` | Hibakód, ha a lekérdezés nem sikerült. `0`azt jelenti, nincs hiba |
| `thread_id_d` | A lekérdezést elnem előzékelő szál azonosítója |
| `host_s` | Blank |
| `ip_s` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `sql_text_s` | Teljes lekérdezés szövege |
| `\_ResourceId` | Erőforrás URI-ja |

### <a name="table-access"></a>Táblahozzáférés

> [!NOTE]
> A table access naplók csak a MySQL 5.7 kimenetére szolgálnak.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlőazonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Időbélyegző, amikor a naplót rögzítették UTC-ben |
| `Type` | A napló típusa. Mindig`AzureDiagnostics` |
| `SubscriptionId` | Guid az előfizetéshez, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Annak az erőforráscsoportnak a neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` `UPDATE`, vagy`DELETE` |
| `connection_id_d` | A MySQL által létrehozott egyedi kapcsolatazonosító |
| `db_s` | Az elért adatbázis neve |
| `table_s` | Az elért tábla neve |
| `sql_text_s` | Teljes lekérdezés szövege |
| `\_ResourceId` | Erőforrás URI-ja |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Naplók elemzése az Azure Figyelő naplóiban

Miután a naplónaplók at az Azure Monitor naplók diagnosztikai naplók on keresztül, a naplózott események további elemzését végezheti el. Az alábbiakban néhány mintalekérdezést talál az első lépések hez. Győződjön meg róla, hogy frissítse az alábbi a szerver nevét.

- ÁLTALÁNOS események listázása egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- KAPCSOLATi események listázása egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Ellenőrzött események összegzése egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- A naplózási esemény típusának eloszlásának grafikonja egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Naplózott események listázása az összes MySQL-kiszolgálón, ha a diagnosztikai naplók engedélyezve vannak a naplók naplózásához

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>További lépések

- [A naplónaplók konfigurálása az Azure Portalon](howto-configure-audit-logs-portal.md)