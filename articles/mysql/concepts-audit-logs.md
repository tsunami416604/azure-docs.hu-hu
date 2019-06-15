---
title: Auditnaplók az Azure Database for MySQL-hez
description: Azure Database-ben elérhető auditnaplók a MySQL és a naplózási szintek engedélyezésének paramétereket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 35cbe04380e2113f986d1e7adf3f7fdf89cb9326
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078871"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Auditnaplók az Azure Database for MySQL-hez

Az Azure Database for MySQL-hez a napló a felhasználók számára érhető el. Az auditnapló adatbázisszintű tevékenységek nyomon követésére használható, és megfelelőségi gyakran használják.

> [!IMPORTANT]
> Auditálási napló funkció jelenleg előzetes verzióban érhető el.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

A napló alapértelmezés szerint le van tiltva. Annak engedélyezéséhez állítsa `audit_log_enabled` ON értékre állítása.

Egyéb úgy módosíthatja a paraméterek a következők:

- `audit_log_events`: az események naplózását szabályozza. Lásd az alábbi táblázat az adott naplózási események.
- `audit_log_exclude_users`: MySQL-felhasználók ki lesznek zárva a naplózást. Lehetővé teszi, hogy legfeljebb négy felhasználó. A paraméter a maximális hossz 256 karakter.

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
| `TABLE_ACCESS` | -A MySQL 5.7 csak érhető el. <br> -Táblázat például VÁLASSZA vagy az INSERT INTO beolvasása... SELECT <br> -Table delete utasítások, mint például a TRUNCATE TABLE vagy törlése <br> -Table insert utasítások, például BESZÚRÁSA vagy LECSERÉLÉSE <br> -Table update utasításokban, mint a frissítés |

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

Naplók integrálva vannak az Azure monitort, diagnosztikai naplók. A MySQL-kiszolgáló a vizsgálati naplók engedélyezése, után az Azure Monitor naplók, az Event Hubs vagy Azure Storage kibocsátható őket. Diagnosztikai naplók az Azure Portalon engedélyezésével kapcsolatos további tudnivalókért tekintse meg a [auditálási napló portál cikk](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="schemas"></a>Sémák

A következő szakaszok ismertetik, mit jelent az eseménytípus alapján MySQL auditnaplók kimenete. A kimeneti módszertől függően a mezők és a megjelenési sorrendben eltérőek lehetnek.

### <a name="connection"></a>Kapcsolat

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Időbélyeg mikor lett rögzítve a napló (UTC) |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| `ResourceGroup` | A kiszolgáló tartozik az erőforráscsoport neve |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás-URI |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (csak MySQL 5.7-es érhető el) |
| `connection_id` | MySQL által létrehozott egyedi Kapcsolatazonosító |
| `host` | Üres |
| `ip` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user` | A lekérdezést végrehajtó felhasználó neve |
| `db` | Csatlakoztatott adatbázis neve |
| `\_ResourceId` | Erőforrás-URI |

### <a name="general"></a>Általános kérdések

Az alábbi séma általános, DML_SELECT, DML_NONSELECT, DML, DDL, Kapcsolattárak és felügyeleti eseménytípusok vonatkozik.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Időbélyeg mikor lett rögzítve a napló (UTC) |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| `ResourceGroup` | A kiszolgáló tartozik az erőforráscsoport neve |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás-URI |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` (csak MySQL 5.6-os érhető el) |
| `event_time` | Lekérdezés indítsa el a másodperc, a UNIX-időbélyege |
| `error_code` | Hibakód: Ha a lekérdezés nem sikerült. `0` azt jelenti, hogy nem történt hiba |
| `thread_id` | A lekérdezés végrehajtása szál azonosítója |
| `host` | Üres |
| `ip` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user` | A lekérdezést végrehajtó felhasználó neve |
| `sql_text` | A lekérdezés teljes szövege |
| `\_ResourceId` | Erőforrás-URI |

### <a name="table-access"></a>Tábla hozzáférés

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Időbélyeg mikor lett rögzítve a napló (UTC) |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| `ResourceGroup` | A kiszolgáló tartozik az erőforráscsoport neve |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás-URI |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, vagy `DELETE` |
| `connection_id` | MySQL által létrehozott egyedi Kapcsolatazonosító |
| `db` | Elérhető adatbázis neve |
| `table` | Elért tábla neve |
| `sql_text` | A lekérdezés teljes szövege |
| `\_ResourceId` | Erőforrás-URI |

## <a name="next-steps"></a>További lépések

- [Naplók konfigurálása az Azure Portalon](howto-configure-audit-logs-portal.md)