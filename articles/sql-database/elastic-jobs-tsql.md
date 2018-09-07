---
title: Feladatok létrehozása és kezelése az Azure SQL rugalmas adatbázis Transact-SQL (T-SQL) használatával |} A Microsoft Docs
description: Több adatbázis közötti parancsfájlok futtatása az adatbázis feladatügynök Transact-SQL (T-SQL) használatával.
services: sql-database
author: jaredmoo
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: jaredmoo
ms.openlocfilehash: d524f7756c8dd394d8651839a1f6426c512d6a73
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023528"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Rugalmas adatbázis-feladatok létrehozása és kezelése Transact-SQL (T-SQL) használatával

A cikk ismerteti az Elastic Jobs használatának megkezdéséhez számos példaforgatókönyvek T-SQL használatával.

A példákban a [tárolt eljárások](#job-stored-procedures) és [nézetek](#job-views) érhető el a [ *feladat adatbázis*](elastic-jobs-overview.md#job-database).

A Transact-SQL (T-SQL) létrehozása, konfigurálása, hajtsa végre és feladatok kezelésére szolgál. A feladatügynök létrehozása nem támogatott a T-SQL, ezért először létre kell hoznia egy *feladatügynök* a portálról, vagy [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Feladat-végrehajtással kapcsolatos hitelesítő adatok létrehozása

A hitelesítő adatokat a parancsprogramok futtatásához a cél adatbázisaihoz való kapcsolódásra szolgál. A hitelesítő adatokat kell megfelelő engedélyeket, az adatbázisokat a célcsoport által megadott sikerült végrehajtani a parancsprogramot. A kiszolgálón és/vagy a célként megadott csoportot készlettag használatakor, magas ajánlott fő hitelesítő adatok való frissítése előtt bővítése a kiszolgálón és/vagy a készlet feladat-végrehajtás időpontjában a hitelesítő adat létrehozása. Az adatbázishoz kötődő hitelesítő adatok a feladat ügynök adatbázisban jön létre. Ugyanazokat a hitelesítő adatokat kell lennie a használt *hozzon létre egy bejelentkezést* és *hozzon létre egy felhasználót a bejelentkezési adatbázis-engedélyek megadására bejelentkezési identitás* a cél adatbázisokon.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Hozzon létre egy célcsoportot (kiszolgálók)

Az alábbi példa bemutatja, hogyan szemben az összes adatbázist feladat végrehajtása a kiszolgálón.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-a-single-database"></a>Önálló adatbázis kizárása

Az alábbi példa bemutatja, hogyan hajtsa végre a feladat összes adatbázisokhoz a kiszolgáló, kivéve az adatbázis nevesített *MappingDB*.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Excude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Hozzon létre egy célcsoportot (készletek)

Az alábbi példa bemutatja, hogyan, amelyekre legalább egy rugalmas készletekben található összes adatbázishoz.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Új sémára nagyszámú adatbázis üzembe helyezése

Az alábbi példa bemutatja, hogyan új sémákat telepíteni az összes adatbázishoz.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Adatgyűjtés beépített paraméterek használatával

Sok adat gyűjtemény esetekben hasznos lehet bizonyos ezeket a parancsfájl-kezelési változókat a feldolgozás eredményét az utófeldolgozási segítségével.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Ha például minden eredmény együtt ugyanazon feladat végrehajtása a csoporthoz, használja a *$(job_execution_id)* , ahogyan az alábbi parancsot:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Adatbázis teljesítményének monitorozása

A következő példában létrehozunk egy új feladatot, amely több adatbázisból származó teljesítményadatokat gyűjtenek.

Alapértelmezés szerint a feladat ügynök fognak kinézni, a visszaadott eredményeket tárolja a tábla létrehozásához. Ennek eredményeképpen a bejelentkezés, a kimeneti hitelesítő adat számára használt hitelesítő adatokat társított kell végezni a megfelelő engedélyekkel. Ha szeretné manuálisan létrehozni a tábla kelljen majd annak rendelkeznie kell a következő tulajdonságokkal:
1. A megfelelő nevű és típusú adatokkal az eredményhalmaz oszlopok.
2. További internal_execution_id UniqueIdentifier adattípusú oszlop.
3. Egy nem fürtözött index nevű "amelynek neve IX_<TableName>_Internal_Execution_ID" internal_execution_id oszlopában.

Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsokat:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name=’myjobcred’,
@output_server_name=’server1.database.windows.net',
@output_database_name=’<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name=’server1.database.windows.net',
@output_database_name=’resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Feladatdefiníciók megtekintése

Az alábbi példa bemutatja, hogyan aktuális feladatdefiníciók megtekintéséhez.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>A feladat alkalmi végrehajtásának megkezdése

Az alábbi példa bemutatja, hogyan azonnal elindul egy feladat.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Egy feladat végrehajtásának ütemezése

Az alábbi példa bemutatja, hogyan egy jövőbeli végrehajtási feladat ütemezése.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Feladat-végrehajtási állapotának figyelése

Az alábbi példa bemutatja az összes feladat-végrehajtási állapot részleteit megtekintheti.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named ‘ResultsPoolJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named ‘ResultsPoolsJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Feladatok megszakítása

Az alábbi példa bemutatja, hogyan megszakítani a feladatot.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Régi feladatelőzmények törlése

Az alábbi példa bemutatja, hogyan adott dátum előtt feladatelőzmények törlése.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Egy feladat és a feladatelőzmények törlése

Az alábbi példa bemutatja, hogyan törölhet egy feladatot, és az összes kapcsolódó feladatelőzményeket.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Feladat tárolt eljárások

A következő tárolt eljárások a [feladatok adatbázis](elastic-jobs-overview.md#job-database).



|Tárolt eljárás  |Leírás  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     Hozzáad egy új feladatot.    |
|[sp_update_job ](#spupdatejob)    |      Frissíti egy meglévő feladat.   |
|[sp_delete_job](#spdeletejob)     |      Egy meglévő feladat törlése.   |
|[sp_add_jobstep](#spaddjobstep)    |    Egy lépést ad egy feladathoz.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Egy feladat lépésének frissíti.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Egy feladat lépésének törlése.    |
|[sp_start_job](#spstartjob)    |  Elindul egy feladat végrehajtása.       |
|[sp_stop_job](#spstopjob)     |     Egy feladat végrehajtása leáll.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Cél csoport hozzáadása.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    A célként megadott csoport törlése.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Egy adatbázis vagy adatbázisokat ad hozzá egy célcsoportot.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     A cél a csoporttag eltávolítása egy célcsoportot.    |
|[sp_purge_jobhistory ](#sppurgejobhistory)    |    Eltávolít egy feladat replikálásielőzmény-rekord.     |





### <a name="spaddjob"></a>sp_add_job

Hozzáad egy új feladatot. 
  
#### <a name="syntax"></a>Szintaxis  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Argumentumok  

[  **@job_name =** ] "job_name"  
A feladat neve. A névnek egyedinek kell lennie, és nem tartalmazhatja a százalék (%) karaktert. job_name nvarchar(128), nem alapértelmezett.

[  **@description =** ] "leírás"  
A feladat leírása. Leírás megadása nem nvarchar(512), az alapértelmezett érték NULL. Ha a leírás, a rendszer üres karakterláncot használja.

[  **@enabled =** ] engedélyezve  
A feladat ütemezés engedélyezve van-e. Engedélyezett bit, az alapértelmezett érték a 0 (letiltva). Ha 0, a feladat nem engedélyezett, és nem fut az ütemezésnek; azonban hogy manuálisan futtatható. Ha 1, a feladat az ütemezése szerint fog futni, és manuálisan is futtathatók.

[  **@schedule_interval_type =**] schedule_interval_type  
Érték azt jelzi, ha a feladat kell végrehajtani. schedule_interval_type nvarchar(50), az egyszer, alapértelmezett érték, és a következő értékek egyike lehet:
- Az "egyszeri"
- "Minutes",
- "Hours",
- "Days",
- "Hét"
- "Hónapok"

[  **@schedule_interval_count =** ] schedule_interval_count  
Minden egyes végrehajtása a feladat a közti schedule_interval_count időszakok száma. schedule_interval_count int, az alapértelmezett 1 érték. Az érték nagyobb, mint 1 vagy azzal egyenlőnek kell lennie.

[  **@schedule_start_time =** ] schedule_start_time  
A dátum mely feladat végrehajtásának kezdete is. schedule_start_time DATETIME2, a rendszer az alapértelmezett 0001-01-01 00:00:00.0000000.

[  **@schedule_end_time =** ] schedule_end_time  
A dátum mely a feladat a végrehajtás leállíthatja. schedule_end_time DATETIME2, akkor az alapértelmezett 9999-12-31 11:59:59.0000000. 

[  **@job_id =** ] job_id KIMENET  
Ha sikeresen létrehozva a feladathoz hozzárendelt feladathoz azonosítószáma. job_id egy UniqueIdentifier típusú kimeneti változó.

#### <a name="return-code-values"></a>Visszatérési kód értéket

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
a feladat ügynök adatbázisból a feladatügynök létrehozásakor megadott sp_add_job kell futtatni.
Miután sp_add_job végre lett hajtva a feladat hozzáadása, sp_add_jobstep vegyen fel olyan lépéseket a feladat tevékenységeit végző használható. A feladat kezdeti verziószáma 0, amely az első lépés hozzáadásakor 1-re növekszik.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:

- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.

### <a name="spupdatejob"></a>sp_update_job

Frissíti egy meglévő feladat.

#### <a name="syntax"></a>Szintaxis

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
Frissíteni kell a feladat neve. job_name nvarchar(128).

[  **@new_name =** ] "új_név"  
A feladat új neve. új_név nvarchar(128).

[  **@description =** ] "leírás"  
A feladat leírása. Leírás megadása nem nvarchar(512).

[  **@enabled =** ] engedélyezve  
Megadja, hogy a feladatütemezés engedélyezve (1) vagy nem engedélyezett (0). Engedélyezve van-e bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Érték azt jelzi, ha a feladat kell végrehajtani. schedule_interval_type nvarchar(50), és a következő értékek egyike lehet:

- Az "egyszeri"
- "Minutes",
- "Hours",
- "Days",
- "Hét"
- "Hónapok"

[  **@schedule_interval_count=** ] schedule_interval_count  
Minden egyes végrehajtása a feladat a közti schedule_interval_count időszakok száma. schedule_interval_count int, az alapértelmezett 1 érték. Az érték nagyobb, mint 1 vagy azzal egyenlőnek kell lennie.

[  **@schedule_start_time=** ] schedule_start_time  
A dátum mely feladat végrehajtásának kezdete is. schedule_start_time DATETIME2, a rendszer az alapértelmezett 0001-01-01 00:00:00.0000000.

[  **@schedule_end_time=** ] schedule_end_time  
A dátum mely a feladat a végrehajtás leállíthatja. schedule_end_time DATETIME2, akkor az alapértelmezett 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Miután sp_add_job végre lett hajtva a feladat hozzáadása, sp_add_jobstep vegyen fel olyan lépéseket a feladat tevékenységeit végző használható. A feladat kezdeti verziószáma 0, amely az első lépés hozzáadásakor 1-re növekszik.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.



### <a name="spdeletejob"></a>sp_delete_job

Egy meglévő feladat törlése.

#### <a name="syntax"></a>Szintaxis

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A törlendő feladat neve. job_name nvarchar(128).

[  **@force =** ] kényszerítése  
Megadja, hogy törölnie kell-e a feladat minden végrehajtás folyamatban van, és megszakítja az összes folyamatban lévő végrehajtások (1) vagy sikertelen, ha bármely feladatvégrehajtások folyamatban (0). kényszerített bit.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Feladatelőzmények automatikusan törlődik a feladat törlése.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.



### <a name="spaddjobstep"></a>sp_add_jobstep

Egy lépést ad egy feladathoz.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentumok

[  **@job_name =** ] "job_name"  
A feladat, amelyhez a lépés hozzáadása neve. job_name nvarchar(128).

[  **@step_id =** ] step_id  
A feladat lépésének feladatütemezési azonosítószáma. Lépés azonosítószámokat számozása 1, és növelje hézagok nélkül. Ha egy meglévő lépés már ezt az azonosítót, majd lépés és lépéseket azonosítójának fog kell a növekszik, úgy, hogy ez a lépés szúrhatók be, a feladatütemezés. Ha nincs megadva, a step_id lesz automatikusan hozzárendelve az utolsó a lépések sorrendjét. step_id egy: egész szám.

[  **@step_name =** ] step_name  
A lépés neve. Meg kell adni, kivéve az első lépés egy feladatot, amely (az egyszerűség) alapértelmezett neve "JobStep". step_name nvarchar(128).

[  **@command_type =** ] "command_type"  
Ez jobstep által végrehajtott parancs típusa. command_type nvarchar(50) a TSQL használatával, ami azt jelenti, hogy értékét, az alapértelmezett érték a @command_type paramétere T-SQL parancsfájl.

Ha meg van adva, az érték a TSQL használatával kell lennie.

[  **@command_source =** ] "command_source"  
A parancs tárolásának helyét típusa. command_source nvarchar(50) soron belüli, ami azt jelenti, hogy értékét az alapértelmezett értékkel a @command_source paraméter a parancs szövegkonstans.

Ha meg van adva, az értéknek a beágyazott kell lennie.

[  **@command =** ] 'parancs'  
A parancsot érvényes T-SQL parancsfájl kell lennie, és majd a feladat lépésének által végrehajtott. a parancs az nvarchar(max), az alapértelmezett érték NULL esetén.

[  **@credential_name =** ] "credential_name"  
Az adatbázis neve kötődő hitelesítőadat-adatbázisban tárolt e feladat vezérlőelem, amellyel csatlakozzon az egyes a céladatbázisok belül a célcsoportot, ez a lépés végrehajtásakor. credential_name nvarchar(128).

[  **@target_group_name =** ] "cél-csoportnév"  
A cél, hogy a feladat lépésének végrehajtásához adatbázisokat tartalmazó célcsoport neve. target_group_name nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
A késleltetés, mielőtt az első újrapróbálkozás kísérli meg, ha a feladat lépés sikertelen lesz, a kezdeti végrehajtási próbálkozásra. initial_retry_interval_seconds int, alapértelmezett értéke 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Az újrapróbálkozási kísérletek közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb, mint ez az érték akkor növekszik, azt a maximumon erre az értékre helyette. maximum_retry_interval_seconds int, az alapértelmezett érték 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
A alkalmazni újrapróbálkozási késleltetéshez, ha több feladat végrehajtási lépés szorzó kísérletek sikertelenek. Például ha az első újrapróbálkozás kellett 5 másodperces késleltetést, és a leállítási szorzó 2.0, majd a második újra lesz 10 másodperc késéssel és a harmadik újrapróbálkozási rendelkeznek majd 20 másodperc késéssel. retry_interval_backoff_multiplier valódi, 2.0 alapértelmezett értékkel.

[  **@retry_attempts =** ] retry_attempts  
Újrapróbálja a futtatást, ha a kezdeti kísérlet sikertelen bejelentkezések száma. Például ha retry_attempts értéke 10., akkor nem lesznek 1 kezdeti kísérlet és 10 újrapróbálkozások száma, így 11 kísérletek összesen. Ha az utolsó újrapróbálkozási kísérlet sikertelen, majd a feladat végrehajtása le fog állni egy életciklusával sikertelen. retry_attempts int, az alapértelmezett 10 értéket.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
A lépés végrehajtásához engedélyezett maximális mennyisége. Ez idő túllépése, majd a feladat végrehajtása időtúllépés miatt megszakadt egy életciklusával akkor megszűnik. step_timeout_seconds int, az alapértelmezett érték pedig 43 200 másodperc (12 óra).

[  **@output_type =** ] "output_type"  
Ha nem null értékű, a cél, amely a parancs első eredményt típusát íródik. output_type nvarchar(50), az alapértelmezett érték NULL.

Ha meg van adva, az értéknek az SQL Database kell lennie.

[  **@output_credential_name =** ] "output_credential_name"  
Nem null értékű, ha az adatbázis neve hatóköre a kimeneti cél adatbázishoz való kapcsolódáshoz használt hitelesítő adatok. Kötelező megadni, ha output_type megegyezik-e az SQL Database. output_credential_name nvarchar(128), NULL alapértelmezett értéket.

[  **@output_subscription_id =** ] "output_subscription_id"  
Leírás szükséges.

[  **@output_resource_group_name =** ] "output_resource_group_name"  
Leírás szükséges.

[  **@output_server_name =** ] "output_server_name"  
Ha nem null, a kimeneti adatbázist tartalmazó kiszolgáló teljesen minősített DNS-nevét. Kötelező megadni, ha output_type megegyezik-e az SQL Database. output_server_name nvarchar(256), az alapértelmezett érték NULL.

[  **@output_database_name =** ] "output_database_name"  
Ha nem null értékű, az adatbázis nevét, amely tartalmazza a kimeneti céltáblázatban. Kötelező megadni, ha output_type megegyezik-e az SQL Database. output_database_name nvarchar(128), az alapértelmezett érték NULL.

[  **@output_schema_name =** ] "output_schema_name"  
Ha nem null értékű, az SQL-séma neve, amely tartalmazza a kimeneti céltáblázatban. Ha output_type megegyezik az SQL Database, az alapértelmezett érték a dbo. output_schema_name nvarchar(128).

[  **@output_table_name =** ] "output_table_name"  
Ha nem null értékű, a tábla, amely a parancs első eredményt neve lesz írva. Ha a tábla már nem létezik, jön az adatszolgáltató eredményhalmaz a séma alapján. Kötelező megadni, ha output_type megegyezik-e az SQL Database. output_table_name nvarchar(128), NULL alapértelmezett értéket.

[  **@job_version =** ] job_version KIMENET  
A kimeneti paraméter, amely az új feladat verziószámmal hozzá lesz rendelve. job_version: egész szám.

[  **@max_parallelism =** ] max_parallelism KIMENET  
A rugalmas készletenként párhuzamosság maximális szintjét. Ha be van állítva, a feladat lépésének csak futtatni, amely legfeljebb annyi adatbázist egy rugalmas készlet korlátozott lesz. Ez minden rugalmas készletet, amely közvetlenül vagy tartalmazza a célcsoportban, vagy olyan kiszolgálóra, amely tartalmazza a célcsoportban lévő vonatkozik. max_parallelism: egész szám.


#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Ha a sp_add_jobstep sikeres, a feladat jelenlegi verziószám értéke akkor növekszik. A következő alkalommal, amikor a feladat végrehajtása, az új verziót fogja használni. Ha a feladat végrehajtása folyamatban van, a végrehajtás nem tartalmazza majd az új lépés.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:  

- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.



### <a name="spupdatejobstep"></a>sp_update_jobstep

Egy feladat lépésének frissíti.

#### <a name="syntax"></a>Szintaxis

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A feladat, amelyhez tartozik. a lépés neve. job_name nvarchar(128).

[  **@step_id =** ] step_id  
A feladat lépésének módosítani azonosítószáma. Step_id vagy step_name meg kell adni. step_id egy: egész szám.

[  **@step_name =** ] "step_name"  
Módosítani kell a lépés neve. Step_id vagy step_name meg kell adni. step_name nvarchar(128).

[  **@new_id =** ] new_id  
A feladat lépésének új feladatütemezési azonosítószáma. Lépés azonosítószámokat számozása 1, és növelje hézagok nélkül. Ha egy lépés átrendezésekor van, majd további lépések fog lehet automatikusan újraszámozásakor.

[  **@new_name =** ] "új_név"  
A lépés új neve. új_név nvarchar(128).

[  **@command_type =** ] "command_type"  
Ez jobstep által végrehajtott parancs típusa. command_type nvarchar(50) a TSQL használatával, ami azt jelenti, hogy értékét, az alapértelmezett érték a @command_type paramétere T-SQL parancsfájl.

Ha meg van adva, az érték a TSQL használatával kell lennie.

[  **@command_source =** ] "command_source"  
A parancs tárolásának helyét típusa. command_source nvarchar(50) soron belüli, ami azt jelenti, hogy értékét az alapértelmezett értékkel a @command_source paraméter a parancs szövegkonstans.

Ha meg van adva, az értéknek a beágyazott kell lennie.

[  **@command =** ] 'parancs'  
A parancsok a következők érvényes T-SQL parancsfájl kell lennie, és majd a feladat lépésének által végrehajtott. a parancs az nvarchar(max), az alapértelmezett érték NULL esetén.

[  **@credential_name =** ] "credential_name"  
Az adatbázis neve kötődő hitelesítőadat-adatbázisban tárolt e feladat vezérlőelem, amellyel csatlakozzon az egyes a céladatbázisok belül a célcsoportot, ez a lépés végrehajtásakor. credential_name nvarchar(128).

[  **@target_group_name =** ] "cél-csoportnév"  
A cél, hogy a feladat lépésének végrehajtásához adatbázisokat tartalmazó célcsoport neve. target_group_name nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
A késleltetés, mielőtt az első újrapróbálkozás kísérli meg, ha a feladat lépés sikertelen lesz, a kezdeti végrehajtási próbálkozásra. initial_retry_interval_seconds int, alapértelmezett értéke 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Az újrapróbálkozási kísérletek közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb, mint ez az érték akkor növekszik, azt a maximumon erre az értékre helyette. maximum_retry_interval_seconds int, az alapértelmezett érték 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
A alkalmazni újrapróbálkozási késleltetéshez, ha több feladat végrehajtási lépés szorzó kísérletek sikertelenek. Például ha az első újrapróbálkozás kellett 5 másodperces késleltetést, és a leállítási szorzó 2.0, majd a második újra lesz 10 másodperc késéssel és a harmadik újrapróbálkozási rendelkeznek majd 20 másodperc késéssel. retry_interval_backoff_multiplier valódi, 2.0 alapértelmezett értékkel.

[  **@retry_attempts =** ] retry_attempts  
Újrapróbálja a futtatást, ha a kezdeti kísérlet sikertelen bejelentkezések száma. Például ha retry_attempts értéke 10., akkor nem lesznek 1 kezdeti kísérlet és 10 újrapróbálkozások száma, így 11 kísérletek összesen. Ha az utolsó újrapróbálkozási kísérlet sikertelen, majd a feladat végrehajtása le fog állni egy életciklusával sikertelen. retry_attempts int, az alapértelmezett 10 értéket.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
A lépés végrehajtásához engedélyezett maximális mennyisége. Ez idő túllépése, majd a feladat végrehajtása időtúllépés miatt megszakadt egy életciklusával akkor megszűnik. step_timeout_seconds int, az alapértelmezett érték pedig 43 200 másodperc (12 óra).

[  **@output_type =** ] "output_type"  
Ha nem null értékű, a cél, amely a parancs első eredményt típusát íródik. Visszaállítani output_type értéke NULL, ez a paraméter értékét állítsa "(üres karakterlánc). output_type nvarchar(50), az alapértelmezett érték NULL.

Ha meg van adva, az értéknek az SQL Database kell lennie.

[  **@output_credential_name =** ] "output_credential_name"  
Nem null értékű, ha az adatbázis neve hatóköre a kimeneti cél adatbázishoz való kapcsolódáshoz használt hitelesítő adatok. Kötelező megadni, ha output_type megegyezik-e az SQL Database. Visszaállítani output_credential_name értéke NULL, ez a paraméter értékét állítsa "(üres karakterlánc). output_credential_name nvarchar(128), NULL alapértelmezett értéket.

[  **@output_server_name =** ] "output_server_name"  
Ha nem null, a kimeneti adatbázist tartalmazó kiszolgáló teljesen minősített DNS-nevét. Kötelező megadni, ha output_type megegyezik-e az SQL Database. Visszaállítani output_server_name értéke NULL, ez a paraméter értékét állítsa "(üres karakterlánc). output_server_name nvarchar(256), az alapértelmezett érték NULL.

[  **@output_database_name =** ] "output_database_name"  
Ha nem null értékű, az adatbázis nevét, amely tartalmazza a kimeneti céltáblázatban. Kötelező megadni, ha output_type megegyezik-e az SQL Database. Visszaállítani output_database_name értéke NULL, ez a paraméter értékét állítsa "(üres karakterlánc). output_database_name nvarchar(128), az alapértelmezett érték NULL.

[  **@output_schema_name =** ] "output_schema_name"  
Ha nem null értékű, az SQL-séma neve, amely tartalmazza a kimeneti céltáblázatban. Ha output_type megegyezik az SQL Database, az alapértelmezett érték a dbo. Visszaállítani output_schema_name értéke NULL, ez a paraméter értékét állítsa "(üres karakterlánc). output_schema_name nvarchar(128).

[  **@output_table_name =** ] "output_table_name"  
Ha nem null értékű, a tábla, amely a parancs első eredményt neve lesz írva. Ha a tábla már nem létezik, jön az adatszolgáltató eredményhalmaz a séma alapján. Kötelező megadni, ha output_type megegyezik-e az SQL Database. Visszaállítani output_server_name értéke NULL, ez a paraméter értékét állítsa "(üres karakterlánc). output_table_name nvarchar(128), NULL alapértelmezett értéket.

[  **@job_version =** ] job_version KIMENET  
A kimeneti paraméter, amely az új feladat verziószámmal hozzá lesz rendelve. job_version: egész szám.

[  **@max_parallelism =** ] max_parallelism KIMENET  
A rugalmas készletenként párhuzamosság maximális szintjét. Ha be van állítva, a feladat lépésének csak futtatni, amely legfeljebb annyi adatbázist egy rugalmas készlet korlátozott lesz. Ez minden rugalmas készletet, amely közvetlenül vagy tartalmazza a célcsoportban, vagy olyan kiszolgálóra, amely tartalmazza a célcsoportban lévő vonatkozik. Visszaállítani max_parallelism értéke null, állítsa be a paraméter értéke 1. max_parallelism: egész szám.


#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Minden folyamatban lévő végrehajtások a feladat nem érinti. Ha a sp_update_jobstep sikeres, a feladat verziószáma értéke akkor növekszik. A következő alkalommal, amikor a feladat végrehajtása, az új verziót fogja használni.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:

- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Egy feladat lépésének távolít el egy feladatot.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A feladat, amelyről a lépés eltávolítja a neve. job_name nvarchar(128), nem alapértelmezett.

[  **@step_id =** ] step_id  
Azonosítószáma. a feladat lépésének törölni. Step_id vagy step_name meg kell adni. step_id egy: egész szám.

[  **@step_name =** ] "step_name"  
A törlendő lépés neve. Step_id vagy step_name meg kell adni. step_name nvarchar(128).

[  **@job_version =** ] job_version KIMENET  
A kimeneti paraméter, amely az új feladat verziószámmal hozzá lesz rendelve. job_version: egész szám.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Minden folyamatban lévő végrehajtások a feladat nem érinti. Ha a sp_update_jobstep sikeres, a feladat verziószáma értéke akkor növekszik. A következő alkalommal, amikor a feladat végrehajtása, az új verziót fogja használni.

A többi feladat lépés automatikusan újraszámozásakor fogja a törölt feladatról lépés az űrt tölti ki.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.






### <a name="spstartjob"></a>sp_start_job

Elindul egy feladat végrehajtása.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A feladat, amelyről a lépés eltávolítja a neve. job_name nvarchar(128), nem alapértelmezett.

[  **@job_execution_id =** ] job_execution_id KIMENET  
A kimeneti paraméter, amely a feladat-végrehajtási azonosítóhoz hozzá lesz rendelve. job_version uniqueidentifier.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Nincs.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.

### <a name="spstopjob"></a>sp_stop_job

Egy feladat végrehajtása leáll.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumentumok
[  **@job_execution_id =** ] job_execution_id  
A feladat végrehajtásának leállítása azonosítószáma. job_execution_id uniqueidentifier alapértelmezett null értékű.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Nincs.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Cél csoport hozzáadása.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumentumok
[  **@target_group_name =** ] "target_group_name"  
Hozhat létre a célcsoport neve. target_group_name nvarchar(128), nem alapértelmezett.

[  **@target_group_id =** ] target_group_id kimeneti a célként megadott csoport azonosító száma pedig a feladathoz hozzárendelt, ha sikeresen létrehozva. target_group_id egy kimeneti változó UniqueIdentifier típusú, az alapértelmezett érték NULL.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Célcsoportok, amelyekre egy feladatot egy adatbázis-gyűjtemény, egyszerű módot biztosítanak.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

A célként megadott csoport törlése.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumentumok
[  **@target_group_name =** ] "target_group_name"  
Törli a célcsoport neve. target_group_name nvarchar(128), nem alapértelmezett.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Nincs.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Egy adatbázis vagy adatbázisokat ad hozzá egy célcsoportot.

#### <a name="syntax"></a>Szintaxis

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] ‘membership_type’ ]   
        [ , [ @target_type = ] ‘target_type’ ]   
        [ , [ @refresh_credential_name = ] ‘refresh_credential_name’ ]   
        [ , [ @server_name = ] ‘server_name’ ]   
        [ , [ @database_name = ] ‘database_name’ ]   
        [ , [ @elastic_pool_name = ] ‘elastic_pool_name’ ]   
        [ , [ @shard_map_name = ] ‘shard_map_name’ ]   
        [ , [ @target_id = ] ‘target_id’ OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok
[  **@target_group_name =** ] "target_group_name"  
A célcsoportot, amely hozzáadja a tag neve. target_group_name nvarchar(128), nem alapértelmezett.

[  **@membership_type =** ] "membership_type"  
Ha a célként megadott csoport tagja foglalt vagy kizárt adja meg. target_group_name nvarchar(128) "Include" alapértelmezés szerint a rendszer. Target_group_name érvényes értékei a következők: "Include" vagy "Kizárása".

[  **@target_type =** ] "target_type"  
A céladatbázis vagy -beleértve az összes adatbázis-kiszolgálók, rugalmas készletben található összes adatbázis, horizontálispartíció-térkép található összes adatbázis vagy az egyes adatbázisok adatbázis-gyűjtemény típusa. target_type nvarchar(128), nem alapértelmezett. Target_type érvényes értékei a következők: "SqlServer", "SqlElasticPool", "SqlDatabase" vagy "SqlShardMap". 

[  **@refresh_credential_name =** ] "refresh_credential_name"  
A logikai kiszolgáló neve. refresh_credential_name nvarchar(128), nem alapértelmezett.

[  **@server_name =** ] ": kiszolgálónév"  
A megadott célcsoportot, az új logikai kiszolgáló neve. kiszolgáló_neve target_type "SqlServer" esetén adható meg. kiszolgáló_neve nvarchar(128), nem alapértelmezett.

[  **@database_name =** ] "database_name"  
A megadott célcsoportot, az új adatbázis neve. Ha target_type "SqlDatabase" database_name adható meg. adatbázisnév nvarchar(128), nem alapértelmezett.

[  **@elastic_pool_name =** ] "elastic_pool_name"  
A rugalmas készlet a megadott célcsoportot, az új neve. Ha target_type "SqlElasticPool" elastic_pool_name adható meg. elastic_pool_name nvarchar(128), nem alapértelmezett.

[  **@shard_map_name =** ] "shard_map_name"  
A szegmens térkép készlet a megadott célcsoportot, az új neve. Ha target_type "SqlSqlShardMap" elastic_pool_name adható meg. shard_map_name nvarchar(128), nem alapértelmezett.

[  **@target_id =** ] target_group_id KIMENET  
A célcsoport hozzá rendelt a célként megadott csoport tagja, ha létrehozott cél azonosítószáma. target_id egy kimeneti változó UniqueIdentifier típusú, az alapértelmezett érték NULL.
Visszatérési kód értékek 0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Egy feladatot hajt végre egy kiszolgálón található összes adatbázis, vagy a célcsoportban végrehajtása, ha egy logikai kiszolgálón vagy a rugalmas készlet időpontjában rugalmas készlet része.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.

#### <a name="examples"></a>Példák
Az alábbi példa adatbázisaihoz hozzáadja a csoporthoz kiszolgálók karbantartása ügyfél-információkat a londoni és NewYork kiszolgálókon. A feladatok adatbázis létrehozásakor megadott a feladatügynök a jelen esetben ElasticJobs kell kapcsolódnia.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="spdeletetargetgroupmember"></a>sp_delete_target_group_member

A cél a csoporttag eltávolítása egy célcsoportot.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] ‘target_id’]
```



Argumentumok [ @target_group_name =] "target_group_name"  
Távolítsa el a célként megadott csoport tagja, amelyből a célcsoport neve. target_group_name nvarchar(128), nem alapértelmezett.

[ @target_id =] target_id  
 A cél tartozó azonosítószámot a célként megadott csoport tagja, el kell távolítani. target_id egy uniqueidentifier, az alapértelmezett érték NULL.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Célcsoportok, amelyekre egy feladatot egy adatbázis-gyűjtemény, egyszerű módot biztosítanak.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.

#### <a name="examples"></a>Példák
A következő példában eltávolítjuk a londoni server kiszolgálók karbantartása ügyfél-információkat a csoportból. A feladatok adatbázis létrehozásakor megadott a feladatügynök a jelen esetben ElasticJobs kell kapcsolódnia.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sppurgejobhistory"></a>sp_purge_jobhistory

Eltávolít egy feladat replikálásielőzmény-rekord.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A feladat, amelynek a replikálásielőzmény-rekord törlése neve. job_name nvarchar(128), az alapértelmezett érték NULL. Meg kell adni job_id vagy a job_name, de mindkettő nem adható meg.

[  **@job_id =** ] job_id  
 A feladat a feladat a törlendő rekordok azonosítószáma. job_id uniqueidentifier, az alapértelmezett érték NULL. Meg kell adni job_id vagy a job_name, de mindkettő nem adható meg.

[  **@oldest_date =** ] oldest_date  
 A legrégebbi az előzményekben megőrizni kívánt rekord. oldest_date DATETIME2, akkor az alapértelmezett érték NULL. Ha oldest_date meg van adva, sp_purge_jobhistory csak eltávolítja azokat a rekordokat, amelyek régebbi, mint a megadott érték.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba) megjegyzések célcsoportokat, amelyekre egy feladatot egy adatbázis-gyűjtemény, egyszerű módot biztosítanak.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre ezt a tárolt eljárást. Akkor korlátozzák a felhasználót, hogy csak a feladatok nyomon kell, a felhasználó a következő adatbázis-szerepkör a feladat ügynök adatbázisban a feladatügynök létrehozásakor megadott részeként biztosítani:
- jobs_reader

Ezek a szerepkörök engedélyeivel kapcsolatos részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztését.

#### <a name="examples"></a>Példák
Az alábbi példa adatbázisaihoz hozzáadja a csoporthoz kiszolgálók karbantartása ügyfél-információkat a londoni és NewYork kiszolgálókon. A feladatok adatbázis létrehozásakor megadott a feladatügynök a jelen esetben ElasticJobs kell kapcsolódnia.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Feladat nézetek

A következő nézetek érhetők el a [feladatok adatbázis](elastic-jobs-overview.md#job-database).


|Nézet  |Leírás  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Látható feladat-végrehajtási előzményei.      |
|[Feladatok](#jobs-view)     |   Az összes feladat látható.      |
|[job_versions](#jobversions-view)     |   Az összes feladat verzió látható.      |
|[feladatlépésnél](#jobsteps-view)     |     Minden egyes feladat jelenlegi verziója minden lépéseit mutatja be.    |
|[jobstep_versions](#jobstepversions-view)     |     Minden egyes feladat összes verzióját az összes lépéseit mutatja be.    |
|[target_groups](#targetgroups-view)     |      Az összes célcsoportok mutatja.   |
|[target_group_members](#targetgroups-view)     |   Minden célként megadott csoport minden tagját mutatja.      |


### <a name="jobsexecutions-view"></a>jobs_executions megtekintése

[feladatok]. [jobs_executions]

Látható feladat-végrehajtási előzményei.


|Oszlop neve|   Adattípus   |Leírás|
|---------|---------|---------|
|**job_execution_id**   |UniqueIdentifier|  A feladat-végrehajtás egy példányát egyedi azonosítója.
|**job_name**   |nvarchar(128)  |A feladat nevét.
|**job_id** |UniqueIdentifier|  A feladat egyedi azonosítója.
|**job_version**    |int    |A feladat (automatikusan frissül minden alkalommal, amikor módosul, a feladat) verziója.
|**step_id**    |int|   (A feladathoz) egyedi azonosítója a lépéssel. NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**is_active**| bit |Azt jelzi, hogy adatokat az aktív vagy inaktív. 1 aktív feladatok azt jelzi, és a 0 azt jelzi, hogy inaktív.
|**életciklus**| nvarchar(50)|A feladat állapotát jelző érték: "Létrehozva", "Folyamatban", "Sikertelen", "Sikeres", "Kihagyva", "SucceededWithSkipped"|
|**create_time**|   datetime2(7)|   Dátum és idő a feladat létrehozása.
|**start_time** |datetime2(7)|  Dátum és idő a feladat végrehajtási elindult. NULL értékű, ha a feladat még nem lett végrehajtva.
|**end_time**|  datetime2(7)    |Dátum és idő a feladat végrehajtása befejeződött. NULL értékű, ha még nem lett végrehajtva a feladat, vagy nem rendelkezik még végrehajtása befejeződött.
|**current_attempts**   |int    |Kísérelte meg a lépés hányszor. Szülő feladat lesz 0, gyermek feladatvégrehajtások 1 lesz, vagy a végrehajtási házirend nagyobb alapján.
|**current_attempt_start_time** |datetime2(7)|  Dátum és idő a feladat végrehajtási elindult. NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**last_message**   |típus: nvarchar(max)| Feladat és lépés előzmények üzenet. 
|**target_type**|   nvarchar(128)   |Céladatbázis vagy az adatbázis összes adatbázissal egy kiszolgálót, egy rugalmas készletben található összes adatbázis vagy egy adatbázis-gyűjtemény típusa. Target_type érvényes értékei a következők: "SqlServer", "SqlElasticPool" vagy "SqlDatabase". NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_id**  |UniqueIdentifier|  A célként megadott csoport tagja egyedi azonosítója.  NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_group_name**  |nvarchar(128)  |A célcsoport neve. NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_server_name**|    nvarchar(256)|  A célként megadott csoportban lévő logikai kiszolgáló nevét. A megadott csak target_type "SqlServer"-e. NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_database_name**   |nvarchar(128)| A célként megadott csoportban szereplő adatbázis nevét. Megadott csak amikor target_type "SqlDatabase". NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.


### <a name="jobs-view"></a>feladatok megtekintése

[feladatok]. [feladatok]

Az összes feladat látható.

|Oszlop neve|   Adattípus|  Leírás|
|------|------|-------|
|**job_name**|  nvarchar(128)   |A feladat nevét.|
|**job_id**|    UniqueIdentifier    |A feladat egyedi azonosítója.|
|**job_version**    |int    |A feladat (automatikusan frissül minden alkalommal, amikor módosul, a feladat) verziója.|
|**description**    |nvarchar(512)| A feladat leírását. engedélyezett bit azt jelzi, hogy a feladat engedélyezve van-e vagy le van tiltva. 1 azt jelzi, hogy engedélyezett feladatok, és a 0 azt jelzi, hogy letiltott feladatok.|
|**schedule_interval_type** |nvarchar(50)   |Érték, amely jelzi, ha a feladat végrehajtását van: az "egyszeri", "Percek", "Hours", "Nap", "hét", "Hónap"
|**schedule_interval_count**|   int|    Minden egyes végrehajtása a feladat a közti schedule_interval_type időszakok száma.|
|**schedule_start_time**    |datetime2(7)|  Dátum és a feladat volt elindítva utolsó végrehajtási időpont.|
|**schedule_end_time**| datetime2(7)|   Dátum és idő a feladat volt a legutóbbi művelet befejeződött.|


### <a name="jobversions-view"></a>job_versions megtekintése

[feladatok]. [job_verions]

Az összes feladat verzió látható.

|Oszlop neve|   Adattípus|  Leírás|
|------|------|-------|
|**job_name**|  nvarchar(128)   |A feladat nevét.|
|**job_id**|    UniqueIdentifier    |A feladat egyedi azonosítója.|
|**job_version**    |int    |A feladat (automatikusan frissül minden alkalommal, amikor módosul, a feladat) verziója.|


### <a name="jobsteps-view"></a>feladatlépésnél megtekintése

[feladatok]. [feladatlépésnél]

Minden egyes feladat jelenlegi verziója minden lépéseit mutatja be.

|Oszlop neve    |Adattípus| Leírás|
|------|------|-------|
|**job_name**   |nvarchar(128)| A feladat nevét.|
|**job_id** |UniqueIdentifier   |A feladat egyedi azonosítója.|
|**job_version**|   int|    A feladat (automatikusan frissül minden alkalommal, amikor módosul, a feladat) verziója.|
|**step_id**    |int    |(A feladathoz) egyedi azonosítója a lépéssel.|
|**step_name**  |nvarchar(128)  |(A feladathoz) egyedi neve a lépéshez.|
|**command_type**   |nvarchar(50)   |A feladat lépésben végrehajtandó parancs típusa. V1, az értéknek meg kell egyeznie a és "TSql" az alapértelmezett érték.|
|**command_source** |nvarchar(50)|  A parancs helye. A 1-es "Beágyazott" az alapértelmezett beállítás, és csak elfogadható érték.|
|**A parancs**|   típus: nvarchar(max)|  Rugalmas feladatok command_type keresztül hajtja végre a parancsokat.|
|**credential_name**|   nvarchar(128)   |Végrehajtási a feladat segítségével az adatbázishoz kötődő hitelesítő adat nevét.|
|**target_group_name**| nvarchar(128)   |A célcsoport neve.|
|**target_group_id**|   UniqueIdentifier|   A célként megadott csoport egyedi azonosítója.|
|**initial_retry_interval_seconds**|    int |A késleltetés az első újrapróbálkozási kísérlet előtt. Alapértelmezett érték az 1.|
|**maximum_retry_interval_seconds** |int|   Az újrapróbálkozási kísérletek közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb, mint ez az érték akkor növekszik, azt a maximumon erre az értékre helyette. Alapértelmezett értéke 120.|
|**retry_interval_backoff_multiplier**  |valódi|  A alkalmazni újrapróbálkozási késleltetéshez, ha több feladat végrehajtási lépés szorzó kísérletek sikertelenek. Alapértelmezett érték a 2.0-s.|
|**retry_attempts** |int|   Az újrapróbálkozási kísérletek száma használhat, ha ez a lépés sikertelen lesz. Alapértelmezett érték 10, amely azt jelzi, hogy nem az újrapróbálkozási kísérletek.|
|**step_timeout_seconds**   |int|   Az újrapróbálkozási kísérletek közötti percek időtartama. Az alapértelmezett érték 0, amely azt jelzi, hogy egy 0 perces időszakban.|
|**output_type**    |nvarchar(11)|  A parancs helye. Az aktuális előzetes verzióban érhető el "Beágyazott" az alapértelmezett beállítás, és csak elfogadható érték.|
|**output_credential_name**|    nvarchar(128)   |Neve a tárolja az eredményeket a célkiszolgálóra való kapcsolódáshoz használt hitelesítő adatok megadása|
|**output_subscription_id**|    UniqueIdentifier|   Az előfizetés, az eredményeket adja meg a lekérdezés végrehajtása a cél server\database egyedi azonosítója.|
|**output_resource_group_name** |nvarchar(128)| A célkiszolgáló tartalmazó erőforráscsoport neve.|
|**output_server_name**|    nvarchar(256)   |Az eredménykészletet a célkiszolgáló nevét.|
|**output_database_name**   |nvarchar(128)| Az eredménykészletet a céladatbázis neve.|
|**output_schema_name** |típus: nvarchar(max)| A cél séma neve. Alapértelmezés szerint a dbo, ha nincs megadva.|
|**output_table_name**| típus: nvarchar(max)|  Az eredmények, adja meg a lekérdezés eredményeinek tárolására a tábla neve. Táblázat az eredmények, ha még nem létezik a séma alapján automatikusan létrejön. Séma meg kell egyeznie az eredménykészletet sémája.|
|**max_parallelism**|   int|    Adatbázisok száma, amelyek a feladat lépésének fut egyszerre rugalmas készlet maximális számát. Az alapértelmezett érték NULL, tehát nincs korlátozva. |


### <a name="jobstepversions-view"></a>jobstep_versions megtekintése

[feladatok]. [jobstep_versions]

Minden egyes feladat összes verzióját az összes lépéseit mutatja be. A séma megegyezik az [feladatlépésnél](#jobsteps-view).

### <a name="targetgroups-view"></a>target_groups megtekintése

[feladatok]. [target_groups]

Az összes cél csoportok listája.

|Oszlop neve|Adattípus| Leírás|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |A célcsoportot, egy adatbázis-gyűjtemény neve. 
|**target_group_id**    |UniqueIdentifier   |A célként megadott csoport egyedi azonosítója.

### <a name="targetgroupsmembers-view"></a>target_groups_members megtekintése

[feladatok]. [target_groups_members]

Minden célként megadott csoport minden tagját mutatja.

|Oszlop neve|Adattípus| Leírás|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|A célcsoportot, egy adatbázis-gyűjtemény neve. |
|**target_group_id**    |UniqueIdentifier   |A célként megadott csoport egyedi azonosítója.|
|**membership_type**    |int|   Itt adhatja meg, ha a célként megadott csoport tagja tartalmaz, vagy a célcsoportban kizárt. Target_group_name érvényes értékei a következők: "Include" vagy "Kizárása".|
|**target_type**    |nvarchar(128)| Céladatbázis vagy az adatbázis összes adatbázissal egy kiszolgálót, egy rugalmas készletben található összes adatbázis vagy egy adatbázis-gyűjtemény típusa. Target_type érvényes értékei a következők: "SqlServer", "SqlElasticPool", "SqlDatabase" vagy "SqlShardMap".|
|**target_id**  |UniqueIdentifier|  A célként megadott csoport tagja egyedi azonosítója.|
|**refresh_credential_name**    |nvarchar(128)  |Az adatbázis nevét a célként megadott csoport tagja való kapcsolódáshoz használt hitelesítő adatok hatókörét.|
|**subscription_id**    |UniqueIdentifier|  Az előfizetés egyedi azonosítója.|
|**resource_group_name**    |nvarchar(128)| Neve az erőforráscsoport, amelyben megtalálható a célként megadott csoport tagja.|
|**kiszolgálónév**    |nvarchar(128)  |A célként megadott csoportban lévő logikai kiszolgáló nevét. A megadott csak target_type "SqlServer"-e. |
|**adatbázisnév**  |nvarchar(128)  |A célként megadott csoportban szereplő adatbázis nevét. Megadott csak amikor target_type "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| A célként megadott csoportban lévő a rugalmas készlet neve. Megadott csak amikor target_type "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| A célként megadott csoportban lévő horizontális skálázási térképet neve. Megadott csak amikor target_type "SqlShardMap".|


## <a name="resources"></a>További források

 - ![Témakör-hivatkozás ikon](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "témakör kapcsolat ikon") [Transact-SQL-szintaxis konvenciók](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>További lépések

- [Rugalmas feladatok létrehozása és kezelése a PowerShell használatával](elastic-jobs-powershell.md)
- [Engedélyezési és az engedélyek az SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  
