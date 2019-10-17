---
title: Azure Database for MySQL naplózási naplói
description: Ismerteti a Azure Database for MySQLban elérhető naplókat, valamint a naplózási szintek engedélyezéséhez elérhető paramétereket.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 42881fcb12f29ec14bbdc0ec4942b2eef17c7312
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434400"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Naplók naplózása Azure Database for MySQL

A Azure Database for MySQL a napló a felhasználók számára érhető el. A napló az adatbázis szintű tevékenységek nyomon követésére használható, és általában a megfelelőséghez használatos.

> [!IMPORTANT]
> A naplózási funkció jelenleg előzetes verzióban érhető el.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

Alapértelmezés szerint a napló le van tiltva. A beállítás engedélyezéséhez állítsa a `audit_log_enabled` értéket a következőre:.

Az egyéb paraméterek a következők:

- `audit_log_events`: a naplózandó események szabályozása. Tekintse meg az alábbi táblázatot az egyes naplózási eseményekhez.
- `audit_log_include_users`: a naplózáshoz a MySQL-felhasználók tartoznak. A paraméter alapértelmezett értéke üres, amely tartalmazza a naplózáshoz szükséges összes felhasználót. Ez magasabb prioritással rendelkezik `audit_log_exclude_users` felett. A paraméter maximális hossza 512 karakter.
> [!Note]
> a `audit_log_include_users` magasabb prioritással rendelkezik a `audit_log_exclude_users` esetében, például ha a audit_log_include_users = `demouser` és a audit_log_exclude_users = `demouser`, akkor a naplózza a naplókat, mert a `audit_log_include_users` magasabb prioritással rendelkezik.
- `audit_log_exclude_users`: a naplózásból kizárandó MySQL-felhasználók. A paraméter maximális hossza 512 karakter.

> [!Note]
> @No__t – 0 esetén a rendszer csonkolja a naplót, ha az meghaladja a 2048 karaktert.

| **Esemény** | **Leírás** |
|---|---|
| `CONNECTION` | – A kapcsolatok kezdeményezése (sikeres vagy sikertelen) <br> – A felhasználó újrahitelesítése eltérő felhasználóval/jelszóval a munkamenet során <br> – A kapcsolatok megszakítása |
| `DML_SELECT`| Lekérdezések kiválasztása |
| `DML_NONSELECT` | Lekérdezések beszúrása/törlése/frissítése |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Lekérdezések, például "DROP DATABASE" |
| `DCL` | Lekérdezések, például "engedély megadása" |
| `ADMIN` | Lekérdezések, például "állapot megjelenítése" |
| `GENERAL` | Mind a DML_SELECT, a DML_NONSELECT, a DML, a DDL, a DCL és a ADMIN |
| `TABLE_ACCESS` | – Csak MySQL 5,7 esetén érhető el <br> – Táblázatos olvasási utasítások, például kijelölés vagy Beszúrás a következőbe:... Válassza <br> – Tábla-törlési utasítások, például törlés vagy TRUNCATE TABLE <br> – Táblázatos beszúrási utasítások, például INSERT vagy replace <br> – Táblázatos frissítési utasítások, például frissítés |

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

A naplófájlok integrálva vannak Azure Monitor diagnosztikai naplókba. Miután engedélyezte a naplók használatát a MySQL-kiszolgálón, kibocsáthatja őket Azure Monitor naplókba, Event Hubsba vagy az Azure Storage szolgáltatásba. Ha szeretne többet megtudni arról, hogyan engedélyezheti a diagnosztikai naplókat a Azure Portalban, tekintse meg a [napló-portált ismertető cikket](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémái

A következő szakaszok ismertetik a MySQL-naplók kimenetét az esemény típusa alapján. A kimeneti módszertől függően a befoglalt mezők és a megjelenő sorrend eltérő lehet.

### <a name="connection"></a>Kapcsolat

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig @no__t – 0 |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig @no__t – 0 |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (csak MySQL 5,7 esetén érhető el) |
| `connection_id_d` | A MySQL által generált egyedi kapcsolatazonosító |
| `host_s` | Üres |
| `ip_s` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `db_s` | A következőhöz kapcsolódó adatbázis neve |
| `\_ResourceId` | Erőforrás URI-ja |

### <a name="general"></a>Általános

Az alábbi séma az általános, a DML_SELECT, a DML_NONSELECT, a DML, a DDL, a DCL és a rendszergazdai események típusára vonatkozik.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig @no__t – 0 |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig @no__t – 0 |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (csak MySQL 5,6 esetén érhető el) |
| `event_time` | Lekérdezés kezdési időpontja (UTC) időbélyegzővel |
| `error_code_d` | Hibakód, ha a lekérdezés nem sikerült. a `0` nem jelent hibát. |
| `thread_id_d` | A lekérdezést futtató szál azonosítója |
| `host_s` | Üres |
| `ip_s` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `sql_text_s` | Teljes lekérdezés szövege |
| `\_ResourceId` | Erőforrás URI-ja |

### <a name="table-access"></a>Tábla-hozzáférés

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig @no__t – 0 |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig @no__t – 0 |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` vagy `DELETE` |
| `connection_id_d` | A MySQL által generált egyedi kapcsolatazonosító |
| `db_s` | Az elért adatbázis neve |
| `table_s` | Az elért tábla neve |
| `sql_text_s` | Teljes lekérdezés szövege |
| `\_ResourceId` | Erőforrás URI-ja |

## <a name="next-steps"></a>Következő lépések

- [Naplók konfigurálása a Azure Portalban](howto-configure-audit-logs-portal.md)