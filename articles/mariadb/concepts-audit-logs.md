---
title: Auditnaplók az Azure Database for MariaDB
description: Azure Database-ben elérhető auditnaplók MariaDB, és a naplózási szintek engedélyezésének paramétereket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 13ea60c62283db35ce4bf9fde6c3b36ba7f88013
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439220"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Naplózási naplók az Azure Database for MariaDB

Azure Database for MariaDB-ben a napló a felhasználók számára érhető el. Az auditnapló adatbázisszintű tevékenységek nyomon követésére használható, és megfelelőségi gyakran használják.

> [!IMPORTANT]
> Auditálási napló funkció jelenleg előzetes verzióban érhető el.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

A napló alapértelmezés szerint le van tiltva. Annak engedélyezéséhez állítsa `audit_log_enabled` ON értékre állítása.

Egyéb úgy módosíthatja a paraméterek a következők:

- `audit_log_events`: az események naplózását szabályozza. Lásd az alábbi táblázat az adott naplózási események.
- `audit_log_exclude_users`: A MariaDB felhasználók ki lesznek zárva a naplózást. Lehetővé teszi, hogy legfeljebb négy felhasználó. A paraméter a maximális hossz 256 karakter.

| **Esemény** | **Leírás** |
|---|---|
| `CONNECTION` | – Csatlakozás kezdeményezéséhez (sikeres vagy sikertelen) <br> -Felhasználó újbóli hitelesítés különböző felhasználó/jelszó munkamenet során <br> – A kapcsolat megszüntetése |
| `DML_SELECT`| A VÁLASZTÓ lekérdezések |
| `DML_NONSELECT` | BESZÚRÁSI vagy törlési/frissítési lekérdezések |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Például a "DROP DATABASE" lekérdezések |
| `DCL` | Például a "ENGEDÉLYEZHETI" lekérdezések |
| `ADMIN` | Például a "ÁLLAPOTKIJELZÉS" lekérdezések |
| `GENERAL` | Az összes a DML_SELECT DML_NONSELECT, DML, DDL, Kapcsolattárak,., rendszergazda |

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

Naplók integrálva vannak az Azure monitort, diagnosztikai naplók. Miután engedélyezte a naplók a MariaDB-kiszolgálón, az Azure Monitor naplók, az Event Hubs vagy Azure Storage kibocsátható őket. Diagnosztikai naplók az Azure Portalon engedélyezésével kapcsolatos további tudnivalókért tekintse meg a [auditálási napló portál cikk](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémák

A következő szakaszok ismertetik, mit jelent az eseménytípus alapján MariaDB auditnaplók kimenete. A kimeneti módszertől függően a mezők és a megjelenési sorrendben eltérőek lehetnek.

### <a name="connection"></a>Kapcsolat

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Időbélyeg mikor lett rögzítve a napló (UTC) |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| `ResourceGroup` | A kiszolgáló tartozik az erőforráscsoport neve |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás-URI |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | A MariaDB által létrehozott egyedi Kapcsolatazonosító |
| `host_s` | Üres |
| `ip_s` | Kapcsolódás a MariaDB ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `db_s` | Csatlakoztatott adatbázis neve |
| `\_ResourceId` | Erőforrás-URI |

### <a name="general"></a>Általános kérdések

Az alábbi séma általános, DML_SELECT, DML_NONSELECT, DML, DDL, Kapcsolattárak és felügyeleti eseménytípusok vonatkozik.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Időbélyeg mikor lett rögzítve a napló (UTC) |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| `ResourceGroup` | A kiszolgáló tartozik az erőforráscsoport neve |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás-URI |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Lekérdezés indítsa el a másodperc, a UNIX-időbélyege |
| `error_code_d` | Hibakód: Ha a lekérdezés nem sikerült. `0` azt jelenti, hogy nem történt hiba |
| `thread_id_d` | A lekérdezés végrehajtása szál azonosítója |
| `host_s` | Üres |
| `ip_s` | Kapcsolódás a MariaDB ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `sql_text_s` | A lekérdezés teljes szövege |
| `\_ResourceId` | Erőforrás-URI |

### <a name="table-access"></a>Tábla hozzáférés

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Időbélyeg mikor lett rögzítve a napló (UTC) |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| `ResourceGroup` | A kiszolgáló tartozik az erőforráscsoport neve |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás-URI |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`, vagy `DELETE` |
| `connection_id_d` | A MariaDB által létrehozott egyedi Kapcsolatazonosító |
| `db_s` | Elérhető adatbázis neve |
| `table_s` | Elért tábla neve |
| `sql_text_s` | A lekérdezés teljes szövege |
| `\_ResourceId` | Erőforrás-URI |

## <a name="next-steps"></a>További lépések

- [Naplók konfigurálása az Azure Portalon](howto-configure-audit-logs-portal.md)