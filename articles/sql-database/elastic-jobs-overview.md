---
title: Elastic Database-kezelési feladatok (előzetes)
description: Configure Elastic Database Jobs (preview) to run Transact-SQL (T-SQL) scripts across a set of one or more Azure SQL databases
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 283b4004f34372104eb083496400772884f5965e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420380"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Create, configure, and manage elastic jobs

In this article, you will learn how to create, configure, and manage elastic jobs.

If you have not used Elastic jobs, [learn more about the job automation concepts in Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Az ügynök létrehozása és konfigurálása

1. Hozzon létre vagy azonosítson egy üres, S0 vagy magasabb szintű SQL-adatbázist. This database will be used as the *Job database* during Elastic Job agent creation.
2. Create an Elastic Job agent in the [portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) or with [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Creating Elastic Job agent](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Feladatok létrehozása, futtatása és kezelése

1. Create a credential for job execution in the *Job database* using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Define the target group (the databases you want to run the job against) using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Hozzon létre feladatügynök-hitelesítő adatokat minden adatbázisban, ahol a feladat futni fog [(adja hozzá a felhasználót (vagy a szerepkört) a csoport minden adatbázisához)](sql-database-control-access.md). Példaként tekintse meg a [PowerShell-oktatóanyagot](elastic-jobs-powershell.md).
4. Create a job using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Adjon hozzá feladatlépéseket a [PowerShell](elastic-jobs-powershell.md) vagy a [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) használatával.
6. Run a job using [PowerShell](elastic-jobs-powershell.md#run-the-job) or [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitor job execution status using the portal, [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) or [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portál](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>A feladatok futtatásához használt hitelesítő adatok

A feladatok [adatbázishoz kötődő hitelesítő adatokat](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) használnak a célcsoport által végrehajtáskor meghatározott adatbázisokhoz történő csatlakozáshoz. Ha egy célcsoport kiszolgálókat vagy készleteket tartalmaz, az adatbázishoz kötődő hitelesítő adatokat a rendszer a master adatbázishoz történő csatlakozáshoz használja az elérhető adatbázisok számbavétele céljából.

Lehetséges, hogy a feladatfuttatáshoz szükséges megfelelő hitelesítő adatok beállítása nem egyértelmű, ezért tartsa szem előtt a következőket:

- Az adatbázishoz kötődő hitelesítő adatokat a *feladat-adatbázisban* kell létrehozni.
- **All target databases must have a login with [sufficient permissions](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) for the job to complete successfully** (`jobuser` in the diagram below).
- Credentials can be reused across jobs, and the credential passwords are encrypted and secured from users who have read-only access to job objects.

Az alábbi kép segítséget nyújt a megfelelő hitelesítő adatok megértéséhez és beállításához. **Ne feledje létrehozni a felhasználót minden olyan adatbázisban (minden *célként megjelölt felhasználói adatbázisban*), ahol a feladatot futtatni szeretné**.

![Rugalmas feladatokhoz tartozó hitelesítő adatok](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Ajánlott biztonsági eljárások

Néhány megfontolandó ajánlott eljárás a rugalmas feladatokkal végzett munkához:

- Az API-k felhasználását korlátozza megbízható személyekre.
- A hitelesítő adatok a feladatlépés végrehajtásához szükséges minimális engedélyekkel rendelkezzenek. For more information, see [Authorization and Permissions SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- When using a server and/or pool target group member, it is highly suggested to create a separate credential with rights on the master database to view/list databases that is used to expand the database lists of the server(s) and/or pool(s) prior to the job execution.

## <a name="agent-performance-capacity-and-limitations"></a>Az ügynök teljesítménye, kapacitása és korlátai

A rugalmas feladatok minimális számítási erőforrást használnak, amíg a hosszan futó feladatok befejezésére várakoznak.

Az adatbázisok célcsoportjának méretétől és az adott feladat kívánt végrehajtási idejétől (az egyidejű feldolgozók számától) függően az ügynök különböző mértékű számítási kapacitást és teljesítményt igényel a *feladat-adatbázis* részéről (több cél és több feladat esetén nagyobb számítási kapacitás szükséges).

Jelenleg az előzetes verzió 100 feladat egyidejű futtatására képes.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>A feladatok a céladatbázis teljesítményét csökkentő hatásának megakadályozása

Ha szeretné elkerülni, hogy az erőforrások túlterheltek legyenek egy rugalmas SQL-készlet adatbázisain történő feladatvégrehajtás közben, akkor a feladatok esetén konfigurálhatja, hogy azok egyszerre legfeljebb hány adatbázison futhatnak.

Set the number of concurrent databases a job runs on by setting the `sp_add_jobstep` stored procedure's `@max_parallelism` parameter in T-SQL, or `Add-AzSqlElasticJobStep -MaxParallelism` in PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Ajánlott eljárások feladatok létrehozásához

### <a name="idempotent-scripts"></a>Idempotens szkriptek
A feladatok T-SQL-szkriptjeinek [idempotensnek](https://en.wikipedia.org/wiki/Idempotence) kell lenniük. Az **idempotens** azt jelenti, hogy a sikeres szkript újrafuttatása esetén ugyanaz az eredmény várható. A szkript végrehajtása átmeneti hálózati problémák következtében meghiúsulhat. Ebben az esetben a feladat automatikusan újrapróbálkozik, és a megadott számú alkalommal ismételten futtatja a szkriptet, mielőtt feladná azt. Az idempotens szkriptek akkor is azonos eredménnyel járnak, ha már két (vagy több) alkalommal sikeresen futottak.

A legegyszerűbb módszer, ha ellenőrzi az objektum meglétét, mielőtt létrehozná.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Ehhez hasonlóan a szkriptnek sikeresen végrehajthatónak kell lennie az észlelt feltételek logikai tesztelése és azokkal ellentétes műveletek révén.



## <a name="next-steps"></a>Következő lépések

- [Rugalmas feladatok létrehozása és kezelése a PowerShell használatával](elastic-jobs-powershell.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql.md)
