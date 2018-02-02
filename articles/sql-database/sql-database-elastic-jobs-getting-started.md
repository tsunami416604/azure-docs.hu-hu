---
title: "Ismerkedés a rugalmas feladatok |} Microsoft Docs"
description: "A rugalmas adatbázis-feladatok segítségével hajtható végre T-SQL-parancsfájlok, amelyek több adatbázis több."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 2540de0e-2235-4cdd-9b6a-b841adba00e5
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
ms.openlocfilehash: c5fe5256d4a8b8479f5e4aff95ec03fc1ba1018a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="getting-started-with-elastic-database-jobs"></a>Ismerkedés a rugalmas adatbázis-feladatok
A rugalmas adatbázis feladataihoz (előzetes verzió) Azure SQL Database megbízhatóan hajtható végre T-SQL-parancsfájlok, amelyek több adatbázis több során automatikusan újrapróbálkozás és a végleges befejezési garanciák teszi lehetővé. A rugalmas adatbázis feladat szolgáltatással kapcsolatos további információkért lásd: [rugalmas feladatok](sql-database-elastic-jobs-overview.md).

Ez a cikk bővíti a minta található [Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md). Amikor elkészült, megtanulhatja a kapcsolódó adatbázisok csoportja felügyelt feladatok létrehozásához és kezeléséhez. Nincs szükség a rugalmas bővítést eszközeit használja a rugalmas feladatok előnyeinek kihasználása érdekében.

## <a name="prerequisites"></a>Előfeltételek
Töltse le és futtassa a [Ismerkedés a rugalmas adatbázis eszközök minta](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Hozzon létre egy shard mintaalkalmazás térkép-kezelőt
Itt létre shard térképre manager több szegmensben osztják, az adatok beszúrását követi azokat a szilánkok együtt. Ha már rendelkezik a bennük foglalt horizontálisan skálázott adatok állítsa be a szilánkok, hagyja ki a következő lépéseket, és helyezze át a következő szakaszban.

1. Hozza létre, és futtassa a **Ismerkedés a rugalmas adatbáziseszközöket** mintaalkalmazást. Kövesse a lépéseket, amíg a szakasz a 7. lépés [töltse le és futtassa a mintaalkalmazást](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). 7. lépés végén tekintse meg a következő parancssort:

   ![parancssor](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. A parancsablakban írja be a "1", és nyomja le az ENTER **Enter**. Létrehozza a shard térkép manager, és két szilánkok hozzáadása a kiszolgálóhoz. Ezután írja be a "3", és nyomja le az ENTER **Enter**; Ez a művelet megismétlése négy alkalommal. Ez a szilánkok minta adatsorok szúrja be.
3. A [Azure-portálon](https://portal.azure.com) három új adatbázist kell megjelenítenie:

   ![Visual Studio confirmation](./media/sql-database-elastic-query-getting-started/portal.png)

   Ezen a ponton azt hozzon létre egy egyéni adatbázis-gyűjteményt, amely tükrözi a shard térkép összes adatbázisát. Ez lehetővé teszi, hogy létrehozni és végrehajtani egy feladatot, amely egy új tábla szilánkok között.

Itt azt kellene általában a shard térkép létrehozásához céloz, használja a **New-AzureSqlJobTarget** parancsmag. A szilánkok manager adatbázist be kell állítani egy adatbázis célként, és majd az adott shard térkép cél van megadva. Ehelyett fogjuk számba venni a kiszolgáló összes adatbázisát, és az adatbázisok a gyűjteményhez hozzáadni kívánt új egyéni fő adatbázis kivételével.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Létrehoz egy egyéni gyűjteményt, és adja hozzá a összes adatbázis a kiszolgálón a egyéni gyűjtemény cél fő kivételével.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Az adatbázisok közötti végrehajtási T-SQL parancsfájl létrehozása
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>A parancsprogram végrehajtása az egyéni csoport adatbázisok között feladat létrehozása

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>A feladat végrehajtása
A következő PowerShell-parancsfájl segítségével egy meglévő feladat végrehajtása:

Frissítse a következő változót a kívánt feladat neve végrehajtott megfelelően:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Egyetlen feladat-végrehajtási állapotának beolvasása
Ugyanazon **Get-AzureSqlJobExecution** parancsmagot a **IncludeChildren** paraméter gyermek feladat végrehajtások, nevezetesen a minden feladat végrehajtása a feladat által megcélzott minden adatbázison meghatározott állapotban állapotának megtekintéséhez.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Több feladat végrehajtások közötti a állapotának megtekintése
A **Get-AzureSqlJobExecution** parancsmag több feladat végrehajtások, a megadott paramétereknek szűrt megjelenítéséhez használható több választható paraméterrel rendelkezik. A következő mutatja be a Get-AzureSqlJobExecution használatának lehetséges módjai közül:

Lekéri az összes aktív legfelső szintű feladat végrehajtások:

   ```
    Get-AzureSqlJobExecution
   ```

Lekéri az összes legfelső szintű feladat végrehajtások, beleértve az inaktív feladat végrehajtások:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Lekéri az összes gyermek feladat végrehajtások inaktív feladat végrehajtások többek között a megadott feladat végrehajtási Azonosítóhoz:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Ütemezés használatával létrehozott összes feladat végrehajtások beolvasása / feladat-kombináció, beleértve az inaktív feladatok:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Lekéri az összes feladat megadott shard térképre, beleértve az inaktív feladatokat célcsoportkezelést:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Lekéri az összes feladat a megadott egyéni gyűjtemény, beleértve az inaktív feladatokat célcsoportkezelést:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Feladat feladat végrehajtások belül egy adott feladat végrehajtási listájának beolvasása:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Feladat végrehajtási részlete beolvasása:

A következő PowerShell-parancsfájl segítségével egy feladat a feladat a végrehajtás, amely akkor különösen hasznos, ha végrehajtási hibakeresésére részleteinek megtekintéséhez.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Sikertelen feladat feladat végrehajtások belül beolvasása
A JobTaskExecution objektum tulajdonság az életciklus a feladat együtt egy üzenettulajdonságot tartalmaz. Ha egy feladat a feladat végrehajtása sikertelen volt, a életciklus tulajdonsága *sikertelen* és az üzenet tulajdonsága az eredményül kapott hibaüzenetet, és a verem. Ha egy feladat sikertelen volt, fontos, amely egy adott feladat sikertelen feladat-feladatok részletes adatainak megtekintéséhez.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>Várakozás a feladat-végrehajtás befejeződésére
A következő PowerShell-parancsfájl segítségével Várjon, amíg a feladat feladat elvégzéséhez:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Egyéni végrehajtási házirend létrehozása
Rugalmas adatbázis-feladatok feladat indításakor alkalmazható egyéni végrehajtási házirendek létrehozását támogatja.

Végrehajtási házirendek definiálása jelenleg engedélyezése:

* Name: A végrehajtási házirend azonosítója.
* Feladat időtúllépése: Teljes idő előtt rugalmas adatbázis-feladatok megszakította a feladatot.
* Kezdeti újrapróbálkozási időköz: Intervallum várjon próbálkozik újra.
* Maximális újrapróbálkozási időköz: Cap újrapróbálkozási intervallumok használatára.
* Újrapróbálkozási időköz leállítási együttható: A következő intervallum próbálkozások közötti különbség kifejezésére szolgáló együttható.  Az alábbi képlet használható: (kezdeti újrapróbálkozási időközét) * Math.pow ((időköz leállítási együttható), (próbálkozások száma) - 2).
* Kísérletek maximális száma: Az újrapróbálkozások maximális számát megkísérel végrehajtani egy feladatot belül.

Az alapértelmezett végrehajtási házirendet a következő értékeket használja:

* Name: Alapértelmezett végrehajtási házirend
* Feladat időtúllépése: 1 hét
* Kezdeti újrapróbálkozási időköz: 100 milliomod másodperc
* Maximális újrapróbálkozási időköz: 30 perc
* Ismételje meg a időköz együttható: 2. régiója
* Kísérletek maximális száma: 2 147 483 647

A kívánt végrehajtási szabályzat létrehozása:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Egy egyéni végrehajtási házirend frissítése
A frissíteni kívánt végrehajtási házirend frissítése:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Feladatok megszakítása
Rugalmas adatbázis-feladatok feladatok megszakítását kérelmeket támogatja.  Ha a rugalmas adatbázis-feladatok jelenleg végrehajtás alatt álló feladat a lemondási kérelmet észlel, megkísérli a feladat leállítása.

Rugalmas adatbázis-feladatok is hajtsa végre a megszakítási két különböző módja van:

1. Megszakítása jelenleg feldolgozás alatt álló feladatok: törlése közben jelenleg fut egy feladat észleli, ha a jelenleg végrehajtás alatt álló szempontja, hogy a feladat belül törlése megkísérlése.  Példa: Ha egy hosszú ideig tartó jelenleg végrehajtás alatt álló lekérdezés során a törlése megkísérlése, van-e a lekérdezés megszakításához tett kísérlet.
2. Zároló feladat újrapróbálkozások: Törlése a vezérlő szál észlelésekor feladat a végrehajtás elindítása előtt, a vezérlő szál Ezzel elkerülheti a feladat futtatására és a kérelem deklarálható megszakítottként.

A feladat megszakításának szülő-feladat van szükség, ha a lemondási kérelmet a szülő feladat és összes a gyermek-feladatokkal figyelembe véve.

Megszakítási kérelmet küldeni, használja a **Stop-AzureSqlJobExecution** parancsmag és állítsa be a **JobExecutionId** paraméter.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>A feladat nevét és a feladatelőzmények törlése
Rugalmas adatbázis-feladatok támogatja az aszinkron feladatok törlése. Egy feladat is törlésre, és a rendszer törli a feladatot, és a feladatelőzményekben az összes feladat végrehajtások a feladat befejezése után. A rendszer automatikusan szakítsa meg a aktív feladat végrehajtások.  

Ehelyett Stop-AzureSqlJobExecution megszakítja az aktív feladat végrehajtások lehet meghívni.

Törlési feladat indításához használja a **Remove-AzureSqlJob** parancsmag és állítsa be a **JobName** paraméter.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Hozzon létre egy egyéni adatbázis-cél
Egyéni adatbázis célok meghatározása a rugalmas adatbázis-feladatokhoz, amelyek a végrehajtás közvetlenül vagy egy egyéni adatbázis csoportban foglalható is használható. Mivel a **rugalmas készletek** nem még közvetlenül támogatottak a PowerShell API-k segítségével egyszerűen létrehozhat egy egyéni adatbázis célként és egy egyéni adatbázis gyűjtemény célként, ami magában foglalja a készlet összes adatbázisát.

Állítsa be a következő változókat, hogy tükrözze a kívánt adatbázis adatait:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Hozzon létre egy egyéni adatbázis-gyűjtemény célja
Egyéni adatbázis gyűjtemény célja végrehajtásának engedélyezéséhez több meghatározott adatbázis cél között definiálhatók. Egy adatbázis-csoport létrehozása után adatbázisok társítható egyéni gyűjtemény célja.

Állítsa be a kívánt egyéni gyűjtemény konfigurációjához megfelelően a következő változókat:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Adatbázisok hozzáadása egy egyéni adatbázis-gyűjtemény célja
Egyéni adatbázis gyűjtemény célok hozzon létre egy csoportot az adatbázisok adatbázis célok társítható. Egy feladat jön létre, amely egyéni adatbázis gyűjtemény céloz, amikor ki van bontva, amelyekre a végrehajtás időpontjában a csoporthoz tartozó adatbázisok.

Vegye fel a kívánt adatbázist egy adott egyéni gyűjtemény:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Tekintse át az adatbázisokat egy egyéni adatbázis gyűjtemény a cél
Használja a **Get-AzureSqlJobTarget** parancsmag egyéni adatbázis gyűjtemény célja a gyermek adatbázis beolvasása.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Hozzon létre egy feladatot, a parancsfájl végrehajtása egy egyéni adatbázis-gyűjtemény célja között
Használja a **New-AzureSqlJob** parancsmag segítségével hozzon létre egy feladatot, egy egyéni adatbázis gyűjtemény tároló által definiált adatbázisok csoportja ellen. Rugalmas adatbázis-feladatok kibontja a feladat több gyermek feladat minden egyes adatbázis megfelelő társított egyéni adatbázis gyűjtemény célja, és győződjön meg arról, hogy a parancsfájl végrehajtása minden egyes adatbázison. Ebben az esetben fontos, hogy-e parancsfájlokat idempotent való ismételt próbálkozás rugalmasak lehetnek.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Az adatbázisok közötti adatok gyűjtése
**Rugalmas adatbázis-feladatok** támogatja, a lekérdezés végrehajtása adatbázisok csoportja között, és a megadott adatbázis tábla történő küldése. A tábla minden egyes adatbázisból a lekérdezési eredmények megtekintése érdekében bekövetkeztek kérdezhetők le. Ez a lekérdezés végrehajtása több adatbázis közötti egy aszinkron mechanizmust biztosít. Hiba esetben, például egy adatbázis átmenetileg elérhetetlenné újrapróbálkozások keresztül automatikusan kezeli.

A megadott célhely tábla automatikusan jön létre, ha még nincs, a séma, a visszaadott eredményhalmaz megfelelő. A parancsfájl végrehajtása több eredménykészlet adja vissza, ha a rugalmas adatbázis-feladatok csak akkor küldi el az első sablon kiválasztásával megadott céltábla.

A következő PowerShell-parancsfájl segítségével egy parancsfájlt, az eredmények gyűjtése egy megadott táblába. Ez a parancsfájl feltételezi, hogy egy T-SQL parancsfájl létrehozva a következő amelyek kimenete egy eredményhalmaz és létrehoztak egy egyéni adatbázis gyűjtemény célhelyet.

Állítsa be a kívánt parancsfájl, a hitelesítő adatokat, és a végrehajtási célját megfelelően a következő:

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Hozzon létre, és elindíthat egy feladatot a adatáttelepítések gyűjtemény esetében
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>A feladat végrehajtását egy feladat eseményindító ütemezés létrehozása
A következő PowerShell-parancsfájl segítségével hozzon létre egy feladatról ütemezést. A parancsfájl egy percen, de a New-AzureSqlJobSchedule is támogat - DayInterval, - HourInterval, - MonthInterval, és - WeekInterval paraméterek. Csak egyszer hajtható végre ütemezéseket is létrehozható, hogy - alkalommal.

Új ütemezés létrehozása:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Szeretné, hogy a feladat végrehajtása ütemezéssel feladat eseményindító létrehozása
Egy feladat eseményindító szeretné, hogy a feladat végrehajtása idő ütemezés szerint lehet megadni. A következő PowerShell-parancsfájl segítségével hozzon létre egy feladat eseményindító.

Állítsa be a következő változókat, hogy a kívánt feladat és ütemezése:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>A feladat ütemezés futtatásának leállítása ütemezett társításának megszüntetése
Megszüntetheti a feladatról feladat végrehajtása a feladat indítási keresztül, a feladat indítási távolíthatja el.
Távolítsa el a feladat eseményindító megfelelően egy ütemezés használatával végrehajtott egy feladatot leállítja a **Remove-AzureSqlJobTrigger** parancsmag.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>A rugalmas adatbázis-lekérdezések eredményének Excel importálása
 Importálhatja az eredményeket a lekérdezés egy Excel-fájlhoz.

1. Indítsa el az Excel 2013.
2. Keresse meg a **adatok** menüszalagján.
3. Kattintson a **egyéb forrásokból származó** kattintson **az SQL Server**.

   ![Excel importálása más forrásokból](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. Az a **Adatkapcsolat varázsló** írja be a kiszolgáló nevét és a bejelentkezési hitelesítő adatokat. Ezután kattintson a **Next** (Tovább) gombra.
5. A párbeszédpanelen **válassza ki a kívánt adatokat tartalmazó adatbázis**, jelölje be a **ElasticDBQuery** adatbázis.
6. Válassza ki a **ügyfelek** táblázatban a lista nézetben, majd kattintson **következő**. Kattintson a **Befejezés**.
7. Az a **és adatokat importálhat** űrlap **válassza ki, hogy az adatok megtekintéséhez a munkafüzet**, jelölje be **tábla** kattintson **OK**.

Összes sorát **ügyfelek** tábla különböző szilánkok tárolt feltölti az Excel-táblában.

## <a name="next-steps"></a>További lépések
Most már használhatja az Excel adatok funkciók. A kapcsolati karakterláncot használ a kiszolgáló nevét, az adatbázisnév és a hitelesítő adatok adatbázishoz való kapcsolódáshoz a BI és az integráció eszközök a rugalmas lekérdezés. Győződjön meg arról, hogy az SQL Server támogatja-e az eszköz adatforrásként. Tekintse meg a lekérdezés rugalmas adatbázis és a külső táblák csakúgy, mint bármely más SQL Server-adatbázis és SQL Server-táblázatot, amely az eszköz kíván csatlakozni.

### <a name="cost"></a>Költségek
Nem kell külön fizetni a rugalmas adatbázis-lekérdezés szolgáltatása van. Azonban jelenleg ez a funkció csak a premium adatbázisokat a rendelkezésre álló egy záró pontot, de a szilánkok lehet bármely szolgáltatásréteg.

Díjszabási információkért lásd: [SQL adatbázis díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
