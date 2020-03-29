---
title: Naplók naplózása – A MariaDB Azure-adatbázisa
description: A Naplózási szintek engedélyezéséhez rendelkezésre álló naplók at ismerteti az Azure Database for MariaDB-ben elérhető napló.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063816"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Naplók naplózása a MariaDB Azure-adatbázisában

A MariaDB Azure Database-ben a napló elérhető a felhasználók számára. A napló adatbázis-szintű tevékenységek nyomon követésére használható, és gyakran használják a megfelelőséghez.

> [!IMPORTANT]
> A napló funkciója jelenleg előzetes verzióban érhető el.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

Alapértelmezés szerint a napló le van tiltva. Az engedélyezéshez `audit_log_enabled` állítsa be a beállítását.

További beállítások a következők:

- `audit_log_events`: szabályozza a naplózandó eseményeket. Az alábbi táblázatban konkrét naplózási eseményeket talál.
- `audit_log_include_users`: MariaDB felhasználók at kell venni a naplózáshoz. A paraméter alapértelmezett értéke üres, amely a naplózáshoz szükséges összes felhasználót tartalmazza. Ez magasabb prioritást élvez a . `audit_log_exclude_users` A paraméter maximális hossza 512 karakter.
> [!Note]
> `audit_log_include_users`elsőbbséget élvez `audit_log_exclude_users`. Ha például `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`a felhasználó szerepelni fog a naplónaplókban, mert `audit_log_include_users` magasabb prioritású.
- `audit_log_exclude_users`: MariaDB-felhasználók ki lesznek zárva a naplózásból. Legtöbb felhasználó számára teszi lehetővé a felhasználók számára. A paraméter maximális hossza 256 karakter.

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

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

A naplónaplók integrálva vannak az Azure Monitor diagnosztikai naplóival. Miután engedélyezte a naplózási naplókat a MariaDB-kiszolgálón, kibocsáthatja őket az Azure Monitor naplóiba, az Event Hubs-ba vagy az Azure Storage-ba. Ha többet szeretne tudni arról, hogyan engedélyezheti a diagnosztikai naplókat az Azure Portalon, tekintse meg a [naplózási portál cikket.](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)

## <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémája

A következő szakaszok ismertetik, mi a kimenet a MariaDB naplózási naplók az esemény típusa alapján. A kimeneti módszertől függően a belefoglalt mezők és megjelenésük sorrendje eltérő lehet.

### <a name="connection"></a>Kapcsolat

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlőazonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Időbélyegző, amikor a naplót rögzítették UTC-ben |
| `Type` | A napló típusa. Mindig`AzureDiagnostics` |
| `SubscriptionId` | Guid az előfizetéshez, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Annak az erőforráscsoportnak a neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | A MariaDB által létrehozott egyedi kapcsolatazonosító |
| `host_s` | Blank |
| `ip_s` | A MariaDB-hez csatlakozó ügyfél IP-címe |
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
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Lekérdezésindítási másodpercei UNIX-időbélyegben |
| `error_code_d` | Hibakód, ha a lekérdezés nem sikerült. `0`azt jelenti, nincs hiba |
| `thread_id_d` | A lekérdezést elnem előzékelő szál azonosítója |
| `host_s` | Blank |
| `ip_s` | A MariaDB-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
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

- Naplózott események listázása az összes MariaDB-kiszolgálón, amelyeken a diagnosztikai naplók engedélyezve vannak a naplókhoz

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>További lépések

- [A naplónaplók konfigurálása az Azure Portalon](howto-configure-audit-logs-portal.md)