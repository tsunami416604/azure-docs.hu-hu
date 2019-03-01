---
title: Az SQL Database naplózási naplóformátum |} A Microsoft Docs
description: Ismerje meg, hogyan SQL-adatbázisok vizsgálati naplóit struktúrája.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 915ccc60216f3f206bcdc53825decac4d6d020d0
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57012027"
---
# <a name="sql-database-audit-log-format"></a>Az SQL Database naplózási naplóformátum

[Az Azure SQL Database naplózási](sql-database-auditing.md) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, az Azure storage-fiókban, vagy alsóbb rétegbeli feldolgozásra és elemzésre az Event Hubs és a Log Analytics küldi őket.

## <a name="naming-conventions"></a>Elnevezési konvenciók

### <a name="blob-audit"></a>Blobnaplózás

A Blob storage-ban tárolt naplók nevű tárolóban vannak tárolva `sqldbauditlogs` az Azure Storage-fiókban. A könyvtár-hierarchia a tárolón belül van, az űrlap `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. A Blob filename formátum `<CreationTime>_<FileNumberInSession>.xel`, ahol `CreationTime` UTC szerint van `hh_mm_ss_ms` formátumban, és `FileNumberInSession` futó index abban az esetben munkamenet átnyúlások naplók több Blob a fájlok között.

Például az adatbázis `Database1` a `Server1` az alábbiakban a lehetséges érvényes elérési utat:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Eseményközpont

Naplózási események kerüljenek a névtér és az eseményközpont naplózási konfiguráció során lett definiálva, és törzsét rögzíti a rendszer [Apache Avro](http://avro.apache.org/) események segítségével a JSON formázását az UTF-8 kódolást és tárolhatók. A vizsgálati naplók elolvasásához használja [Avro eszközök](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) vagy hasonló eszközt, amely ezt a formátumot.

### <a name="log-analytics"></a>Log Analytics

Naplózás konfigurálása során meghatározott a Log Analytics-munkaterület írt események naplózása a `AzureDiagnostics` tábla kategóriával `SQLSecurityAuditEvents`. További hasznos információkat a Log Analytics keresési nyelv és a parancsok, lásd: [Log Analytics keresési referenciáját bemutató](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Audit napló mezők

| Name (Blob) | Név (Event Hubs és a Log Analytics) | Leírás | Blob Type | Event Hubs/Log Analytics típusa |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | A művelet azonosítója | varchar(4) | sztring |
| action_name | action_name_s | Művelet neve | – | sztring |
| additional_information | additional_information_s | További információt az esemény tárolt XML-fájlként | nvarchar(4000) | sztring |
| affected_rows | affected_rows_d | A lekérdezés által érintett sorok száma | bigint | int |
| application_name | application_name_s| Az ügyfél-alkalmazás neve | nvarchar(128) | sztring |
| audit_schema_version | audit_schema_version_d | Mindig 1 | int | int |
| class_type | class_type_s | A naplózási következik be naplózható entitás típusa | varchar(2) | sztring |
| class_type_desc | class_type_description_s | Naplózható entitás, amely a naplózási előfordulás leírása | – | sztring |
| client_ip | client_ip_s | Forrás IP-cím, az ügyfélalkalmazás | nvarchar(128) | sztring |
| connection_id | – | A kapcsolat a kiszolgáló azonosítója | GUID | – |
| data_sensitivity_information | data_sensitivity_information_s | Adattípusok és az adatbázis osztályozott oszlopok alapján, a naplózott lekérdezés által visszaadott bizalmassági címke. Tudjon meg többet [Azure SQL Database adatok felderítése és besorolása](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | sztring |
| database_name | database_name_s | Az adatbázis-környezet, amelyben a művelet történt. | sysname | sztring |
| database_principal_id | database_principal_id_d | Az adatbázis, amely a művelet végrehajtását a felhasználói környezet azonosítója | int | int |
| database_principal_name | database_principal_name_s | Az adatbázis-felhasználói környezetet, amelyben a művelet végrehajtását neve | sysname | sztring |
| duration_milliseconds | duration_milliseconds_d | Lekérdezés-végrehajtási idő ezredmásodpercben | bigint | int |
| event_time | event_time_t | Dátum és idő, amikor a naplózható művelet aktiválódik. | datetime2 | dátum/idő |
| host_name | – | Ügyfél-állomásnév | sztring | – |
| is_column_permission | is_column_permission_s | Ez a jelző azt jelzi, ha egy oszlop szintű engedéllyel. 1 = igaz, 0 = false | bit | sztring |
| – | is_server_level_audit_s | Jelzi, hogy a kiszolgáló szintjén van ehhez az ellenőrzéshez | – | sztring |
| object_ azonosítója | object_id_d | A naplózási lépett fel az entitás azonosítója. Ez magában foglalja a: kiszolgáló, adatbázisok, adatbázis-objektumok és séma objektumok. Ha az entitás magát a kiszolgálót, vagy ha az ellenőrzés nem az objektumok szintjén végzett 0 | int | int |
| object_name | object_name_s | A naplózási lépett fel az entitás neve. Ez magában foglalja a: kiszolgáló, adatbázisok, adatbázis-objektumok és séma objektumok. Ha az entitás magát a kiszolgálót, vagy ha az ellenőrzés nem az objektumok szintjén végzett 0 | sysname | sztring |
| permission_bitmask | permission_bitmask_s | Ha alkalmazható, jeleníti meg az engedélyeket, megtagadva, vagy visszavonták | varbinary(16) | sztring |
| response_rows | response_rows_d | Az eredményhalmaz visszaadott sorok száma | bigint | int |
| schema_name | schema_name_s | A séma környezet, amelyben a művelet történt. A séma kívül bekövetkező eseményeket NULL | sysname | sztring |
| – | securable_class_type_s | Biztonságos, a naplózott class_type leképező objektum | – | sztring |
| sequence_group_id | sequence_group_id_g | Egyedi azonosító | varbinary | GUID |
| sequence_number | sequence_number_d | Nyomon követi a rekordok, amelyek túl nagy volt ahhoz, hogy az írási puffert naplózások elférjen egyetlen naplórekordot belül sorrendje | int | int |
| server_instance_name | server_instance_name_s | Ahol a naplózási történt server-példány neve | sysname | sztring |
| server_principal_id | server_principal_id_d | A bejelentkezési környezet, amelyben a művelet végrehajtását azonosítója | int | int |
| server_principal_name | server_principal_name_s | Az aktuális bejelentkezési | sysname | sztring |
| server_principal_sid | server_principal_sid_s | Az aktuális bejelentkezési biztonsági azonosítója | varbinary | sztring |
| session_id | session_id_d | Az esemény történt, amelyen a munkamenet azonosítója | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Munkamenethez tartozó kiszolgálói tag | sysname | sztring |
| Utasítás | statement_s | T-SQL-utasítás (ha vannak) végrehajtott | nvarchar(4000) | sztring |
| Sikeres volt | succeeded_s | Azt jelzi, hogy sikerült-e az eseményt előidéző műveletet végrehajtó. Események, bejelentkezési és a batch kivételével ez csak a jelentés-e a engedélyeinek ellenőrzése sikeres, vagy nem, a művelet nem sikerült. 1 = sikeres, 0 = sikertelen | bit | sztring |
| target_database_principal_id | target_database_principal_id_d | Az adatbázis rendszerbiztonsági tagjához a GRANT/DENY/REVOKE művelet történik. 0, ha nem alkalmazható | int | int |
| target_database_principal_name | target_database_principal_name_s | Célfelhasználó művelet. NULL értékű, ha nem alkalmazható | sztring | sztring |
| target_server_principal_id | target_server_principal_id_d | Kiszolgálói tag a GRANT/DENY/REVOKE műveletet végrehajtó. 0 értéket adja vissza, ha nem alkalmazható | int | int |
| target_server_principal_name | target_server_principal_name_s | Cél bejelentkezési művelet. NULL értékű, ha nem alkalmazható | sysname | sztring |
| target_server_principal_sid | target_server_principal_sid_s | Cél bejelentkezési biztonsági azonosítója. NULL értékű, ha nem alkalmazható | varbinary | sztring |
| transaction_id | transaction_id_d | Csak az SQL Server (2016-tól induló) – az Azure SQL DB 0 | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Felhasználó által definiált sp_audit_write argumentumként átadott eseményazonosító. NULL értékű, a rendszer események (alapértelmezett) és a felhasználó által meghatározott esemény nem nulla. További információkért lásd: [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Felhasználó által definiált sp_audit_write argumentumként átadott adatokat. NULL értékű, a rendszer események (alapértelmezett) és a felhasználó által meghatározott esemény nem nulla. További információkért lásd: [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | sztring |

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure SQL Database naplózási funkciójának](sql-database-auditing.md).