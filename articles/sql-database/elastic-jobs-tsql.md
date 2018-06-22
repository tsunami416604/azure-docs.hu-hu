---
title: Feladatok létrehozásához és kezeléséhez Azure SQL rugalmas adatbázis Transact-SQL (T-SQL) használatával |} Microsoft Docs
description: Futtassa a parancsfájlok több adatbázis közötti rugalmas adatbázis-feladat ügynök Transact-SQL (T-SQL) használatával.
services: sql-database
author: jaredmoo
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: jaredmoo
ms.openlocfilehash: fb6e4ebd635d8afa8e679ee5bb0f5646f28f887b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313338"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>A rugalmas adatbázis-feladatok létrehozásához és kezeléséhez a Transact-SQL (T-SQL) használatával

Ez a cikk ismerteti a rugalmas feladatok használatának megkezdéséhez sok példaforgatókönyvek T-SQL használatával.

A példában a [tárolt eljárások](#job-stored-procedures) és [nézetek](#job-views) érhető el a [ *feladat adatbázis*](elastic-jobs-overview.md#job-database).

Transact-SQL (T-SQL) segítségével létrehozása, konfigurálása, végrehajtási és feladatok kezelése. A rugalmas feladat ügynök nem támogatott az T-SQL-ben, előbb létre kell hoznia egy *rugalmas feladat ügynök* a portált használja, vagy [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>A feladat-végrehajtás hitelesítő adatok létrehozása

A hitelesítő adatok szolgál céladatbázisokhoz a céladatbázisokhoz való csatlakozáshoz. A hitelesítő adatokat kell a megfelelő engedélyekkel, az adatbázisok, a célcsoport által megadott sikeresen végrehajtani a parancsprogramot. A kiszolgálón és/vagy a cél csoport készlettag használatakor magas javasolt hozzon létre egy fő hitelesítő adat segítségével frissítse a hitelesítő adatok a feladatok végrehajtásának időpontjában a kiszolgálón és/vagy a készlet bővítése előtt. Az adatbázishoz kötődő hitelesítő adatok a feladat ügynök adatbázis jön létre. Lehet, hogy ugyanazon hitelesítő használt *-bejelentkezés létrehozásával* és *hozzon létre egy felhasználó bejelentkezési identitás: a bejelentkezés adatbázis-engedélyek megadására* a cél adatbázisok.


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

A következő példa bemutatja, hogyan hajthat végre egy feladat minden adatbázisokhoz kiszolgálón.  
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


## <a name="exclude-a-single-database"></a>Egy önálló adatbázis kizárása

A következő példa bemutatja, hogyan hajthat végre egy feladat, szemben az összes adatbázis-kiszolgálók, kivéve az adatbázis nevesített *MappingDB*.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group = N'ServerGroup'
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

A következő példa bemutatja, hogyan, amelyekre egy vagy több rugalmas készletek összes adatbázisát.  
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


## <a name="deploy-new-schema-to-many-databases"></a>Új séma számos más adatbázis központi telepítése

A következő példa bemutatja, hogyan telepítése új sémát az összes adatbázisra.  
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


## <a name="data-collection-using-built-in-parameters"></a>Adatgyűjtés beépített paraméterek használata

Adatok gyűjtése helyzetekben hasznos lehet bizonyos parancsfájl-kezelési változókhoz utófeldolgozási a feladat eredményeinek segítségével.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Például minden eredmény együtt ugyanazon feladat végrehajtása a csoportba, használja a *$(job_execution_id)* látható módon a következő parancsot:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Adatbázis teljesítményének monitorozása

Az alábbi példa létrehoz egy új feladatot, amely több adatbázisból származó teljesítményadatokat gyűjtenek.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) és a következő parancsokat:

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


## <a name="view-job-definitions"></a>Nézet feladatdefiníciók

A következő példa bemutatja, hogyan aktuális feladatdefiníciók megtekintéséhez.  
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

A következő példa bemutatja, hogyan azonnal elindítja a feladatot.  
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


## <a name="schedule-execution-of-a-job"></a>A feladat végrehajtásának ütemezése

A következő példa bemutatja, hogyan egy feladat jövőbeli végrehajtásra ütemezni.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>A figyelő feladat végrehajtási állapota

A következő példa bemutatja, hogyan összes feladat végrehajtási állapot részleteinek megtekintése.  
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

A következő példa bemutatja, hogyan visszavonni a feladatot.  
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

A következő példa bemutatja, hogyan adott dátum előtti feladatelőzmények törlése.  
Csatlakozás a [ *feladat adatbázis* ](elastic-jobs-overview.md#job-database) , és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Egy feladat és a feladatelőzmények törlése

A következő példa bemutatja, hogyan törli a feladatot, és az összes kapcsolódó feladatelőzményekben.  
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
|[sp_update_job ](#spupdatejob)    |      A frissítések egy meglévő feladata.   |
|[sp_delete_job](#spdeletejob)     |      Egy meglévő feladat törlése.   |
|[sp_add_jobstep](#spaddjobstep)    |    A lépés ad hozzá egy feladatot.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Egy feladat lépésének frissíti.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Egy feladat lépésének törli.    |
|[sp_start_job](#spstartjob)    |  Egy feladat végrehajtásának elkezdése.       |
|[sp_stop_job](#spstopjob)     |     A feladat végrehajtása leáll.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Cél csoport hozzáadása.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    Egy célcsoport törli.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Adatbázis vagy az adatbázisok csoport hozzáadása egy célcsoportot.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     Eltávolítja a cél csoporttag egy célcsoportot.    |
|[sp_purge_jobhistory ](#sppurgejobhistory)    |    Eltávolítja a előzményrekordjai feladat.     |





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
A feladat nevét. A nevének egyedinek kell lennie, és nem tartalmazhatja a százalék (%) karaktert. job_name nvarchar(128) nincs alapértelmezett értéke.

[  **@description =** ] "description"  
A feladat leírása. Leírás: nvarchar(512), az alapértelmezett érték NULL. Ha leírása hiányzik, üres karakterlánc szolgál.

[  **@enabled =** ] engedélyezve  
A feladat ütemezésének engedélyezve van-e. Engedélyezett bit, az alapértelmezett érték a 0 (letiltva). Ha 0, a feladat nincs engedélyezve, és nem fut megfelelően az ütemezését ennek; azonban futtatása manuálisan. Ha 1, a feladat az ütemezése szerint fog futni, és manuálisan is futtatható.

[  **@schedule_interval_type =**] schedule_interval_type  
Érték jelzi a feladat futtatását. schedule_interval_type nvarchar(50), az alapértelmezett érték egyszer, és a következő értékek egyike lehet:
- Az "egyszeri"
- "Minutes",
- "Hours",
- "Days",
- "Hét, a"
- "Hónap"

[  **@schedule_interval_count =** ] schedule_interval_count  
A feladat minden egyes végrehajtása közti schedule_interval_count időszakok számát. schedule_interval_count int, az alapértelmezett 1 érték. Az érték kisebb, mint 1-nél kell lennie.

[  **@schedule_start_time =** ] schedule_start_time  
A dátum mely feladat végrehajtásának megkezdése. schedule_start_time DATETIME2, a rendszer az alapértelmezett 0001-01-01 00:00:00.0000000 jelenti.

[  **@schedule_end_time =** ] schedule_end_time  
A dátum mely feladat végrehajtási leállíthatja. schedule_end_time DATETIME2, akkor az alapértelmezett 9999-12-31 11:59:59.0000000. 

[  **@job_id =** ] job_id kimeneti  
A feladat azonosítószámának a feladathoz rendelt, ha sikeresen létrehozva. job_id egy kimeneti változó típusa UniqueIdentifier.

#### <a name="return-code-values"></a>Visszatérési kód értéket

0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
a feladat ügynök létrehozásakor megadott feladat ügynök adatbázis sp_add_job kell futtatni.
Miután sp_add_job hozzáadása egy feladatot végre lett hajtva, sp_add_jobstep segítségével lépéseket adhat hozzá, a tevékenységet a feladat végez. A feladat kezdeti verziószáma 0, 1 nőni fog, amikor az első lépés ad hozzá.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:

- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.

### <a name="spupdatejob"></a>sp_update_job

A frissítések egy meglévő feladata.

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
Frissíteni kell a feladat nevét. job_name nvarchar(128).

[  **@new_name =** ] "új_név"  
A feladat új neve. új_név nvarchar(128).

[  **@description =** ] "description"  
A feladat leírása. Leírás: nvarchar(512).

[  **@enabled =** ] engedélyezve  
Megadja, hogy a feladatütemezés enabled (1), vagy nincs engedélyezve az (0). Engedélyezve van-e bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Érték jelzi a feladat futtatását. schedule_interval_type nvarchar(50), és a következő értékek egyike lehet:

- Az "egyszeri"
- "Minutes",
- "Hours",
- "Days",
- "Hét, a"
- "Hónap"

[  **@schedule_interval_count=** ] schedule_interval_count  
A feladat minden egyes végrehajtása közti schedule_interval_count időszakok számát. schedule_interval_count int, az alapértelmezett 1 érték. Az érték kisebb, mint 1-nél kell lennie.

[  **@schedule_start_time=** ] schedule_start_time  
A dátum mely feladat végrehajtásának megkezdése. schedule_start_time DATETIME2, a rendszer az alapértelmezett 0001-01-01 00:00:00.0000000 jelenti.

[  **@schedule_end_time=** ] schedule_end_time  
A dátum mely feladat végrehajtási leállíthatja. schedule_end_time DATETIME2, akkor az alapértelmezett 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Miután sp_add_job hozzáadása egy feladatot végre lett hajtva, sp_add_jobstep segítségével lépéseket adhat hozzá, a tevékenységet a feladat végez. A feladat kezdeti verziószáma 0, 1 nőni fog, amikor az első lépés ad hozzá.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.



### <a name="spdeletejob"></a>sp_delete_job

Egy meglévő feladat törlése.

#### <a name="syntax"></a>Szintaxis

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A törölni kívánt feladat neve. job_name nvarchar(128).

[  **@force =** ] kényszerítése  
Megadja, hogy törölje, ha a feladat minden végrehajtások van folyamatban, és az összes folyamatban lévő végrehajtások (1) vagy sikertelen megszüntetése, ha a minden feladat végrehajtások van folyamatban (0). kényszerített bit.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Feladatelőzmények egy feladat törlésekor automatikusan törlődik.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.



### <a name="spaddjobstep"></a>sp_add_jobstep

A lépés ad hozzá egy feladatot.

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
A feladat lépésének feladatütemezési azonosítószáma. Lépés azonosítószámok számozása 1-gyel kezdődik, és növelheti szünetek nélkül. Ha egy meglévő lépés már ezt az azonosítót, majd, hogy lépés és a következő lépéseket kell azonosítójának tartozó eggyel növekszik, hogy ez a lépés szúrhatók be, a feladatütemezési. Ha nincs megadva, a step_id a rendszer automatikusan hozzárendeli az utolsó a lépések sorrendjét. step_id egy egész szám.

[  **@step_name =** ] step_name  
A lépés neve. Meg kell adni, kivéve az első lépés egy feladatot, amely (a kényelem) alapértelmezett neve a "Feladatlépés használja". step_name nvarchar(128).

[  **@command_type =** ] "command_type"  
Ez a feladatlépés használja által végrehajtott parancs típusa. command_type nvarchar(50) TSql, ami azt jelenti, hogy értéke alapértelmezés szerint a rendszer a @command_type paraméter T-SQL parancsfájl.

Ha meg van adva, az érték TSql kell lennie.

[  **@command_source =** ] "command_source"  
A parancs tárolási helyének típusa. command_source nvarchar(50) beágyazott, ami azt jelenti, hogy értéke alapértelmezés szerint a rendszer a @command_source paramétere a parancs a szövegkonstans.

Ha meg van adva, az érték beágyazott kell lennie.

[  **@command =** ] "command"  
A parancsot érvényes T-SQL parancsfájl kell lennie, és a feladat lépésben végrehajthatók. a parancs akkor típus: nvarchar(max), az alapértelmezett érték NULL.

[  **@credential_name =** ] "credential_name"  
Az adatbázis nevét kötődő hitelesítő adatok a feladatvezérlő adatbázishoz való csatlakozáshoz a céladatbázisokhoz a célként megadott csoportban lévő egyes, ha ez a lépés végrehajtása használt tárolja. credential_name nvarchar(128).

[  **@target_group_name =** ] "cél-csoportnév"  
A célcsoportot, amely tartalmazza a céladatbázisokhoz, amely a feladat lépésének végrehajtja a neve. target_group_name nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
A késleltetés, mielőtt az első újrapróbálkozásnál, ha a feladat lépésének kezdeti végrehajtási tett kísérlet sikertelen. initial_retry_interval_seconds int, alapértelmezett értéke 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
A maximális késleltetés, újrapróbálkozások között. Ha az újrapróbálkozások közötti késleltetés nagyobb, mint ez az érték akkor növekszik, akkor tárfiókonként erre az értékre helyette. maximum_retry_interval_seconds int, alapértelmezett érték 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
A többszöröző alkalmazandó az újrapróbálkozási késleltetést, ha több feladat lépés végrehajtása sikertelen kísérlet után. Például ha az első újrapróbálkozásnál kellett 5 másodperces késleltetést és a leállítási többszöröző 2.0, majd a második újrapróbálkozási 10 másodperc várnia kell, és a harmadik újrapróbálkozási 20 másodperc várnia kell. retry_interval_backoff_multiplier valós 2.0 alapértelmezett értéke.

[  **@retry_attempts =** ] retry_attempts  
Újrapróbálja a futtatást, ha a kezdeti sikertelen bejelentkezések száma. Például ha retry_attempts értéke 10., akkor nem lesznek 1 kezdeti kísérlet és 10 az újrapróbálkozások, egyúttal jogosultságot ad a 11 kísérletek összesen. Ha a végső újrapróbálkozási kísérlet sikertelen, majd a feladat végrehajtásának megszünteti egy életciklusával kapcsolatos sikertelen. retry_attempts int, az alapértelmezett érték 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
A maximális időt a lépés végrehajtásához engedélyezett. Megadott idő túllépése, majd a feladat végrehajtásának megszünteti egy életciklusával kapcsolatos időtúllépésbe került. step_timeout_seconds int 43 200 másodperc (12 óra) alapértelmezett értéke.

[  **@output_type =** ] "output_type"  
Ha nem null, a cél, hogy a parancs első eredmény típus nevével. output_type nvarchar(50), az alapértelmezett érték NULL.

Ha meg van adva, az érték SqlDatabase kell lennie.

[  **@output_credential_name =** ] "output_credential_name"  
Nem null, ha az adatbázis nevét hatókörét a kimeneti cél adatbázishoz való kapcsolódáshoz használt hitelesítő adatok. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. output_credential_name nvarchar(128), NULL alapértelmezett értékű.

[  **@output_subscription_id =** ] "output_subscription_id"  
Leírás szükséges.

[  **@output_resource_group_name =** ] "output_resource_group_name"  
Leírás szükséges.

[  **@output_server_name =** ] "output_server_name"  
Ha nem null, a kimeneti adatbázist tartalmazó kiszolgáló teljesen minősített DNS-nevét. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. output_server_name nvarchar(256), az alapértelmezett érték NULL.

[  **@output_database_name =** ] "output_database_name"  
Ha nem null értékű, az adatbázis nevét, amely tartalmazza a kimeneti céltábla. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. output_database_name nvarchar(128), az alapértelmezett érték NULL.

[  **@output_schema_name =** ] "output_schema_name"  
Ha nem null értékű, az SQL-séma, amely tartalmazza a kimeneti céltábla nevét. Ha a output_type SqlDatabase, alapértelmezett értéke dbo. output_schema_name nvarchar(128).

[  **@output_table_name =** ] "output_table_name"  
Ha nem null, a táblázat, amely a parancs első eredmény neve lesz írva. Ha a tábla nem létezik, akkor létrehozza az adatszolgáltató eredménykészlet séma alapján. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. output_table_name nvarchar(128), NULL alapértelmezett értékű.

[  **@job_version =** ] job_version kimeneti  
A kimeneti paraméter, amely az új feladat verziószámot hozzá lesz rendelve. job_version az egész szám.

[  **@max_parallelism =** ] max_parallelism kimeneti  
A rugalmas készlet száma párhuzamos maximális szintjét. Ha be van állítva, a feladat lépésének csak futhat, amely legfeljebb egy rugalmas készlet sok adatbázis korlátozott lesz. Ez vonatkozik, minden egyes rugalmas készlethez, vagy közvetlenül megtalálható a célcsoportot, vagy a kiszolgálón, amely megtalálható a célcsoport belül. max_parallelism az egész szám.


#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Ha a sp_add_jobstep sikeres, a feladat jelenlegi verziószám értéke akkor növekszik. A feladat végrehajtása, amikor legközelebb az új verziót fogja használni. Ha a feladat végrehajtása folyamatban van, a végrehajtás nem fog tartalmazni az új lépés.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:  

- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.



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
A feladat, amelyhez tartozik a lépés neve. job_name nvarchar(128).

[  **@step_id =** ] step_id  
A módosítani kívánt feladat lépésének azonosítószáma. Step_id vagy step_name meg kell adni. step_id egy egész szám.

[  **@step_name =** ] "step_name"  
A módosítani kívánt lépés neve. Step_id vagy step_name meg kell adni. step_name nvarchar(128).

[  **@new_id =** ] new_id  
A feladat lépésének új feladatütemezési azonosítószáma. Lépés azonosítószámok számozása 1-gyel kezdődik, és növelheti szünetek nélkül. Ha egy lépés van újra, majd más lépéseket fogja lehet automatikusan újraszámozásakor.

[  **@new_name =** ] "új_név"  
A lépés új neve. új_név nvarchar(128).

[  **@command_type =** ] "command_type"  
Ez a feladatlépés használja által végrehajtott parancs típusa. command_type nvarchar(50) TSql, ami azt jelenti, hogy értéke alapértelmezés szerint a rendszer a @command_type paraméter T-SQL parancsfájl.

Ha meg van adva, az érték TSql kell lennie.

[  **@command_source =** ] "command_source"  
A parancs tárolási helyének típusa. command_source nvarchar(50) beágyazott, ami azt jelenti, hogy értéke alapértelmezés szerint a rendszer a @command_source paramétere a parancs a szövegkonstans.

Ha meg van adva, az érték beágyazott kell lennie.

[  **@command =** ] "command"  
A command(s) érvényes T-SQL parancsfájl kell lennie, és a feladat lépésben végrehajthatók. a parancs akkor típus: nvarchar(max), az alapértelmezett érték NULL.

[  **@credential_name =** ] "credential_name"  
Az adatbázis nevét kötődő hitelesítő adatok a feladatvezérlő adatbázishoz való csatlakozáshoz a céladatbázisokhoz a célként megadott csoportban lévő egyes, ha ez a lépés végrehajtása használt tárolja. credential_name nvarchar(128).

[  **@target_group_name =** ] "cél-csoportnév"  
A célcsoportot, amely tartalmazza a céladatbázisokhoz, amely a feladat lépésének végrehajtja a neve. target_group_name nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
A késleltetés, mielőtt az első újrapróbálkozásnál, ha a feladat lépésének kezdeti végrehajtási tett kísérlet sikertelen. initial_retry_interval_seconds int, alapértelmezett értéke 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
A maximális késleltetés, újrapróbálkozások között. Ha az újrapróbálkozások közötti késleltetés nagyobb, mint ez az érték akkor növekszik, akkor tárfiókonként erre az értékre helyette. maximum_retry_interval_seconds int, alapértelmezett érték 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
A többszöröző alkalmazandó az újrapróbálkozási késleltetést, ha több feladat lépés végrehajtása sikertelen kísérlet után. Például ha az első újrapróbálkozásnál kellett 5 másodperces késleltetést és a leállítási többszöröző 2.0, majd a második újrapróbálkozási 10 másodperc várnia kell, és a harmadik újrapróbálkozási 20 másodperc várnia kell. retry_interval_backoff_multiplier valós 2.0 alapértelmezett értéke.

[  **@retry_attempts =** ] retry_attempts  
Újrapróbálja a futtatást, ha a kezdeti sikertelen bejelentkezések száma. Például ha retry_attempts értéke 10., akkor nem lesznek 1 kezdeti kísérlet és 10 az újrapróbálkozások, egyúttal jogosultságot ad a 11 kísérletek összesen. Ha a végső újrapróbálkozási kísérlet sikertelen, majd a feladat végrehajtásának megszünteti egy életciklusával kapcsolatos sikertelen. retry_attempts int, az alapértelmezett érték 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
A maximális időt a lépés végrehajtásához engedélyezett. Megadott idő túllépése, majd a feladat végrehajtásának megszünteti egy életciklusával kapcsolatos időtúllépésbe került. step_timeout_seconds int 43 200 másodperc (12 óra) alapértelmezett értéke.

[  **@output_type =** ] "output_type"  
Ha nem null, a cél, hogy a parancs első eredmény típus nevével. Visszaállítani output_type értéke NULL, adja meg ennek a paraméternek értéket "(üres karakterlánc). output_type nvarchar(50), az alapértelmezett érték NULL.

Ha meg van adva, az érték SqlDatabase kell lennie.

[  **@output_credential_name =** ] "output_credential_name"  
Nem null, ha az adatbázis nevét hatókörét a kimeneti cél adatbázishoz való kapcsolódáshoz használt hitelesítő adatok. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. Visszaállítani output_credential_name értéke NULL, adja meg ennek a paraméternek értéket "(üres karakterlánc). output_credential_name nvarchar(128), NULL alapértelmezett értékű.

[  **@output_server_name =** ] "output_server_name"  
Ha nem null, a kimeneti adatbázist tartalmazó kiszolgáló teljesen minősített DNS-nevét. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. Visszaállítani output_server_name értéke NULL, adja meg ennek a paraméternek értéket "(üres karakterlánc). output_server_name nvarchar(256), az alapértelmezett érték NULL.

[  **@output_database_name =** ] "output_database_name"  
Ha nem null értékű, az adatbázis nevét, amely tartalmazza a kimeneti céltábla. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. Visszaállítani output_database_name értéke NULL, adja meg ennek a paraméternek értéket "(üres karakterlánc). output_database_name nvarchar(128), az alapértelmezett érték NULL.

[  **@output_schema_name =** ] "output_schema_name"  
Ha nem null értékű, az SQL-séma, amely tartalmazza a kimeneti céltábla nevét. Ha a output_type SqlDatabase, alapértelmezett értéke dbo. Visszaállítani output_schema_name értéke NULL, adja meg ennek a paraméternek értéket "(üres karakterlánc). output_schema_name nvarchar(128).

[  **@output_table_name =** ] "output_table_name"  
Ha nem null, a táblázat, amely a parancs első eredmény neve lesz írva. Ha a tábla nem létezik, akkor létrehozza az adatszolgáltató eredménykészlet séma alapján. Kötelező megadni, ha output_type SqlDatabase megegyezik-e. Visszaállítani output_server_name értéke NULL, adja meg ennek a paraméternek értéket "(üres karakterlánc). output_table_name nvarchar(128), NULL alapértelmezett értékű.

[  **@job_version =** ] job_version kimeneti  
A kimeneti paraméter, amely az új feladat verziószámot hozzá lesz rendelve. job_version az egész szám.

[  **@max_parallelism =** ] max_parallelism kimeneti  
A rugalmas készlet száma párhuzamos maximális szintjét. Ha be van állítva, a feladat lépésének csak futhat, amely legfeljebb egy rugalmas készlet sok adatbázis korlátozott lesz. Ez vonatkozik, minden egyes rugalmas készlethez, vagy közvetlenül megtalálható a célcsoportot, vagy a kiszolgálón, amely megtalálható a célcsoport belül. Visszaállítani max_parallelism értéke null, állítsa a paraméter értéke-1. max_parallelism az egész szám.


#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Minden folyamatban lévő végrehajtások a feladat nem érinti. Ha sp_update_jobstep sikeres, a feladat verziószáma értéke akkor növekszik. A feladat végrehajtása, amikor legközelebb az új verziót fogja használni.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:

- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Egy feladat lépésének eltávolítása egy feladatot.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A feladat, amelyből a lépés eltávolítja a neve. job_name nvarchar(128) nincs alapértelmezett értéke.

[  **@step_id =** ] step_id  
Azonosítószáma. a feladat lépésének törölni. Step_id vagy step_name meg kell adni. step_id egy egész szám.

[  **@step_name =** ] "step_name"  
A lépés törli a neve. Step_id vagy step_name meg kell adni. step_name nvarchar(128).

[  **@job_version =** ] job_version kimeneti  
A kimeneti paraméter, amely az új feladat verziószámot hozzá lesz rendelve. job_version az egész szám.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Minden folyamatban lévő végrehajtások a feladat nem érinti. Ha sp_update_jobstep sikeres, a feladat verziószáma értéke akkor növekszik. A feladat végrehajtása, amikor legközelebb az új verziót fogja használni.

A többi feladat lépés kitöltse a törölt feladat lépésének által hátrahagyott résnek automatikusan újraszámozásakor lesz.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.






### <a name="spstartjob"></a>sp_start_job

Egy feladat végrehajtásának elkezdése.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A feladat, amelyből a lépés eltávolítja a neve. job_name nvarchar(128) nincs alapértelmezett értéke.

[  **@job_execution_id =** ] job_execution_id kimeneti  
A kimeneti paraméter, amely a feladat végrehajtási azonosítóhoz hozzá lesz rendelve. job_version uniqueidentifier.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Nincs.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.

### <a name="spstopjob"></a>sp_stop_job

A feladat végrehajtása leáll.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumentumok
[  **@job_execution_id =** ] job_execution_id  
A feladat végrehajtásának leállítása azonosító száma. job_execution_id uniqueidentifier, NULL alapértelmezett értéke.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Nincs.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Cél csoport hozzáadása.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumentumok
[  **@target_group_name =** ] "target_group_name"  
A célcsoport létrehozásához neve. target_group_name nvarchar(128) nincs alapértelmezett értéke.

[  **@target_group_id =** ] target_group_id kimeneti a célként megadott csoportot azonosító számot a feladathoz rendelt, ha sikeresen létrehozva. target_group_id egy kimeneti változó típusa UniqueIdentifier, az alapértelmezett érték NULL.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Célcsoportok, amelyekre az adatbázisok egy csoportját egy feladat könnyű megoldást biztosítson.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

Egy célcsoport törli.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumentumok
[  **@target_group_name =** ] "target_group_name"  
Törli a célcsoport neve. target_group_name nvarchar(128) nincs alapértelmezett értéke.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Nincs.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Adatbázis vagy az adatbázisok csoport hozzáadása egy célcsoportot.

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
A célcsoportot, amelyhez a tag lesz felvéve a neve. target_group_name nvarchar(128) nincs alapértelmezett értéke.

[  **@membership_type =** ] "membership_type"  
Itt adhatja meg, ha a cél csoporttag felügyelt vagy kizárt. target_group_name nvarchar(128), az "Include" alapértelmezett értéke. Target_group_name érvényes értékei a "Include" vagy "Kizárása".

[  **@target_type =** ] "target_type"  
A céladatbázis vagy -adatbázisokat, többek között az összes adatbázis-kiszolgálók, a rugalmas készletekben található összes adatbázis, a shard térképen az összes adatbázis vagy egyedi adatbázis gyűjteménye típusa. target_type nvarchar(128) nincs alapértelmezett értéke. Target_type érvényes értékei a "SqlServer", "SqlElasticPool", "SqlDatabase" vagy "SqlShardMap". 

[  **@refresh_credential_name =** ] "refresh_credential_name"  
A logikai kiszolgáló nevét. refresh_credential_name nvarchar(128) nincs alapértelmezett értéke.

[  **@server_name =** ] "kiszolgáló"  
A logikai kiszolgáló, amelyet kell adni a megadott célcsoportot a neve. kiszolgáló_neve meg kell adni, ha target_type "SqlServer". kiszolgáló_neve nvarchar(128) nincs alapértelmezett értéke.

[  **@database_name =** ] "adatbázisnév"  
Az adatbázis, amelyet kell adni a megadott célcsoportot a nevét. adatbázisnév meg kell adni, ha target_type "SqlDatabase". adatbázisnév nvarchar(128) nincs alapértelmezett értéke.

[  **@elastic_pool_name =** ] "elastic_pool_name"  
A rugalmas készlet a megadott célcsoportot a az új neve. elastic_pool_name meg kell adni, ha target_type "SqlElasticPool". elastic_pool_name nvarchar(128) nincs alapértelmezett értéke.

[  **@shard_map_name =** ] "shard_map_name"  
A szilánkok kell adni a megadott célcsoportot térkép készlettől neve. elastic_pool_name meg kell adni, ha target_type "SqlSqlShardMap". shard_map_name nvarchar(128) nincs alapértelmezett értéke.

[  **@target_id =** ] target_group_id kimeneti  
A cél azonosítószámának a cél csoporttag rendelt, ha létre célcsoport hozzáadni. target_id egy kimeneti változó típusa UniqueIdentifier, az alapértelmezett érték NULL.
Visszatérési kód értéket (sikeres) 0 vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Egy feladat végrehajtása a belül a kiszolgáló összes adatbázis, vagy a végrehajtási, amikor egy logikai kiszolgáló vagy a rugalmas készlethez tartozó rugalmas készlet megtalálható a célcsoport.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.

#### <a name="examples"></a>Példák
A következő példa az adatbázisokat a londoni és NewYork kiszolgálókon a csoporthoz hozzáadott kiszolgálók karbantartásának ügyfél adatait. A feladatok adatbázis létrehozásakor adni a feladat ügynök ezen eset ElasticJobs kell kapcsolódnia.

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

Eltávolítja a cél csoporttag egy célcsoportot.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] ‘target_id’]
```



Argumentumok [ @target_group_name =] "target_group_name"  
A célcsoportot, amelyből távolítsa el a célként megadott csoport tagjának neve. target_group_name nvarchar(128) nincs alapértelmezett értéke.

[ @target_id =] target_id  
 A cél rendelt azonosítószám cél csoporttag távolíthatók el. target_id egy egyedi azonosító, az alapértelmezett érték NULL.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
Célcsoportok, amelyekre az adatbázisok egy csoportját egy feladat könnyű megoldást biztosítson.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.

#### <a name="examples"></a>Példák
A következő példa a londoni kiszolgáló eltávolítása a kiszolgálók karbantartásának ügyféladatok csoport. A feladatok adatbázis létrehozásakor adni a feladat ügynök ezen eset ElasticJobs kell kapcsolódnia.

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

Eltávolítja a előzményrekordjai feladat.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentumok
[  **@job_name =** ] "job_name"  
A feladatot, amelynek a replikálásielőzmény-rekord törlése neve. job_name nvarchar(128), az alapértelmezett érték NULL. Meg kell adnia job_id vagy a job_name, de a kettő nem adható meg.

[  **@job_id =** ] job_id  
 A feladat a feladat törli a rekordok azonosítószáma. job_id az egyedi azonosító, az alapértelmezett érték NULL. Meg kell adnia job_id vagy a job_name, de a kettő nem adható meg.

[  **@oldest_date =** ] oldest_date  
 A legrégebbi bejegyzést az előzményekben. oldest_date DATETIME2, akkor az alapértelmezett érték NULL. Ha a oldest_date meg van adva, a sp_purge_jobhistory csak eltávolítja azt jelzi, hogy a régebbi, mint a megadott értéke.

#### <a name="return-code-values"></a>Visszatérési kód értéket
0 (sikeres) vagy 1 (hiba) megjegyzések célcsoportok, amelyekre az adatbázisok egy csoportját egy feladat könnyű megoldást biztosítson.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai hajthatják végre a tárolt eljárást. A korlátozása csak a felhasználók tudják feladatok figyelése, is meg lehet adni a felhasználó a feladat ügynök adatbázisban meg a feladat ügynök létrehozásakor a következő adatbázis-szerepkör részeként:
- jobs_reader

Ezek a szerepkörök engedélyekre vonatkozó részletekért lásd: a engedély szakasz ebben a dokumentumban. Csak a sysadmin (rendszergazda) tagjai a tárolt eljárás segítségével más felhasználók tulajdonában lévő feladatok az attribútumok szerkesztése.

#### <a name="examples"></a>Példák
A következő példa az adatbázisokat a londoni és NewYork kiszolgálókon a csoporthoz hozzáadott kiszolgálók karbantartásának ügyfél adatait. A feladatok adatbázis létrehozásakor adni a feladat ügynök ezen eset ElasticJobs kell kapcsolódnia.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Feladat nézetek

Az alábbi táblázatban felsorolt nézetek érhetők el a a [feladatok adatbázis](elastic-jobs-overview.md#job-database).


|Nézet  |Leírás  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Sikertelen feladat-végrehajtási előzményei mutat be.      |
|[Feladatok](#jobs-view)     |   Az összes feladat megjelenik.      |
|[job_versions](#jobversions-view)     |   Az összes feladat verzió jeleníti meg.      |
|[feladatlépésnél](#jobsteps-view)     |     Minden lépéseit mutatja be, minden egyes feladat aktuális verziójában.    |
|[jobstep_versions](#jobstepversions-view)     |     Minden lépéseit mutatja be, minden egyes feladat összes verzióján.    |
|[target_groups](#targetgroups-view)     |      Minden célcsoportok jeleníti meg.   |
|[target_group_members](#targetgroups-view)     |   Minden cél csoport minden tagját jeleníti meg.      |


### <a name="jobsexecutions-view"></a>jobs_executions megtekintése

[feladatok]. [jobs_executions]

Sikertelen feladat-végrehajtási előzményei mutat be.


|Oszlop neve|   Adattípus   |Leírás|
|---------|---------|---------|
|**job_execution_id**   |egyedi azonosító|  A feladat-végrehajtás egy példánya egyedi azonosítója.
|**job_name**   |nvarchar(128)  |A feladat nevét.
|**job_id** |egyedi azonosító|  A feladat egyedi Azonosítóját.
|**job_version**    |int    |A feladat (automatikusan frissül a feladat módosítása) verziója.
|**step_id**    |int|   A lépés (meg a feladat) egyedi azonosítója. NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**is_active**| bit |Azt jelzi, hogy adatokat az aktív vagy inaktív-e. 1 aktív feladatok azt jelzi, és a 0 azt jelzi, inaktív.
|**Életciklusa**| nvarchar(50)|A feladat állapotát jelző érték: "Létrehozott", "Folyamatban", "Sikertelen", "Sikeres", "Kihagyva", "SucceededWithSkipped"|
|**create_time**|   datetime2(7)|   A feladat létrehozásának dátuma és időpontja.
|**start_time** |datetime2(7)|  Dátum és idő, a feladat a végrehajtás elindítása. NULL, ha a feladat még nem lett végrehajtva.
|**end_time**|  datetime2(7)    |Dátum és idő, a feladat végrehajtása befejeződött. NULL értékű, ha a feladat még nem került végrehajtásra, vagy még nem fejeződött be a végrehajtása.
|**current_attempts**   |int    |A lépés lett újrapróbált ennyiszer. Szülő feladatukhoz 0 lesz, gyermek feladat végrehajtások 1 vagy nagyobb alapján a végrehajtási házirendet.
|**current_attempt_start_time** |datetime2(7)|  Dátum és idő, a feladat a végrehajtás elindítása. NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**last_message**   |típus: nvarchar(max)| Feladat vagy lépés előzmények üzenet. 
|**target_type**|   nvarchar(128)   |A céladatbázis vagy -adatbázisok, beleértve az összes adatbázis egy kiszolgáló, a rugalmas készletekben található összes adatbázis vagy egy adatbázis gyűjtemény típusa. Target_type érvényes értékei a "SqlServer", "SqlElasticPool" vagy "SqlDatabase". NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_id**  |egyedi azonosító|  A cél csoporttag egyedi azonosítója.  NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_group_name**  |nvarchar(128)  |A célcsoport neve. NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_server_name**|    nvarchar(256)|  A célként megadott csoportban lévő logikai kiszolgáló neve. Csak ha megadása target_type "SqlServer". NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.
|**target_database_name**   |nvarchar(128)| A célként megadott csoportban lévő adatbázis neve. Határozni, csak ha target_type "SqlDatabase". NULL azt jelzi, hogy ez az a szülő feladat végrehajtása.


### <a name="jobs-view"></a>feladatok megtekintése

[feladatok]. [feladatok]

Az összes feladat megjelenik.

|Oszlop neve|   Adattípus|  Leírás|
|------|------|-------|
|**job_name**|  nvarchar(128)   |A feladat nevét.|
|**job_id**|    egyedi azonosító    |A feladat egyedi Azonosítóját.|
|**job_version**    |int    |A feladat (automatikusan frissül a feladat módosítása) verziója.|
|**description**    |nvarchar(512)| A feladat leírása. engedélyezett bit azt jelzi, hogy a feladat engedélyezve van vagy le van tiltva. 1 engedélyezett feladatok azt jelzi, és a 0 azt jelzi, letiltott feladatok.|
|**schedule_interval_type** |nvarchar(50)   |Azt jelzi, hogy ha a feladat futtatását érték: az "egyszeri", "Perc", "Óra", "Nap", "hét", "Hónap"
|**schedule_interval_count**|   int|    A feladat minden egyes végrehajtása közti schedule_interval_type időszakok számát.|
|**schedule_start_time**    |datetime2(7)|  Dátuma és időpontja a feladat a végrehajtás utolsó megkezdődött.|
|**schedule_end_time**| datetime2(7)|   Dátum és idő, a feladat lett az utolsó művelet befejeződött.|


### <a name="jobversions-view"></a>job_versions megtekintése

[feladatok]. [job_verions]

Az összes feladat verzió jeleníti meg.

|Oszlop neve|   Adattípus|  Leírás|
|------|------|-------|
|**job_name**|  nvarchar(128)   |A feladat nevét.|
|**job_id**|    egyedi azonosító    |A feladat egyedi Azonosítóját.|
|**job_version**    |int    |A feladat (automatikusan frissül a feladat módosítása) verziója.|


### <a name="jobsteps-view"></a>feladatlépésnél megtekintése

[feladatok]. [feladatlépésnél]

Minden lépéseit mutatja be, minden egyes feladat aktuális verziójában.

|Oszlop neve    |Adattípus| Leírás|
|------|------|-------|
|**job_name**   |nvarchar(128)| A feladat nevét.|
|**job_id** |egyedi azonosító   |A feladat egyedi Azonosítóját.|
|**job_version**|   int|    A feladat (automatikusan frissül a feladat módosítása) verziója.|
|**step_id**    |int    |A lépés (meg a feladat) egyedi azonosítója.|
|**step_name**  |nvarchar(128)  |A lépés (meg a feladat) egyedi neve.|
|**command_type**   |nvarchar(50)   |A feladat lépésben végrehajtandó parancs típusa. 1-es verzió, az értéknek meg kell egyeznie a és az alapértelmezett érték a "TSql".|
|**command_source** |nvarchar(50)|  A parancs helye. A 1-es "Beágyazott" az alapértelmezett, és csak elfogadható érték.|
|**a parancs**|   típus: nvarchar(max)|  A parancsok a rugalmas feladatok command_type keresztül hajtja végre.|
|**credential_name**|   nvarchar(128)   |Az adatbázishoz kötődő hitelesítő adat nevét a végrehajtás során használja a feladat.|
|**target_group_name**| nvarchar(128)   |A célcsoport neve.|
|**target_group_id**|   egyedi azonosító|   A cél csoport egyedi azonosítója.|
|**initial_retry_interval_seconds**|    int |A késleltetés az első újrapróbálkozási kísérlet előtt. Alapértelmezett értéke 1.|
|**maximum_retry_interval_seconds** |int|   A maximális késleltetés, újrapróbálkozások között. Ha az újrapróbálkozások közötti késleltetés nagyobb, mint ez az érték akkor növekszik, akkor tárfiókonként erre az értékre helyette. Az alapérték 120.|
|**retry_interval_backoff_multiplier**  |valós|  A többszöröző alkalmazandó az újrapróbálkozási késleltetést, ha több feladat lépés végrehajtása sikertelen kísérlet után. Alapértelmezett érték: 2.0-s.|
|**retry_attempts** |int|   Az újrapróbálkozások számát próbálja meg használni, ha ez a lépés sikertelen lesz. Alapértelmezett 10, amely megadja, hogy nincs újrapróbálkozási kísérletek.|
|**step_timeout_seconds**   |int|   Az újrapróbálkozási kísérletek közötti időtartam. Az alapértelmezett érték 0, amely megadja, hogy egy 0 percnél időszakban.|
|**output_type**    |nvarchar(11)|  A parancs helye. Az aktuális előzetes "Beágyazott" az alapértelmezett, és csak elfogadható érték.|
|**output_credential_name**|    nvarchar(128)   |Állítsa be a hitelesítő adatokat, amelyek csatlakozni az eredményeket fogja tárolni a célkiszolgáló nevét.|
|**output_subscription_id**|    egyedi azonosító|   Az előfizetést, a cél server\database a eredmény elérése érdekében állítson be úgy a lekérdezés végrehajtása a egyedi azonosítója.|
|**output_resource_group_name** |nvarchar(128)| Ahol a célkiszolgálón található az erőforráscsoport neve.|
|**output_server_name**|    nvarchar(256)   |Állítsa az eredményeket a célkiszolgáló nevét.|
|**output_database_name**   |nvarchar(128)| Állítsa be az eredmények a célként megadott adatbázis nevét.|
|**output_schema_name** |típus: nvarchar(max)| A cél séma neve. Az alapértelmezett érték dbo, ha nincs megadva.|
|**output_table_name**| típus: nvarchar(max)|  Állítsa be a lekérdezési eredmények az eredményeket fogja tárolni a tábla neve. Tábla a séma állítja be, ha még nem létezik az eredmények alapján automatikusan jön létre. Séma meg kell egyeznie az eredmények közül a séma.|
|**max_parallelism**|   int|    A feladat lépésének fog futni a egyszerre rugalmas készletenként adatbázisok maximális száma. Az alapértelmezett érték NULL, ami azt jelenti, nincs korlátozás. |


### <a name="jobstepversions-view"></a>jobstep_versions megtekintése

[feladatok]. [jobstep_versions]

Minden lépéseit mutatja be, minden egyes feladat összes verzióján. A séma megegyezik [feladatlépésnél](#jobsteps-view).

### <a name="targetgroups-view"></a>target_groups megtekintése

[feladatok]. [target_groups]

Az összes cél csoport listája.

|Oszlop neve|Adattípus| Leírás|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |A célcsoportot, az adatbázisok gyűjtemény neve. 
|**target_group_id**    |egyedi azonosító   |A cél csoport egyedi azonosítója.

### <a name="targetgroupsmembers-view"></a>target_groups_members megtekintése

[feladatok]. [target_groups_members]

Minden cél csoport minden tagját jeleníti meg.

|Oszlop neve|Adattípus| Leírás|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|A célcsoportot, az adatbázisok gyűjtemény neve. |
|**target_group_id**    |egyedi azonosító   |A cél csoport egyedi azonosítója.|
|**membership_type**    |int|   Itt adhatja meg, ha a célként megadott csoport tagja van, illetve tiltani szeretné a célcsoportban. Target_group_name érvényes értékei a "Include" vagy "Kizárása".|
|**target_type**    |nvarchar(128)| A céladatbázis vagy -adatbázisok, beleértve az összes adatbázis egy kiszolgáló, a rugalmas készletekben található összes adatbázis vagy egy adatbázis gyűjtemény típusa. Target_type érvényes értékei a "SqlServer", "SqlElasticPool", "SqlDatabase" vagy "SqlShardMap".|
|**target_id**  |egyedi azonosító|  A cél csoporttag egyedi azonosítója.|
|**refresh_credential_name**    |nvarchar(128)  |Az adatbázis nevét a cél csoporttag való kapcsolódáshoz használt hitelesítő adatok hatókörét.|
|**subscription_id**    |egyedi azonosító|  Az előfizetés egyedi azonosítója.|
|**resource_group_name**    |nvarchar(128)| Az erőforráscsoport, ahol a cél csoporttag van neve.|
|**kiszolgálónév**    |nvarchar(128)  |A célként megadott csoportban lévő logikai kiszolgáló neve. Csak ha megadása target_type "SqlServer". |
|**adatbázisnév**  |nvarchar(128)  |A célként megadott csoportban lévő adatbázis neve. Határozni, csak ha target_type "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| A célként megadott csoportban lévő a rugalmas készlet neve. Határozni, csak ha target_type "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| A szilánkok leképezés a célként megadott csoportban lévő neve. Határozni, csak ha target_type "SqlShardMap".|


## <a name="resources"></a>További források

 - ![A témakör kapcsolat ikon](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "témakör kapcsolat ikon") [Transact-SQL-szintaxis konvenciók](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  


## <a name="next-steps"></a>További lépések

- [PowerShell-lel rugalmas feladatok létrehozásához és kezeléséhez](elastic-jobs-powershell.md)
- [Engedélyezési és engedélyeket az SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  