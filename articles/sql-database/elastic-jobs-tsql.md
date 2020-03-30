---
title: Rugalmas adatbázis-feladatok létrehozása és kezelése transact-SQL (T-SQL) segítségével
description: Parancsfájlok futtatása számos adatbázis rugalmas adatbázis-feladat ügynök transact-SQL (T-SQL) használatával parancsfájlok futtatásához.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: c228f3d6591cd72845101c00188f3fc4a55be644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087351"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Rugalmas adatbázis-feladatok létrehozása és kezelése a Transact-SQL (T-SQL) segítségével

Ez a cikk számos példa forgatókönyvek a T-SQL rugalmas feladatok használatával kapcsolatos munka megkezdéséhez.

A példák a [*feladatadatbázisban*](sql-database-job-automation-overview.md#job-database)elérhető [tárolt eljárásokat](#job-stored-procedures) és [nézeteket](#job-views) használják.

A Transact-SQL (T-SQL) feladatok létrehozására, konfigurálására, végrehajtására és kezelésére szolgál. A rugalmas feladat ügynök létrehozása nem támogatott a T-SQL, ezért először létre kell *hoznia* egy rugalmas feladat ügynök a portál vagy a [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent)használatával.


## <a name="create-a-credential-for-job-execution"></a>Hitelesítő adatok létrehozása a feladat végrehajtásához

A hitelesítő adatok segítségével csatlakozhat a céladatbázisokhoz parancsfájl-végrehajtáshoz. A hitelesítő adatoknak a parancsfájl sikeres végrehajtásához a célcsoport által megadott adatbázisokhoz megfelelő engedélyekre van szüksége. Kiszolgáló- és/vagy készletcélcsoport-tag használata esetén erősen ajánlott létrehozni egy fő hitelesítő adatokat a hitelesítő adatok frissítéséhez a kiszolgáló és/vagy készlet bővítése előtt a feladat végrehajtásakor. Az adatbázis hatókörrel rendelkezett hitelesítő adatai a feladatügynök-adatbázisban jönnek létre. Ugyanezzel a hitelesítő adatkal *kell létrehozni egy bejelentkezést,* és *hozzon létre egy felhasználót a bejelentkezésből, hogy megadja a bejelentkezési adatbázis engedélyeit* a céladatbázisokhoz.


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

## <a name="create-a-target-group-servers"></a>Célcsoport (kiszolgálók) létrehozása

A következő példa bemutatja, hogyan hajtható végre egy feladat a kiszolgáló összes adatbázisán.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:


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


## <a name="exclude-an-individual-database"></a>Egyéni adatbázis kizárása

A következő példa bemutatja, hogyan hajtható végre egy feladat az SQL Database-kiszolgáló összes adatbázisán, kivéve a *MappingDB*nevű adatbázist.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

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

--Exclude a database target member from the server target group
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


## <a name="create-a-target-group-pools"></a>Célcsoport (készletek) létrehozása

A következő példa bemutatja, hogyan lehet az összes adatbázist egy vagy több rugalmas készletben megcélozni.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

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


## <a name="deploy-new-schema-to-many-databases"></a>Új séma telepítése számos adatbázisba

A következő példa bemutatja, hogyan telepíthet új sémát az összes adatbázisba.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:


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


## <a name="data-collection-using-built-in-parameters"></a>Adatgyűjtés beépített paraméterekkel

Számos adatgyűjtési forgatókönyvben hasznos lehet ezen parancsfájlok futtatását a feladat eredményeinek utólagos feldolgozásához.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Ha például az azonos feladatvégrehajtás összes eredményét össze szeretné csoportosítani, használja a *$(job_execution_id)* parancsot a következő parancs szerint:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Adatbázis teljesítményének monitorozása

A következő példa létrehoz egy új feladatot, amely több adatbázis teljesítményadatait gyűjti.

Alapértelmezés szerint a feladatügynök létrehozza a kimeneti táblát a visszaadott eredmények tárolásához. Ezért a kimeneti hitelesítő adathoz társított egyszerű adatbázisnak `CREATE TABLE` legalább a `ALTER`következő `SELECT` `INSERT`engedélyekkel kell rendelkeznie: az adatbázison , , , a kimeneti `DELETE` táblán vagy annak sémáján, valamint `SELECT` a [sys.indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql) katalógusnézetben.

Ha manuálisan szeretné előre létrehozni a táblát, akkor a következő tulajdonságokkal kell rendelkeznie:
1. Az eredményhalmazhoz megfelelő névvel és adattípusokkal rendelkező oszlopok.
2. További oszlop az egyedi azonosító adattípusával internal_execution_id.
3. A internal_execution_id oszlopban `IX_<TableName>_Internal_Execution_ID` elnevezett nem fürtözött index.
4. Az összes fent felsorolt `CREATE TABLE` engedély, kivéve az adatbázisra vonatkozó engedélyt.

Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsokat:

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
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
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
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Feladatdefiníciók megtekintése

A következő példa bemutatja, hogyan tekintheti meg az aktuális feladatdefiníciókat.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Feladat ad hoc végrehajtásának megkezdése

A következő példa bemutatja, hogyan lehet azonnal elindítani egy feladatot.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

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


## <a name="schedule-execution-of-a-job"></a>Feladat végrehajtásának ütemezése

A következő példa bemutatja, hogyan ütemezheti a feladatot a későbbi végrehajtásra.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Feladat-végrehajtási állapot figyelése

A következő példa bemutatja, hogyan tekintheti meg az összes feladat végrehajtási állapotának részleteit.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Feladat visszavonása

A következő példa bemutatja, hogyan lehet egy feladatot megszakítani.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

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

A következő példa bemutatja, hogyan törölheti a feladatelőzményeket egy adott dátum előtt.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Feladat és az összes feladatelőzmény törlése

A következő példa bemutatja, hogyan lehet törölni egy feladatot és az összes kapcsolódó feladatelőzményeket.  
Csatlakozzon a [*feladatadatbázishoz,*](sql-database-job-automation-overview.md#job-database) és futtassa a következő parancsot:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Feladat által tárolt eljárások

A következő tárolt eljárások találhatók a [feladat-adatbázisban.](sql-database-job-automation-overview.md#job-database)



|Tárolt eljárás  |Leírás  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Új feladatot ad hozzá.    |
|[sp_update_job](#sp_update_job)    |      Meglévő feladat frissítése.   |
|[sp_delete_job](#sp_delete_job)     |      Meglévő feladat törlése.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Lépés hozzáadása a feladathoz.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Egy feladatlépés frissítése.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Feladatlépés törlése.    |
|[sp_start_job](#sp_start_job)    |  Megkezdi egy feladat végrehajtását.       |
|[sp_stop_job](#sp_stop_job)     |     Leállítja a feladat végrehajtását.   |
|[sp_add_target_group](#sp_add_target_group)    |     Célcsoportot ad hozzá.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Célcsoport törlése.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Adatbázist vagy adatbáziscsoportot ad hozzá egy célcsoporthoz.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     A célcsoport egy tagjának eltávolítása a célcsoportból.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Eltávolítja egy feladat előzményrekordjait.     |





### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Új feladatot ad hozzá. 
  
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

[ ** \@job_name =** ] "job_name"  
A feladat neve. A névnek egyedinek kell lennie, és nem tartalmazhat százalékot (%) Karakter. job_name nvarchar(128), alapértelmezés nélkül.

[ ** \@leírás =** ] "leírás"  
A feladat leírása. leírás: nvarchar(512), az alapértelmezett ÉRTÉKE NULL. Ha a megnevezés nincs megadva, a program üres karakterláncot használ.

[ ** \@engedélyezve =** ] engedélyezve  
Azt jelzi, hogy a feladat ütemezése engedélyezve van-e. Engedélyezve bit, az alapértelmezett 0 (letiltva). Ha 0, a feladat nincs engedélyezve, és nem fut az ütemezés szerint; azonban manuálisan is futtatható. Ha 1, a feladat az ütemezés szerint fog futni, és manuálisan is futtatható.

[ ** \@schedule_interval_type =**] schedule_interval_type  
Az érték azt jelzi, hogy mikor hajtandó a feladat. schedule_interval_type nvarchar(50), az Egyszer alapértelmezett értékkel, és a következő értékek egyike lehet:
- "Egyszer",
- "Perc",
- "Óra",
- "Napok",
- "Hetek",
- "Hónapok"

[ ** \@schedule_interval_count =** ] schedule_interval_count  
A feladat egyes végrehajtásai között előforduló schedule_interval_count időszakok száma. schedule_interval_count int, az alapértelmezett 1. Az értéknek legfeljebb 1-nek kell lennie.

[ ** \@schedule_start_time =** ] schedule_start_time  
Az a dátum, amelyen a feladat végrehajtása megkezdődhet. schedule_start_time DATETIME2, az alapértelmezett 0001-01-00:00:00.0000000.

[ ** \@schedule_end_time =** ] schedule_end_time  
Az a dátum, amikor a feladat végrehajtása leállhat. schedule_end_time DATETIME2, az alapértelmezett érték 9999-12-31 11:59:59.000000. 

[ ** \@job_id =** ] job_id OUTPUT  
A feladathoz rendelt feladatazonosító szám, ha a létrehozás sikeres. job_id egyedi azonosító típusú kimeneti változó.

#### <a name="return-code-values"></a>Visszatérési kód értékei

0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
sp_add_job a feladatügynök létrehozásakor megadott feladatügynök-adatbázisból kell futtatni.
Miután sp_add_job végrehajtása egy feladat hozzáadásához történt, sp_add_jobstep a feladat tevékenységeit végző lépések hozzáadására használható. A feladat kezdeti verziószáma 0, amely az első lépés hozzáadásakor 1-re lesz növelve.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:

- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Meglévő feladat frissítése.

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
[ ** \@job_name =** ] "job_name"  
A frissítendő feladat neve. job_name nvarchar(128).

[ ** \@new_name =** ] "new_name"  
A feladat új neve. new_name nvarchar(128).

[ ** \@leírás =** ] "leírás"  
A feladat leírása. leírás: nvarchar(512).

[ ** \@engedélyezve =** ] engedélyezve  
Itt adható meg, hogy a feladat ütemezése engedélyezve van-e (1) vagy nincs-e engedélyezve (0). Engedélyezve van a bit.

[ ** \@schedule_interval_type=** ] schedule_interval_type  
Az érték azt jelzi, hogy mikor hajtandó a feladat. schedule_interval_type nvarchar(50), és a következő értékek egyike lehet:

- "Egyszer",
- "Perc",
- "Óra",
- "Napok",
- "Hetek",
- "Hónapok"

** \@schedule_interval_count** schedule_interval_count  
A feladat egyes végrehajtásai között előforduló schedule_interval_count időszakok száma. schedule_interval_count int, az alapértelmezett 1. Az értéknek legfeljebb 1-nek kell lennie.

** \@schedule_start_time** schedule_start_time  
Az a dátum, amelyen a feladat végrehajtása megkezdődhet. schedule_start_time DATETIME2, az alapértelmezett 0001-01-00:00:00.0000000.

schedule_end_time ** \@schedule_end_time schedule_end_time**  
Az a dátum, amikor a feladat végrehajtása leállhat. schedule_end_time DATETIME2, az alapértelmezett érték 9999-12-31 11:59:59.000000. 

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
Miután sp_add_job végrehajtása egy feladat hozzáadásához történt, sp_add_jobstep a feladat tevékenységeit végző lépések hozzáadására használható. A feladat kezdeti verziószáma 0, amely az első lépés hozzáadásakor 1-re lesz növelve.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.



### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Meglévő feladat törlése.

#### <a name="syntax"></a>Szintaxis

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentumok
[ ** \@job_name =** ] "job_name"  
A törölendő feladat neve. job_name nvarchar(128).

[ ** \@erő =** ] erő  
Itt adható meg, hogy törölni kell-e a műveletet, ha a feladat folyamatban van,és megszakítja az összes folyamatban lévő végrehajtást (1), vagy sikertelen, ha egy feladat végrehajtása folyamatban van (0). az erő bit.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
A feladat előzményei automatikusan törlődnek a feladat törlésekor.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.



### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Lépés hozzáadása a feladathoz.

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

[ ** \@job_name =** ] "job_name"  
Annak a feladatnak a neve, amelyhez hozzá szeretné adni a lépést. job_name nvarchar(128).

[ ** \@step_id =** ] step_id  
A feladatlépés szekvenciaazonosító száma. A lépésazonosító számok 1-nél kezdődnek, és hézagok nélkül növeksznek. Ha egy meglévő lépés már rendelkezik ezzel az azonosítóval, akkor ez a lépés és az összes következő lépés az azonosítón növekszik, így ez az új lépés beilleszthető a sorozatba. Ha nincs megadva, a step_id automatikusan hozzáleszrendelve az utolsóhoz a lépések sorrendjében. step_id egy int.

[ ** \@step_name =** ] step_name  
A lépés neve. Meg kell adni, kivéve a feladat első lépését, amelynek alapértelmezett neve "JobStep". step_name nvarchar(128).

[ ** \@command_type =** ] "command_type"  
A feladatlépés által végrehajtott parancs típusa. command_type nvarchar(50), a TSql alapértelmezett értékével, ami @command_type azt jelenti, hogy a paraméter értéke T-SQL parancsfájl.

Ha meg van adva, az értéknek TSql-nek kell lennie.

[ ** \@command_source =** ] "command_source"  
A parancs tárolási helye. command_source nvarchar(50), az alapértelmezett értéke Inline, ami azt @command_source jelenti, hogy a paraméter értéke a parancs literális szövege.

Ha meg van adva, az értéknek Szövegközinek kell lennie.

[ ** \@parancs =** ] "parancs"  
A parancsnak érvényes T-SQL parancsfájlnak kell lennie, majd ez a feladatlépés végrehajtja. a parancs nvarchar(max), az alapértelmezett értéke NULL.

/ ** \@credential_name =** ] "credential_name"  
A feladatvezérlő adatbázisban tárolt adatbázis hatókörrel végzett hitelesítő adatainak neve, amely a lépés végrehajtásakor a célcsoporton belüli céladatbázisokhoz való kapcsolódásra szolgál. credential_name nvarchar(128).

[ ** \@target_group_name =** ] "cél-group_name"  
Annak a célcsoportnak a neve, amely azt a céladatbázist tartalmazza, amelyen a feladatlépés végrehajtásra kerül. target_group_name nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
A késleltetés az első újrapróbálkozási kísérlet előtt, ha a feladatlépés sikertelen lesz az első végrehajtási kísérletnél. initial_retry_interval_seconds int, az alapértelmezett értéke 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Az újrapróbálkozások közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb lesz, mint ez az érték, akkor ez az érték lesz korlátozva. maximum_retry_interval_seconds int, az alapértelmezett értéke 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Az újrapróbálkozási késleltetésre alkalmazandó szorzó, ha több feladatlépés-végrehajtási kísérlet sikertelen. Ha például az első újrapróbálkozás késleltetése 5 másodperc volt, a visszarúgási szorzó pedig 2,0, akkor a második újrapróbálkozás 10 másodperces, a harmadik újrapróbálkozás pedig 20 másodperces késleltetéssel rendelkezik. retry_interval_backoff_multiplier valós, az alapértelmezett értéke 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
A végrehajtás újrapróbálkozási ideje, ha a kezdeti kísérlet sikertelen. Ha például a retry_attempts érték 10, akkor 1 kezdeti és 10 újrapróbálkozási kísérlet lesz, összesen 11 kísérlet. Ha a végső újrapróbálkozási kísérlet sikertelen, majd a feladat végrehajtása sikertelen életciklussal fejeződik be. retry_attempts int, az alapértelmezett értéke 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
A lépés végrehajtásához legfeljebb engedélyezett idő. Ha ezt az időt túllépi, akkor a feladat végrehajtása a TimedOut életciklusával fejeződik be. step_timeout_seconds int, az alapértelmezett értéke 43 200 másodperc (12 óra).

[ ** \@output_type =** ] "output_type"  
Ha nem null, akkor az a céltípus, amelybe a parancs első eredményhalmaza be van írva. output_type nvarchar(50), az alapértelmezett ÉRTÉKE NULL.

Ha meg van adva, az értéknek SqlDatabase-nek kell lennie.

/ ** \@output_credential_name =** ] "output_credential_name"  
Ha nem null, akkor a kimeneti céladatbázishoz való csatlakozáshoz használt adatbázis hatókörrel rendelkező hitelesítő adatainak neve. Meg kell adni, ha output_type egyenlő SqlDatabase. output_credential_name nvarchar(128), az alapértelmezett értéke NULL.

[ ** \@output_subscription_id =** ] "output_subscription_id"  
Leírásra van szüksége.

[ ** \@output_resource_group_name =** ] "output_resource_group_name"  
Leírásra van szüksége.

[ ** \@output_server_name =** ] "output_server_name"  
Ha nem null, a kimeneti céladatbázist tartalmazó kiszolgáló teljesen minősített DNS-neve. Meg kell adni, ha output_type egyenlő SqlDatabase. output_server_name nvarchar(256), az alapértelmezett ÉRTÉKE NULL.

[ ** \@output_database_name =** ] "output_database_name"  
Ha nem null, a kimeneti céltáblát tartalmazó adatbázis neve. Meg kell adni, ha output_type egyenlő SqlDatabase. output_database_name nvarchar(128), az alapértelmezett értéke NULL.

[ ** \@output_schema_name =** ] "output_schema_name"  
Ha nem null, a kimeneti céltáblát tartalmazó SQL-séma neve. Ha output_type sqldatabase, az alapértelmezett érték dbo. output_schema_name nvarchar(128).

[ ** \@output_table_name =** ] "output_table_name"  
Ha nem null, akkor annak a táblának a neve, amelybe a parancs első eredményhalmaza lesz írva. Ha a tábla még nem létezik, akkor a visszatérési eredményhalmaz sémája alapján jön létre. Meg kell adni, ha output_type egyenlő SqlDatabase. output_table_name nvarchar(128), alapértelmezett értéke NULL.

[ ** \@job_version =** ] job_version OUTPUT  
Kimeneti paraméter, amelyhez hozzá lesz rendelve az új feladat verziószáma. job_version int.

[ ** \@max_parallelism =** ] max_parallelism OUTPUT  
A maximális szint a párhuzamosság rugalmas medence. Ha be van állítva, majd a feladat lépés lesz korlátozva, hogy csak akkor fut, hogy a rugalmas készletenként legfeljebb ennyi adatbázis. Ez minden olyan rugalmas készletre vonatkozik, amely vagy közvetlenül szerepel a célcsoportban, vagy a célcsoportban szereplő kiszolgálón belül van. max_parallelism int.


#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
Ha sp_add_jobstep sikeres, a feladat aktuális verziószáma növekszik. A feladat következő végrehajtásakor a lesz az új verzió. Ha a feladat végrehajtása folyamatban van, a végrehajtás nem fogja tartalmazni az új lépést.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:  

- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.



### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Egy feladatlépés frissítése.

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
[ ** \@job_name =** ] "job_name"  
Annak a feladatnak a neve, amelyhez a lépés tartozik. job_name nvarchar(128).

[ ** \@step_id =** ] step_id  
A módosítandó feladatlépés azonosítószáma. Meg kell adni step_id vagy step_name. step_id egy int.

[ ** \@step_name =** ] "step_name"  
A módosítandó lépés neve. Meg kell adni step_id vagy step_name. step_name nvarchar(128).

[ ** \@new_id =** ] new_id  
A feladatlépés új szekvenciaazonosító száma. A lépésazonosító számok 1-nél kezdődnek, és hézagok nélkül növeksznek. Ha egy lépést átrendez, a többi lépés automatikusan újraszámozásra kerül.

[ ** \@new_name =** ] "new_name"  
A lépés új neve. new_name nvarchar(128).

[ ** \@command_type =** ] "command_type"  
A feladatlépés által végrehajtott parancs típusa. command_type nvarchar(50), a TSql alapértelmezett értékével, ami @command_type azt jelenti, hogy a paraméter értéke T-SQL parancsfájl.

Ha meg van adva, az értéknek TSql-nek kell lennie.

[ ** \@command_source =** ] "command_source"  
A parancs tárolási helye. command_source nvarchar(50), az alapértelmezett értéke Inline, ami azt @command_source jelenti, hogy a paraméter értéke a parancs literális szövege.

Ha meg van adva, az értéknek Szövegközinek kell lennie.

[ ** \@parancs =** ] "parancs"  
A parancs(ok) érvényes T-SQL parancsfájlnak kell lenniük, majd ez a feladatlépés végrehajtja. a parancs nvarchar(max), az alapértelmezett értéke NULL.

/ ** \@credential_name =** ] "credential_name"  
A feladatvezérlő adatbázisban tárolt adatbázis hatókörrel végzett hitelesítő adatainak neve, amely a lépés végrehajtásakor a célcsoporton belüli céladatbázisokhoz való kapcsolódásra szolgál. credential_name nvarchar(128).

[ ** \@target_group_name =** ] "cél-group_name"  
Annak a célcsoportnak a neve, amely azt a céladatbázist tartalmazza, amelyen a feladatlépés végrehajtásra kerül. target_group_name nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
A késleltetés az első újrapróbálkozási kísérlet előtt, ha a feladatlépés sikertelen lesz az első végrehajtási kísérletnél. initial_retry_interval_seconds int, az alapértelmezett értéke 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Az újrapróbálkozások közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb lesz, mint ez az érték, akkor ez az érték lesz korlátozva. maximum_retry_interval_seconds int, az alapértelmezett értéke 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Az újrapróbálkozási késleltetésre alkalmazandó szorzó, ha több feladatlépés-végrehajtási kísérlet sikertelen. Ha például az első újrapróbálkozás késleltetése 5 másodperc volt, a visszarúgási szorzó pedig 2,0, akkor a második újrapróbálkozás 10 másodperces, a harmadik újrapróbálkozás pedig 20 másodperces késleltetéssel rendelkezik. retry_interval_backoff_multiplier valós, az alapértelmezett értéke 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
A végrehajtás újrapróbálkozási ideje, ha a kezdeti kísérlet sikertelen. Ha például a retry_attempts érték 10, akkor 1 kezdeti és 10 újrapróbálkozási kísérlet lesz, összesen 11 kísérlet. Ha a végső újrapróbálkozási kísérlet sikertelen, majd a feladat végrehajtása sikertelen életciklussal fejeződik be. retry_attempts int, az alapértelmezett értéke 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
A lépés végrehajtásához legfeljebb engedélyezett idő. Ha ezt az időt túllépi, akkor a feladat végrehajtása a TimedOut életciklusával fejeződik be. step_timeout_seconds int, az alapértelmezett értéke 43 200 másodperc (12 óra).

[ ** \@output_type =** ] "output_type"  
Ha nem null, akkor az a céltípus, amelybe a parancs első eredményhalmaza be van írva. A output_type érték null értékre való visszaállításához állítsa a paraméter értékét '' (üres karakterlánc) értékre. output_type nvarchar(50), az alapértelmezett ÉRTÉKE NULL.

Ha meg van adva, az értéknek SqlDatabase-nek kell lennie.

/ ** \@output_credential_name =** ] "output_credential_name"  
Ha nem null, akkor a kimeneti céladatbázishoz való csatlakozáshoz használt adatbázis hatókörrel rendelkező hitelesítő adatainak neve. Meg kell adni, ha output_type egyenlő SqlDatabase. Ha vissza szeretné állítani output_credential_name értékét NULL értékre, állítsa a paraméter értékét '' (üres karakterlánc) értékre. output_credential_name nvarchar(128), az alapértelmezett értéke NULL.

[ ** \@output_server_name =** ] "output_server_name"  
Ha nem null, a kimeneti céladatbázist tartalmazó kiszolgáló teljesen minősített DNS-neve. Meg kell adni, ha output_type egyenlő SqlDatabase. A output_server_name érték null értékre való visszaállításához állítsa a paraméter értékét '' (üres karakterlánc) értékre. output_server_name nvarchar(256), az alapértelmezett ÉRTÉKE NULL.

[ ** \@output_database_name =** ] "output_database_name"  
Ha nem null, a kimeneti céltáblát tartalmazó adatbázis neve. Meg kell adni, ha output_type egyenlő SqlDatabase. Ha vissza szeretné állítani output_database_name értékét NULL értékre, állítsa a paraméter értékét '' (üres karakterlánc) értékre. output_database_name nvarchar(128), az alapértelmezett értéke NULL.

[ ** \@output_schema_name =** ] "output_schema_name"  
Ha nem null, a kimeneti céltáblát tartalmazó SQL-séma neve. Ha output_type sqldatabase, az alapértelmezett érték dbo. Ha vissza szeretné állítani a output_schema_name értékét NULL értékre, állítsa a paraméter értékét '' (üres karakterlánc) értékre. output_schema_name nvarchar(128).

[ ** \@output_table_name =** ] "output_table_name"  
Ha nem null, akkor annak a táblának a neve, amelybe a parancs első eredményhalmaza lesz írva. Ha a tábla még nem létezik, akkor a visszatérési eredményhalmaz sémája alapján jön létre. Meg kell adni, ha output_type egyenlő SqlDatabase. A output_server_name érték null értékre való visszaállításához állítsa a paraméter értékét '' (üres karakterlánc) értékre. output_table_name nvarchar(128), alapértelmezett értéke NULL.

[ ** \@job_version =** ] job_version OUTPUT  
Kimeneti paraméter, amelyhez hozzá lesz rendelve az új feladat verziószáma. job_version int.

[ ** \@max_parallelism =** ] max_parallelism OUTPUT  
A maximális szint a párhuzamosság rugalmas medence. Ha be van állítva, majd a feladat lépés lesz korlátozva, hogy csak akkor fut, hogy a rugalmas készletenként legfeljebb ennyi adatbázis. Ez minden olyan rugalmas készletre vonatkozik, amely vagy közvetlenül szerepel a célcsoportban, vagy a célcsoportban szereplő kiszolgálón belül van. Ha vissza szeretné állítani a max_parallelism értékét null értékre, állítsa a paraméter értékét -1 értékre. max_parallelism int.


#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
A feladat folyamatban lévő végrehajtásait ez nem érinti. Ha sp_update_jobstep sikeres, a feladat verziószáma növekszik. A feladat következő végrehajtásakor a lesz az új verzió.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:

- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.




### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Egy feladatlépés eltávolítása a feladatból.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok
[ ** \@job_name =** ] "job_name"  
Annak a feladatnak a neve, amelyből a lépés eltávolításra kerül. job_name nvarchar(128), alapértelmezés nélkül.

[ ** \@step_id =** ] step_id  
A törölendő feladatlépés azonosítószáma. Meg kell adni step_id vagy step_name. step_id egy int.

[ ** \@step_name =** ] "step_name"  
A törölendő lépés neve. Meg kell adni step_id vagy step_name. step_name nvarchar(128).

[ ** \@job_version =** ] job_version OUTPUT  
Kimeneti paraméter, amelyhez hozzá lesz rendelve az új feladat verziószáma. job_version int.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
A feladat folyamatban lévő végrehajtásait ez nem érinti. Ha sp_update_jobstep sikeres, a feladat verziószáma növekszik. A feladat következő végrehajtásakor a lesz az új verzió.

A többi feladatlépés automatikusan újraszámozásra kerül, hogy kitöltse a törölt feladatlépés által hagyott hiányt.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.






### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Megkezdi egy feladat végrehajtását.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumentumok
[ ** \@job_name =** ] "job_name"  
Annak a feladatnak a neve, amelyből a lépés eltávolításra kerül. job_name nvarchar(128), alapértelmezés nélkül.

[ ** \@job_execution_id =** ] job_execution_id KIMENET  
Kimeneti paraméter, amely hozzá lesz rendelve a feladat-végrehajtási azonosítóját. job_version egyedi azonosító.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
Nincs.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Leállítja a feladat végrehajtását.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumentumok
[ ** \@job_execution_id =** ] job_execution_id  
A leállítandó feladat végrehajtásának azonosító száma. job_execution_id egyedi azonosító, az alapértelmezett NULL.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
Nincs.
 
#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.


### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Célcsoportot ad hozzá.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumentumok
[ ** \@target_group_name =** ] "target_group_name"  
A létrehozandó célcsoport neve. target_group_name nvarchar(128), alapértelmezés nélkül.

[ ** \@target_group_id =** ] target_group_id OUTPUT A feladathoz rendelt célcsoport azonosítószáma, ha sikeresen létrehozva van. target_group_id egyedi azonosító típusú kimeneti változó, értéke NULL.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
A célcsoportok segítségével egyszerűen célozhatja meg a feladatot adatbázisok gyűjteményében.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Célcsoport törlése.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumentumok
[ ** \@target_group_name =** ] "target_group_name"  
A törlendő célcsoport neve. target_group_name nvarchar(128), alapértelmezés nélkül.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
Nincs.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Adatbázist vagy adatbáziscsoportot ad hozzá egy célcsoporthoz.

#### <a name="syntax"></a>Szintaxis

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumentumok
[ ** \@target_group_name =** ] "target_group_name"  
Annak a célcsoportnak a neve, amelyhez a tagot hozzá adja. target_group_name nvarchar(128), alapértelmezés nélkül.

[ ** \@membership_type =** ] "membership_type"  
Itt adható meg, hogy a célcsoport tagja szerepel-e vagy sem. target_group_name nvarchar(128), az alapértelmezett "Include". A target_group_name érvényes értékei a következők: "Belefoglalás" vagy "Kizárás".

[ ** \@target_type =** ] "target_type"  
A céladatbázis vagy az adatbázisok gyűjteménye, beleértve a kiszolgáló összes adatbázisát, az összes rugalmas készletben lévő adatbázist, a szegmenstérkép összes adatbázisát vagy egy különálló adatbázist. target_type nvarchar(128), alapértelmezés nélkül. a target_type érvényes értékei: 'SqlServer', 'SqlElasticPool', 'SqlDatabase', vagy 'SqlShardMap'. 

[ ** \@refresh_credential_name =** ] "refresh_credential_name"  
Az SQL Database-kiszolgáló neve. refresh_credential_name nvarchar(128), alapértelmezés nélkül.

[ ** \@server_name =** ] "server_name"  
A megadott célcsoporthoz hozzáadandó SQL Database-kiszolgáló neve. server_name akkor kell megadni, ha target_type "SqlServer". server_name nvarchar(128), alapértelmezés nélkül.

[ ** \@database_name =** ] "database_name"  
A megadott célcsoporthoz hozzáadandó adatbázis neve. database_name meg kell adni, ha target_type "SqlDatabase". database_name nvarchar(128), alapértelmezés nélkül.

[ ** \@elastic_pool_name =** ] "elastic_pool_name"  
A megadott célcsoporthoz hozzáadandó rugalmas készlet neve. elastic_pool_name akkor kell megadni, ha target_type "SqlElasticPool". elastic_pool_name nvarchar(128), alapértelmezés nélkül.

[ ** \@shard_map_name =** ] "shard_map_name"  
A shard térképkészlet neve, amelyet hozzá kell adni a megadott célcsoporthoz. elastic_pool_name akkor kell megadni, ha target_type "SqlSqlShardMap". shard_map_name nvarchar(128), alapértelmezés nélkül.

[ ** \@target_id =** ] target_group_id OUTPUT  
A célcsoport tagjához rendelt célazonosító szám, ha a célcsoporthoz hozzáadva van. target_id egyedi azonosító típusú kimeneti változó, értéke NULL.
Visszatérési kód értéke0 (sikeres) vagy 1 (hiba)

#### <a name="remarks"></a>Megjegyzések
A feladat végrehajtása az SQL Database-kiszolgálón belül vagy egy rugalmas készletben a végrehajtás időpontjában végrehajtott összes adatbázison, ha egy SQL Database-kiszolgáló vagy rugalmas készlet szerepel a célcsoportban.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.

#### <a name="examples"></a>Példák
A következő példa hozzáadja a londoni és a New York-i kiszolgálók összes adatbázisát az ügyféladatokat karbantartó kiszolgálók csoporthoz. A feladatügynök, ebben az esetben elasticJobs létrehozásakor megadott feladat-adatbázishoz kell csatlakoznia.

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

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

A célcsoport egy tagjának eltávolítása a célcsoportból.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argumentumok @target_group_name [ = ] "target_group_name"  
Annak a célcsoportnak a neve, amelyből el szeretné távolítani a célcsoport tagot. target_group_name nvarchar(128), alapértelmezés nélkül.

[ @target_id = ] target_id  
 Az eltávolítandó célcsoport-taghoz rendelt célazonosító szám. target_id egy egyedi azonosító, értéke NULL.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen)

#### <a name="remarks"></a>Megjegyzések
A célcsoportok segítségével egyszerűen célozhatja meg a feladatot adatbázisok gyűjteményében.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.

#### <a name="examples"></a>Példák
A következő példa eltávolítja a londoni kiszolgálót az ügyféladatokat karbantartó kiszolgálók csoportból. A feladatügynök, ebben az esetben elasticJobs létrehozásakor megadott feladat-adatbázishoz kell csatlakoznia.

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

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Eltávolítja egy feladat előzményrekordjait.

#### <a name="syntax"></a>Szintaxis


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentumok
[ ** \@job_name =** ] "job_name"  
Annak a feladatnak a neve, amelynek az előzményrekordjait törölni szeretné. job_name nvarchar(128), az alapértelmezett ÉRTÉKE NULL. Meg kell adni job_id vagy job_name, de mindkettő nem adható meg.

[ ** \@job_id =** ] job_id  
 A törölendő rekordok feladatazonosító száma. job_id egyedi azonosító, értéke NULL. Meg kell adni job_id vagy job_name, de mindkettő nem adható meg.

[ ** \@oldest_date =** ] oldest_date  
 A legrégebbi rekord, amit meg kell őrizni az előzményekben. oldest_date DATETIME2, az alapértelmezett NULL. Ha oldest_date van megadva, sp_purge_jobhistory csak a megadott értéknél régebbi rekordokat távolítja el.

#### <a name="return-code-values"></a>Visszatérési kód értékei
0 (sikeres) vagy 1 (sikertelen) Megjegyzések A célcsoportok egyszerű módot kínálnak egy feladat adatbázisok gyűjteményének célzására.

#### <a name="permissions"></a>Engedélyek
Alapértelmezés szerint a sysadmin fix kiszolgálói szerepkör tagjai végrehajthatják ezt a tárolt eljárást. Korlátozzák a felhasználót, hogy csak képes legyen a feladatok figyelésére, megadhatja a felhasználónak, hogy a következő adatbázis-szerepkör része legyen a feladatügynök létrehozásakor megadott feladatügynök-adatbázisban:
- jobs_reader

A szerepkörök engedélyeinek részleteit a dokumentum Engedély szakasza ismerteti. Csak a rendszergazda tagjai használhatják ezt a tárolt eljárást a más felhasználók tulajdonában lévő feladatok attribútumainak szerkesztésére.

#### <a name="examples"></a>Példák
A következő példa hozzáadja a londoni és a New York-i kiszolgálók összes adatbázisát az ügyféladatokat karbantartó kiszolgálók csoporthoz. A feladatügynök, ebben az esetben elasticJobs létrehozásakor megadott feladat-adatbázishoz kell csatlakoznia.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Feladatnézetek

A következő nézetek érhetők el a [feladat-adatbázisban.](sql-database-job-automation-overview.md#job-database)


|Nézet  |Leírás  |
|---------|---------|
|[job_executions](#job_executions-view)     |  A feladat végrehajtási előzményeit jeleníti meg.      |
|[Munkahelyek](#jobs-view)     |   Az összes feladat megjelenítése.      |
|[job_versions](#job_versions-view)     |   Az összes feladatverzió megjelenítése.      |
|[jobsteps](#jobsteps-view)     |     Az egyes feladatok aktuális verziójának összes lépése.    |
|[jobstep_versions](#jobstep_versions-view)     |     Az egyes feladatverziók összes lépésének megjelenítése.    |
|[target_groups](#target_groups-view)     |      Az összes célcsoport megjelenítése.   |
|[target_group_members](#target_group_members-view)     |   Az összes célcsoport összes tagjának megjelenítése.      |


### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions nézet

[munkahelyek]. Ez az job_executions.

A feladat végrehajtási előzményeit jeleníti meg.


|Oszlop neve|   Adattípus   |Leírás|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Egy feladat-végrehajtás egy példányának egyedi azonosítója.
|**job_name**   |nvarchar(128)  |A feladat neve.
|**job_id** |uniqueidentifier|  A feladat egyedi azonosítója.
|**job_version**    |int    |A feladat verziója (automatikusan frissül minden alkalommal, amikor a feladat módosul).
|**step_id**    |int|   A lépés egyedi azonosítója (ehhez a feladathoz) azonosító. NULL azt jelzi, hogy ez a szülőfeladat végrehajtása.
|**is_active**| bit |Azt jelzi, hogy az információ aktív vagy inaktív-e. Az 1 az aktív feladatokat, a 0 pedig az inaktívt jelöli.
|**Életciklus**| nvarchar (50)|A feladat állapotát jelző érték:'Létrehozva', 'Folyamatban', 'Failed', 'Sikerült', "Sikerült", "Skiped", 'SucceededWithSkiped'|
|**create_time**|   datetime2(7)|   A feladat létrehozásának dátuma és időpontja.
|**start_time** |datetime2(7)|  A feladat végrehajtásának dátuma és időpontja. NULL, ha a feladat még nem lett végrehajtva.
|**end_time**|  datetime2(7)    |A feladat végrehajtásának dátuma és időpontja. NULL, ha a feladat még nem lett végrehajtva, vagy még nem fejeződött be a végrehajtás.
|**current_attempts**   |int    |A lépés újbóli megkísérlésének száma. A szülőfeladat 0 lesz, a gyermekfeladat-végrehajtások a végrehajtási házirend alapján 1 vagy nagyobb lesz.
|**current_attempt_start_time** |datetime2(7)|  A feladat végrehajtásának dátuma és időpontja. NULL azt jelzi, hogy ez a szülőfeladat végrehajtása.
|**last_message**   |nvarchar(max)| Feladat- vagy lépéselőzmények üzenet. 
|**target_type**|   nvarchar(128)   |A céladatbázis vagy az adatbázisok gyűjteménye, beleértve a kiszolgáló összes adatbázisát, az összes rugalmas készletben vagy adatbázisban lévő adatbázisokat. A target_type érvényes értékei: "SqlServer", "SqlElasticPool" vagy "SqlDatabase". NULL azt jelzi, hogy ez a szülőfeladat végrehajtása.
|**target_id**  |uniqueidentifier|  A célcsoport tagjának egyedi azonosítója.  NULL azt jelzi, hogy ez a szülőfeladat végrehajtása.
|**target_group_name**  |nvarchar(128)  |A célcsoport neve. NULL azt jelzi, hogy ez a szülőfeladat végrehajtása.
|**target_server_name**|    nvarchar(256)|  A célcsoportban található SQL Database-kiszolgáló neve. Csak akkor adja meg, ha target_type "SqlServer". NULL azt jelzi, hogy ez a szülőfeladat végrehajtása.
|**target_database_name**   |nvarchar(128)| A célcsoportban lévő adatbázis neve. Csak akkor adja meg, ha target_type "SqlDatabase". NULL azt jelzi, hogy ez a szülőfeladat végrehajtása.


### <a name="jobs-view"></a>feladatok nézet

[munkahelyek]. [munkahelyek]

Az összes feladat megjelenítése.

|Oszlop neve|   Adattípus|  Leírás|
|------|------|-------|
|**job_name**|  nvarchar(128)   |A feladat neve.|
|**job_id**|    uniqueidentifier    |A feladat egyedi azonosítója.|
|**job_version**    |int    |A feladat verziója (automatikusan frissül minden alkalommal, amikor a feladat módosul).|
|**Leírás**    |nvarchar(512)| A feladat leírása. engedélyezett bit: Azt jelzi, hogy a feladat engedélyezve van-e vagy le van tiltva. 1 az engedélyezett feladatokat, a 0 a letiltott feladatokat jelöli.|
|**schedule_interval_type** |nvarchar (50)   |A feladat végrehajtását jelző érték:'Once', 'Perc', 'Hours', 'Days', 'Weeks', 'Months'
|**schedule_interval_count**|   int|    A feladat egyes végrehajtásai között előforduló schedule_interval_type időszakok száma.|
|**schedule_start_time**    |datetime2(7)|  A feladat utolsó végrehajtásának dátuma és időpontja.|
|**schedule_end_time**| datetime2(7)|   A feladat utolsó befejezésének dátuma és időpontja.|


### <a name="job_versions-view"></a><a name="job_versions-view"></a>job_versions nézet

[munkahelyek]. Ez az a job_versions 100 000

Az összes feladatverzió megjelenítése.

|Oszlop neve|   Adattípus|  Leírás|
|------|------|-------|
|**job_name**|  nvarchar(128)   |A feladat neve.|
|**job_id**|    uniqueidentifier    |A feladat egyedi azonosítója.|
|**job_version**    |int    |A feladat verziója (automatikusan frissül minden alkalommal, amikor a feladat módosul).|


### <a name="jobsteps-view"></a>jobsteps nézet

[munkahelyek]. - Nem, nem, nem, nem, nem, nem, nem

Az egyes feladatok aktuális verziójának összes lépése.

|Oszlop neve    |Adattípus| Leírás|
|------|------|-------|
|**job_name**   |nvarchar(128)| A feladat neve.|
|**job_id** |uniqueidentifier   |A feladat egyedi azonosítója.|
|**job_version**|   int|    A feladat verziója (automatikusan frissül minden alkalommal, amikor a feladat módosul).|
|**step_id**    |int    |A lépés egyedi azonosítója (ehhez a feladathoz) azonosító.|
|**step_name**  |nvarchar(128)  |A lépés egyedi neve (ehhez a feladathoz) a lépés neve.|
|**command_type**   |nvarchar (50)   |A feladatlépésben végrehajtandó parancs típusa. A v1 esetében az értéknek meg kell egyeznie a "TSql"-rel, és alapértelmezettértéknek kell lennie.|
|**command_source** |nvarchar (50)|  A parancs helye. A v1 esetében a "Szövegközi" az alapértelmezett érték, és csak elfogadott érték.|
|**Parancs**|   nvarchar(max)|  Az elasztikus feladatok által végrehajtandó parancsok command_type.|
|**credential_name**|   nvarchar(128)   |A feladat végrehajtásához használt adatbázis hatókörrel rendelkező hitelesítő adatának neve.|
|**target_group_name**| nvarchar(128)   |A célcsoport neve.|
|**target_group_id**|   uniqueidentifier|   A célcsoport egyedi azonosítója.|
|**initial_retry_interval_seconds**|    int |Az első újrapróbálkozáselőtti késleltetés. Az alapértelmezett érték 1.|
|**maximum_retry_interval_seconds** |int|   Az újrapróbálkozások közötti maximális késleltetés. Ha az újrapróbálkozások közötti késleltetés nagyobb lesz, mint ez az érték, akkor ez az érték lesz korlátozva. Az alapértelmezett érték 120.|
|**retry_interval_backoff_multiplier**  |real|  Az újrapróbálkozási késleltetésre alkalmazandó szorzó, ha több feladatlépés-végrehajtási kísérlet sikertelen. Az alapértelmezett érték 2.0.|
|**retry_attempts** |int|   A lépés sikertelensítése esetén az újrapróbálkozások száma. 10-es alapértelmezett érték, ami azt jelzi, hogy nincsenek újrapróbálkozási kísérletek.|
|**step_timeout_seconds**   |int|   Az újrapróbálkozások közötti percmennyiség percben. Az alapértelmezett érték 0, ami 0 perces időközt jelöl.|
|**output_type**    |nvarchar [11]|  A parancs helye. Az aktuális előzetes verzióban a "Szövegközi" az alapértelmezett érték, és csak elfogadott érték.|
|**output_credential_name**|    nvarchar(128)   |A célkiszolgálóhoz az eredményhalmaz tárolásához használandó hitelesítő adatok neve.|
|**output_subscription_id**|    uniqueidentifier|   A célkiszolgáló\adatbázis előfizetésének egyedi azonosítója a lekérdezés végrehajtásából származó eredményekhez.|
|**output_resource_group_name** |nvarchar(128)| Erőforráscsoport neve, ahol a célkiszolgáló található.|
|**output_server_name**|    nvarchar(256)   |Az eredményhalmaz célkiszolgálójának neve.|
|**output_database_name**   |nvarchar(128)| Az eredményhalmaz céladatbázisának neve.|
|**output_schema_name** |nvarchar(max)| A célséma neve. Alapértelmezés szerint a dbo, ha nincs megadva.|
|**output_table_name**| nvarchar(max)|  A lekérdezés eredményéből származó eredményhalmaz tárolására váró tábla neve. A tábla automatikusan jön létre az eredményhalmaz sémája alapján, ha még nem létezik. A sémának meg kell egyeznie az eredményhalmaz sémájával.|
|**max_parallelism**|   int|    Rugalmas készletenként az adatbázisok maximális száma, amelyen a feladatlépés egyszerre futni fog. Az alapértelmezett érték NULL, ami azt jelenti, hogy nincs korlátozás. |


### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions nézet

[munkahelyek]. Ez az jobstep_versions.

Az egyes feladatverziók összes lépésének megjelenítése. A séma megegyezik a [feladatlépésekkel.](#jobsteps-view)

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups nézet

[munkahelyek]. Ez az target_groups.

Az összes célcsoport felsorasztása.

|Oszlop neve|Adattípus| Leírás|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |A célcsoport neve, adatbázisok gyűjteménye. 
|**target_group_id**    |uniqueidentifier   |A célcsoport egyedi azonosítója.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members nézet

[munkahelyek]. Ez nem az én target_group_members.

Az összes célcsoport összes tagjának megjelenítése.

|Oszlop neve|Adattípus| Leírás|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|A célcsoport neve, adatbázisok gyűjteménye. |
|**target_group_id**    |uniqueidentifier   |A célcsoport egyedi azonosítója.|
|**membership_type**    |int|   Itt adható meg, hogy a célcsoport tagja szerepel-e vagy sem a célcsoportban. A target_group_name érvényes értékei a következők: "Belefoglalás" vagy "Kizárás".|
|**target_type**    |nvarchar(128)| A céladatbázis vagy az adatbázisok gyűjteménye, beleértve a kiszolgáló összes adatbázisát, az összes rugalmas készletben vagy adatbázisban lévő adatbázisokat. a target_type érvényes értékei: 'SqlServer', 'SqlElasticPool', 'SqlDatabase', vagy 'SqlShardMap'.|
|**target_id**  |uniqueidentifier|  A célcsoport tagjának egyedi azonosítója.|
|**refresh_credential_name**    |nvarchar(128)  |A célcsoport tagjához való csatlakozáshoz használt adatbázishatókör-hitelesítő adatok neve.|
|**subscription_id**    |uniqueidentifier|  Az előfizetés egyedi azonosítója.|
|**resource_group_name**    |nvarchar(128)| Annak az erőforráscsoportnak a neve, amelyben a célcsoport tagja található.|
|**server_name**    |nvarchar(128)  |A célcsoportban található SQL Database-kiszolgáló neve. Csak akkor adja meg, ha target_type "SqlServer". |
|**database_name**  |nvarchar(128)  |A célcsoportban lévő adatbázis neve. Csak akkor adja meg, ha target_type "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| A célcsoportban található rugalmas készlet neve. Csak akkor adja meg, ha target_type "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| A célcsoportban található shard térkép neve. Csak akkor adja meg, ha target_type "SqlShardMap".|


## <a name="resources"></a>Források

 - ![Témakörhivatkozás ikon](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "A Témakör hivatkozásikonja") [Transact-SQL szintaxis konvenciók](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>További lépések

- [Rugalmas feladatok létrehozása és kezelése a PowerShell használatával](elastic-jobs-powershell.md)
- [Engedélyezés és engedélyek SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
