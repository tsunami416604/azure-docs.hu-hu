---
title: Naplók – Azure Database for MariaDB
description: Ismerteti a Azure Database for MariaDBban elérhető naplókat, valamint a naplózási szintek engedélyezéséhez elérhető paramétereket.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: e66eaf62b54759222421b17ebc7e33b82e018338
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541759"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Naplók naplózása Azure Database for MariaDB

A Azure Database for MariaDB a napló a felhasználók számára érhető el. A napló az adatbázis szintű tevékenységek nyomon követésére használható, és általában a megfelelőséghez használatos.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Azt javasoljuk, hogy csak azokat az eseményeket és felhasználókat naplózza, amelyek szükségesek a naplózáshoz, hogy a kiszolgáló teljesítménye ne legyen nagy hatással.

Alapértelmezés szerint a napló le van tiltva. Az engedélyezéséhez állítsa be értékre a következőre: `audit_log_enabled` .

Az egyéb paraméterek a következők:

- `audit_log_events`: a naplózandó események szabályozása. Tekintse meg az alábbi táblázatot az egyes naplózási eseményekhez.
- `audit_log_include_users`: A naplózáshoz MariaDB felhasználókat. A paraméter alapértelmezett értéke üres, amely tartalmazza a naplózáshoz szükséges összes felhasználót. Ennek magasabb prioritása van `audit_log_exclude_users` . A paraméter maximális hossza 512 karakter.
- `audit_log_exclude_users`: A MariaDB a naplózásból kizárandó felhasználókat. Legfeljebb négy felhasználó számára teszi lehetővé. A paraméter maximális hossza 256 karakter.

> [!Note]
> `audit_log_include_users` magasabb prioritással rendelkezik `audit_log_exclude_users` . Ha például `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser` a és a, a felhasználó belekerül a naplóba, mert `audit_log_include_users` magasabb prioritással rendelkezik.

| **Esemény** | **Leírás** |
|---|---|
| `CONNECTION` | – A kapcsolatok kezdeményezése (sikeres vagy sikertelen) <br> – A felhasználó újrahitelesítése eltérő felhasználóval/jelszóval a munkamenet során <br> – A kapcsolatok megszakítása |
| `DML_SELECT`| Lekérdezések kiválasztása |
| `DML_NONSELECT` | Lekérdezések beszúrása/törlése/frissítése |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Lekérdezések, például "DROP DATABASE" |
| `DCL` | Lekérdezések, például "engedély megadása" |
| `ADMIN` | Lekérdezések, például "állapot megjelenítése" |
| `GENERAL` | Összes DML_SELECT, DML_NONSELECT, DML, DDL, DCL és ADMIN |

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

A naplók az Azure Monitor Diagnostic Logs szolgáltatással vannak integrálva. Miután engedélyezte a naplókat a MariaDB-kiszolgálón, kibocsáthatja azokat az Azure Monitor-naplókba, az Event Hubsba vagy az Azure Storage-ba. Ha szeretne többet megtudni arról, hogyan engedélyezheti a diagnosztikai naplókat a Azure Portalban, tekintse meg a [napló-portált ismertető cikket](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémái

A következő szakaszok azt írják le, hogy milyen kimenetet MariaDB a naplók az esemény típusa alapján. A kimeneti módszertől függően a befoglalt mezők és a megjelenő sorrend eltérő lehet.

### <a name="connection"></a>Kapcsolat

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | A MariaDB által generált egyedi kapcsolatazonosító |
| `host_s` | Blank |
| `ip_s` | Az MariaDB-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `db_s` | A következőhöz kapcsolódó adatbázis neve |
| `\_ResourceId` | Erőforrás URI-ja |

### <a name="general"></a>Általános kérdések

Az alábbi séma az általános, a DML_SELECT, a DML_NONSELECT, a DML, a DDL, a DCL és a rendszergazdai események típusára vonatkozik.

> [!NOTE]
> A esetében a rendszer `sql_text` csonkolja a naplót, ha az meghaladja a 2048 karaktert.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Lekérdezés kezdő másodperce a UNIX időbélyegzőben |
| `error_code_d` | Hibakód, ha a lekérdezés nem sikerült. `0` nem jelent hibát |
| `thread_id_d` | A lekérdezést futtató szál azonosítója |
| `host_s` | Blank |
| `ip_s` | Az MariaDB-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `sql_text_s` | Teljes lekérdezés szövege |
| `\_ResourceId` | Erőforrás URI-ja |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Naplók elemzése Azure Monitor naplókban

Miután a naplókat a diagnosztikai naplókon keresztül Azure Monitor naplókra, a naplózott események további elemzését is elvégezheti. Az alábbiakban néhány példa olvasható, amely segítséget nyújt az első lépésekhez. Győződjön meg arról, hogy az alábbit frissíti a kiszolgáló nevével.

- Egy adott kiszolgálón lévő általános események listázása

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Egy adott kiszolgálón található kapcsolatok eseményeinek listázása

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Naplózott események összefoglalása egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- A naplózási esemény típusának eloszlása egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- A naplózott események listázása az összes MariaDB-kiszolgálón a naplók számára engedélyezett diagnosztikai naplókkal

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Következő lépések

- [Naplók konfigurálása a Azure Portalban](howto-configure-audit-logs-portal.md)