---
title: Auditnapló formátuma
description: Ismerje meg, hogyan épülnek fel az SQL Database naplózási naplói.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722084"
---
# <a name="sql-database-audit-log-format"></a>SQL-adatbázis naplózási naplójának formátuma

[Az Azure SQL Database naplózása](sql-database-auditing.md) nyomon követi az adatbázis-eseményeket, és naplóba írja őket az Azure storage-fiókjában, vagy elküldi őket az Event Hubvagy a Log Analytics későbbi feldolgozásra és elemzésre.

## <a name="naming-conventions"></a>Elnevezési konvenciók

### <a name="blob-audit"></a>Blob-naplózás

A Blob storage-ban tárolt naplónaplók az `sqldbauditlogs` Azure Storage-fiókban elnevezett tárolóban tárolótárolhatók. A tárolón belüli könyvtárhierarchia az űrlapból áll. `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` A Blob fájlnév `<CreationTime>_<FileNumberInSession>.xel`formátuma , `CreationTime` `hh_mm_ss_ms` ahol UTC formátumban van, és `FileNumberInSession` egy futó index abban az esetben, ha a munkamenetnaplók több Blob-fájlra terjednek ki.

Például az `Database1` adatbázis `Server1` a következő egy lehetséges érvényes elérési utat:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Írásvédett replikák](sql-database-read-scale-out.md) A naplónaplók ugyanabban a tárolóban tárolódnak. A tárolón belüli könyvtárhierarchia az űrlapból áll. `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` A Blob fájlnév ugyanazt a formátumot osztja meg. Az írásvédett replikák naplói ugyanabban a tárolóban tárolódnak.


### <a name="event-hub"></a>Eseményközpont

A naplózási események a naplózási konfiguráció során definiált névtérbe és eseményközpontba kerülnek, és az [Apache Avro-események](https://avro.apache.org/) törzsében kerülnek rögzítésre, és UTF-8 kódolással json formázással tárolódnak. A naplónaplók olvasásához használhatja az [Avro Tools-t](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) vagy hasonló eszközöket, amelyek ezt a formátumot dolgozzák fel.

### <a name="log-analytics"></a>Log Analytics

A naplózási események a naplózási konfiguráció során definiált `AzureDiagnostics` Log Analytics-munkaterületre kerülnek a kategóriát `SQLSecurityAuditEvents`tartalmazó táblába. A Log Analytics keresési nyelvével és parancsaival kapcsolatos további hasznos tudnivalókat a [Log Analytics keresési hivatkozáscímű témakörben talál.](../log-analytics/log-analytics-log-search.md)

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Naplómezők

| Név (Blob) | Név (Event Hubs/Log Analytics) | Leírás | Blob típusa | Eseményközpontok/Log analytics típusa |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | A művelet azonosítója | varchar[4] | sztring |
| action_name | action_name_s | A művelet neve | N/A | sztring |
| additional_information | additional_information_s | Az eseményről xml formátumban tárolt további információk | nvarchar(4000) | sztring |
| affected_rows | affected_rows_d | A lekérdezés által érintett sorok száma | bigint | int |
| application_name | application_name_s| Az ügyfélalkalmazás neve | nvarchar(128) | sztring |
| audit_schema_version | audit_schema_version_d | Mindig 1 | int | int |
| class_type | class_type_s | Annak a könyvvizsgálatnak az típusa, amelyen a könyvvizsgálat történik | varchar[2] | sztring |
| class_type_desc | class_type_description_s | Annak a könyvvizsgálatnak az ismertethető entitásának leírása, amelyen a könyvvizsgálat történik | N/A | sztring |
| client_ip | client_ip_s | Az ügyfélalkalmazás forrás IP-címe | nvarchar(128) | sztring |
| connection_id | N/A | A kapcsolat azonosítója a kiszolgálón | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | A naplózott lekérdezés által visszaadott adattípusok és érzékenységi címkék az adatbázis minősített oszlopai alapján. További információ az [Azure SQL Database-adatok felderítéséről és besorolásáról](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | sztring |
| database_name | database_name_s | Az adatbázis-környezet, amelyben a művelet történt | sysname között | sztring |
| database_principal_id | database_principal_id_d | Annak az adatbázis-felhasználói környezetnek az azonosítója, amelyben a műveletet végrehajtják | int | int |
| database_principal_name | database_principal_name_s | Annak az adatbázis-felhasználói környezetnek a neve, amelyben a műveletet végrehajtják | sysname között | sztring |
| duration_milliseconds | duration_milliseconds_d | Lekérdezés végrehajtási időtartama ezredmásodpercben | bigint | int |
| event_time | event_time_t | A naplózható művelet ellépésének dátuma és időpontja | datetime2 | dátum/idő |
| host_name | N/A | Ügyfél állomásneve | sztring | N/A |
| is_column_permission | is_column_permission_s | Jelző, amely azt jelzi, hogy oszlopszintű engedélyről van-e szükség. 1 = igaz, 0 = hamis | bit | sztring |
| N/A | is_server_level_audit_s | Jelző, amely jelzi, hogy a naplózás kiszolgálószinten van-e | N/A | sztring |
| object_ azonosító | object_id_d | Annak az entitásnak az azonosítója, amelyen a könyvvizsgálat történt. Ez magában foglalja a következőket: kiszolgálóobjektumok, adatbázisok, adatbázis-objektumok és sémaobjektumok. 0, ha az entitás maga a kiszolgáló, vagy ha a naplózás nem objektumszinten történik | int | int |
| object_name | object_name_s | Annak az entitásnak a neve, amelyen a könyvvizsgálat történt. Ez magában foglalja a következőket: kiszolgálóobjektumok, adatbázisok, adatbázis-objektumok és sémaobjektumok. 0, ha az entitás maga a kiszolgáló, vagy ha a naplózás nem objektumszinten történik | sysname között | sztring |
| permission_bitmask | permission_bitmask_s | Adott esetben a megadott, megtagadott vagy visszavont engedélyeket jeleníti meg. | varbinary(16) | sztring |
| response_rows | response_rows_d | Az eredményhalmazban visszaadott sorok száma | bigint | int |
| schema_name | schema_name_s | Az a sémakörnyezet, amelyben a művelet történt. NULL a sémán kívül előforduló ellenőrzések esetén | sysname között | sztring |
| N/A | securable_class_type_s | Az ellenőrzött class_type leképező biztonságos objektum | N/A | sztring |
| sequence_group_id | sequence_group_id_g | Egyedi azonosító | varbinary között | GUID |
| sequence_number | sequence_number_d | Egyetlen naplózási rekordon belüli rekordok sorrendjének nyomon követi, amely túl nagy ahhoz, hogy elférjen az írási pufferben az auditok számára | int | int |
| server_instance_name | server_instance_name_s | Annak a kiszolgálópéldánynak a neve, ahol a naplózás történt | sysname között | sztring |
| server_principal_id | server_principal_id_d | Annak a bejelentkezési környezetnek az azonosítója, amelyben a műveletet végrehajtják | int | int |
| server_principal_name | server_principal_name_s | Aktuális bejelentkezés | sysname között | sztring |
| server_principal_sid | server_principal_sid_s | Aktuális bejelentkezési SID | varbinary között | sztring |
| session_id | session_id_d | Annak a munkamenetnek az azonosítója, amelyen az esemény történt | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Egyszerű kiszolgáló a munkamenethez | sysname között | sztring |
| Nyilatkozatot | statement_s | Végrehajtott T-SQL utasítás (ha van ilyen) | nvarchar(4000) | sztring |
| sikeres | succeeded_s | Azt jelzi, hogy az eseményt kiváltó művelet sikeres volt-e. A bejelentkezéstől és a kötegtől eltérő események esetében ez csak azt jelenti, hogy az engedélyellenőrzés sikeres vagy sikertelen volt-e, nem a művelet. 1 = siker, 0 = sikertelen | bit | sztring |
| target_database_principal_id | target_database_principal_id_d | A GRANT/DENY/REVOKE művelet et tartalmazó adatbázis-tag hajtható végre. 0, ha nem alkalmazható | int | int |
| target_database_principal_name | target_database_principal_name_s | A művelet célfelhasználója. NULL, ha nem alkalmazható | sztring | sztring |
| target_server_principal_id | target_server_principal_id_d | A GRANT/DENY/REVOKE művelet egyszerű kiszolgálója. 0 értéket ad eredményül, ha nem alkalmazható. | int | int |
| target_server_principal_name | target_server_principal_name_s | Cél bejelentkezés. NULL, ha nem alkalmazható | sysname között | sztring |
| target_server_principal_sid | target_server_principal_sid_s | A célbejelentkezés sid-je. NULL, ha nem alkalmazható | varbinary között | sztring |
| transaction_id | transaction_id_d | Csak SQL Server (2016-tól kezdődően) - 0 az Azure SQL DB-hez | bigint | int |
| user_defined_event_id | user_defined_event_id_d | A felhasználó által definiált eseményazonosító argumentumként sp_audit_write. NULL a rendszereseményeknél (alapértelmezett) és nem nulla a felhasználó által definiált eseménynél. További információ: [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | A felhasználó által definiált adatok at a sp_audit_write argumentumként adták át. NULL a rendszereseményeknél (alapértelmezett) és nem nulla a felhasználó által definiált eseménynél. További információ: [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | sztring |

## <a name="next-steps"></a>Következő lépések

További információ az [Azure SQL Database naplózásáról.](sql-database-auditing.md)
