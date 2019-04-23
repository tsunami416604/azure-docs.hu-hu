---
title: Ismerkedés a rugalmas adatbázis-feladatok |} A Microsoft Docs
description: Rugalmas adatbázis-feladatok segítségével hajtsa végre a több adatbázisra kiterjedő T-SQL-parancsfájlok.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6d794fb14b7f581c9e9b92dc581de97e0a236630
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786472"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Ismerkedés a rugalmas adatbázis-feladatok

Elastic Database-feladatok (előzetes verzió) az Azure SQL Database lehetővé teszi, hogy automatikusan újra próbálkozik, és a végleges befejezési garanciákat biztosít több adatbázisra kiterjedő T-SQL-parancsfájlok megbízhatóan futtathat. A rugalmas feladat szolgáltatással kapcsolatos további információkért lásd: [az Elastic jobs](sql-database-elastic-jobs-overview.md).

Ez a cikk a található minta kibővíti [Ismerkedés az Elastic Database-eszközök](sql-database-elastic-scale-get-started.md). Amikor elkészült, megismerheti, hogyan hozhat létre és kezelhet, amelyek a kapcsolódó adatbázisok egy csoportjának kezelését feladatok. Nem kötelező a rugalmasan méretezhető eszközeivel az Elastic jobs előnyeinek kihasználása érdekében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

Töltse le és futtassa a [Ismerkedés az Elastic Database-eszközök minta](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Hozzon létre egy szegmens a mintaalkalmazással kezelő

Itt létrehozhat horizontálispartíció-térkép manager több szegmensben, majd az adatok beszúrását a szegmensekre együtt. Ha már rendelkezik állítsa be őket a horizontálisan skálázott adatok szegmensek, hagyja ki a következő lépéseket, és helyezze át a következő szakaszra.

1. Hozza létre és futtassa a **Ismerkedés az Elastic Database-eszközök** mintaalkalmazást. Kövesse a lépéseket, amíg a szakasz a 7. lépés [töltse le és futtassa a mintaalkalmazást](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). 7. lépés végén jelenik meg a következő parancssort:

   ![parancssor](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. A parancssori ablakba írja be a "1", és nyomja le az **Enter**. Ez létrehozza a szegmens kezelő, és két szegmensek hozzáadása a kiszolgálóhoz. Ezután írja be a "3", és nyomja le az **Enter**; négyszer addig ismételje a műveletet. Ez a szegmensek minta adatsor szúr be.
3. A [az Azure portal](https://portal.azure.com) három új adatbázisokat kell megjelennie:

   ![A Visual Studio megerősítése](./media/sql-database-elastic-query-getting-started/portal.png)

   Ezen a ponton hozunk létre egy egyéni adatbázis-gyűjtemény, amely tükrözi a szegmenstérkép összes adatbázishoz. Ez lehetővé teszi számunkra, hogy hozzon létre, és hajtsa végre egy feladatot, amely hozzáad egy új táblát a szegmensek között.

Itt mi lenne általában horizontálispartíció-térkép létrehozása cél használata a **New-AzureSqlJobTarget** parancsmagot. A szegmenstérkép-kezelő adatbázis állítson be egy adatbázis célként, és majd a különleges szegmenstérkép célként van megadva. Ehelyett fogjuk enumerálni az összes adatbázist a kiszolgálón, és adja hozzá az adatbázisokat az új egyéni gyűjtemény kivételével a master adatbázisban.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Egy egyéni gyűjteményt hoz létre, és adja hozzá minden adatbázis a kiszolgáló master kivételével az egyéni gyűjtemény cél

   ```powershell
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
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

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Hozzon létre egy T-SQL parancsfájl végrehajtása több adatbázisban

   ```powershell
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

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Az egyéni csoportot, az adatbázisok között parancsprogram végrehajtása a feladat létrehozása

   ```powershell
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>A feladat végrehajtására

A következő PowerShell-parancsprogram egy meglévő feladat végrehajtására használhatók:

A következő változót kell végrehajtani a kívánt feladat neve megfelelően frissítse:

   ```powershell
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Egy egyetlen feladat-végrehajtási állapotának lekérése

Ugyanaz, mint **Get-AzureSqlJobExecution** parancsmagot a **IncludeChildren** paramétert gyermek feladatvégrehajtások, nevezetesen a meghatározott állapotban minden egyes adatbázison feladat-végrehajtási állapotának megtekintése a feladat által megcélzott.

   ```powershell
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Az állapot megtekintéséhez között több feladat-végrehajtások

A **Get-AzureSqlJobExecution** parancsmag rendelkezik több választható paraméterek: több feladatvégrehajtások szűrt keresztül megadott paraméterek megjelenítéséhez használható. A következő mutat be néhányat. használja a Get-AzureSqlJobExecution lehetséges módja:

Kérje le az összes aktív legfelső szintű feladat-végrehajtások:

   ```powershell
    Get-AzureSqlJobExecution
   ```

Minden felső szintű feladat-végrehajtások, beleértve az inaktív feladat-végrehajtások lekéréséhez:

   ```powershell
    Get-AzureSqlJobExecution -IncludeInactive
   ```

A megadott feladat végrehajtási Azonosítóhoz, beleértve az inaktív feladat-végrehajtások az összes gyermek feladatvégrehajtások lekéréséhez:

   ```powershell
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Egy ütemezés használatával létrehozott összes feladat-végrehajtások beolvasása / feladat-kombináció, beleértve az inaktív feladatok:

   ```powershell
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

A megadott horizontális skálázási térképet, beleértve az inaktív feladatok célzó összes feladat beolvasása:

   ```powershell
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

A megadott egyéni gyűjteményét, beleértve az inaktív feladatok célzó összes feladat beolvasása:

   ```powershell
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Belül egy adott feladat-végrehajtási feladat feladat-végrehajtások listájának beolvasása:

   ```powershell
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Kérje le a feladat feladat-végrehajtási részleteit:

A következő PowerShell-parancsfájl segítségével egy feladat a feladat a végrehajtás, amelyek akkor különösen hasznos, ha a hibakeresés végrehajtása érdekében részleteinek megtekintéséhez.

   ```powershell
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Feladat feladat-végrehajtások belül hibáinak beolvasása

A JobTaskExecution objektum a feladatot egy üzenet egyik tulajdonságát együtt életciklusának tulajdonság tartalmazza. Ha egy feladat a feladat végrehajtása sikertelen volt a életciklus tulajdonsága *sikertelen* és a Message tulajdonság értéke az eredményül kapott kivételre vonatkozó üzenet, és a stack. Ha egy feladat sikertelen volt, fontos, hogy egy adott feladat sikertelen feladatok részleteinek megtekintéséhez.

   ```powershell
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

A következő PowerShell-parancsfájl segítségével várnia a feladat feladat végrehajtásához:

   ```powershell
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Hozzon létre egy egyéni végrehajtási házirend

Elastic Database-feladatok feladatok indításakor alkalmazható egyéni végrehajtási házirendek létrehozását támogatja.

Végrehajtási házirendek definiálása jelenleg engedélyezése:

* Név: A végrehajtási házirend azonosítója.
* Feladat időtúllépése: Teljes idő, mielőtt az Elastic Database-feladatok által megszakított feladatok.
* Kezdeti újrapróbálkozási időköz: Első újrapróbálkozás előtti várakozási időt.
* Maximális újrapróbálkozási időköz: Az újrapróbálkozási időközök használandó korlátot.
* Újrapróbálkozási időköz leállítási együttható: Újrapróbálkozások között a következő esedékes kiszámításához használt együttható.  Használja a következő képletre: (A kezdeti újrapróbálkozási időköz) * Math.pow ((intervallum leállítási együttható) (újrapróbálkozások száma) – 2).
* Kísérletek maximális száma: A maximális újrapróbálkozási kísérletek száma a feladatokon belül végrehajtani.

Az alapértelmezett végrehajtási házirendet a következő értékeket használja:

* Név: Alapértelmezett végrehajtási házirend
* Feladat időtúllépése: 1 hét
* Kezdeti újrapróbálkozási időköz:  100 ideje ezredmásodpercben
* Maximális újrapróbálkozási időköz: 30 perc
* Újrapróbálkozási időköz együttható: 2
* Kísérletek maximális száma: 2,147,483,647

A kívánt végrehajtási házirend létrehozása:

   ```powershell
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

A frissíteni kívánt végrehajtási szabályzat frissítése:

   ```powershell
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

Elastic Database-feladatok feladatok megszakítási kérések támogatja.  Elastic Database-feladatok egy jelenleg végrehajtás alatt feladat megszakítási kérelmet észleli, ha megkísérli leállítani a feladatot.

Elastic Database-feladatok is hajtsa végre a megszakítási két különböző módja van:

1. Megszakítása jelenleg végrehajtás alatt álló feladatokat: Lemondás észlel, amíg egy tevékenység fut, ha a lemondás kísérlet történik, a feladat jelenleg végrehajtás alatt aspektusa belül.  Példa: Ha nincs hosszan futó lekérdezést jelenleg lemondás megkísérelt végrehajtani, nincs megszakítja a lekérdezés ismételt megkísérléséhez.
2. Újrapróbálkozások megszakítása. feladat: Lemondás végrehajtási feladat konfigurálása előtt a vezérlő szál észlelése esetén a vezérlőelem szál elkerülhető, hogy a feladat futtatására, és deklarálja a kérelmet, mert meg lett szakítva.

Ha egy feladat törlése a szülőfeladat van szükség, a szülő feladat és az összes hozzá tartozó gyermekfeladatok a megszakítási kérelemre váró figyelembe véve.

Megszakítási kérelmet küldeni, használja a **Stop-AzureSqlJobExecution** parancsmagot, és állítsa a **JobExecutionId** paraméter.

   ```powershell
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Feladat neve és a feladatelőzmények törlése

Elastic Database-feladatok támogatja az aszinkron feladatok törlése. Egy feladat is törlésre, és a rendszer törli a feladatot és annak feladatelőzmények összes feladat-végrehajtások a feladat befejezése után. A rendszer nem automatikusan szünteti meg az aktív feladat-végrehajtások.  

Ehelyett Stop-AzureSqlJobExecution megszakítja az aktív feladat-végrehajtások lehet meghívni.

A törlési feladat indításához használja a **Remove-AzureSqlJob** parancsmagot, és állítsa a **JobName** paraméter.

   ```powershell
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Hozzon létre egy egyéni adatbázis-cél

Rugalmas adatbázis-feladatok végrehajtási közvetlenül vagy egy egyéni adatbázis csoportban belefoglalási használható egyéni adatbázis célok lehet definiálni. Mivel **rugalmas készletek** nem még közvetlenül használhatók a PowerShell API-kon keresztül egyszerűen létrehozhat egy egyéni adatbázis célként és egy egyéni adatbázis gyűjtemény cél számára, amely magában foglalja a készletben található összes adatbázishoz.

Állítsa be a kívánt adatbázis adatait tükrözik a következő változókat:

   ```powershell
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Hozzon létre egy egyéni adatbázis-gyűjtemény cél

Egy egyéni adatbázis-gyűjtemény cél között több meghatározott adatbázis-cél végrehajtásának engedélyezéséhez lehet definiálni. Egy adatbázis-csoport létrehozása után adatbázisok társíthatók a cél-egyéni gyűjtemény.

Állítsa be az alábbi változókat, hogy a kívánt egyéni gyűjtemény cél konfigurációját tükrözzék:

   ```powershell
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Egy egyéni adatbázis-gyűjtemény cél-adatbázisok hozzáadása

Egyéni adatbázis gyűjtemény célozza, hozzon létre egy csoportot az adatbázisok adatbázis-tárolók társítható. Minden alkalommal, amikor egy feladat jön létre, amely az egyéni adatbázis gyűjtemény céloz, ki van bontva, amelyekre a végrehajtás időpontjában a csoporthoz tartozó adatbázisok.

A kívánt adatbázis hozzáadása egy adott egyéni gyűjtemény:

   ```powershell
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Tekintse át az adatbázisok egy egyéni adatbázis-gyűjtemény célon belül

Használja a **Get-AzureSqlJobTarget** beolvasásához a gyermek adatbázisok egy egyéni adatbázis-gyűjtemény célon belül maradjon.

   ```powershell
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Hozzon létre egy feladatot, amely egy szkript végrehajtása egy egyéni adatbázis-gyűjtemény cél között

Használja a **New-AzureSqlJob** parancsmaggal hozzon létre egy feladatot egy egyéni adatbázis-gyűjtemény cél által meghatározott adatbázisok csoportjain. Elastic Database-feladatok kibővíti a feladat be több, az egyéni adatbázis-gyűjtemény cél társított minden egyes adatbázishoz tartozó gyermekfeladatok, és győződjön meg arról, hogy a parancsfájl végrehajtása minden adatbázison. Újra fontos, hogy a parancsfájlok idempotens rugalmas való próbálkozások.

   ```powershell
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Adatgyűjtés több adatbázisban

**Elastic Database-feladatok** adatbáziscsoportok közötti a lekérdezés végrehajtása támogatja, és elküldi az eredményeket egy megadott adatbázistáblába. A táblázat az egyes adatbázisok a lekérdezés eredményeinek megtekintéséhez utólag kérdezhetők le. Ez itt egy mechanizmust, az aszinkron lekérdezés végrehajtása több adatbázis közötti. Hiba eseteket, például egy adatbázis ideiglenesen elérhetetlenné automatikusan kezeli, az újrapróbálkozások keresztül.

A megadott célpartícióra tábla automatikusan létrejön, ha ezt még nem létezik, a visszaadott eredményhalmaz sémájának megfelelő. A parancsfájl végrehajtása több eredménykészletet adja vissza, ha a rugalmas adatbázis-feladatok csak küld a megadott cél tábla első alkalommal.

A következő PowerShell-parancsfájl segítségével futtathat egy olyan parancsprogramot, az eredmények gyűjtése a megadott táblába. Ez a parancsfájl feltételezi, hogy egyetlen eredményhalmaz adja vissza, amely egy T-SQL-szkript létrejött, és a egy egyéni adatbázis-gyűjtemény cél jött létre.

Állítsa be a kívánt parancsfájlt, a hitelesítő adatok és a végrehajtási célként a következőket:

   ```powershell
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

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Hozzon létre, és a gyűjtemény adatforgatókönyvek feladat indítása

   ```powershell
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>A feladat-végrehajtási feladat trigger használatával ütemezés létrehozása

A következő PowerShell-parancsfájl segítségével egy ismétlődő ütemezés létrehozásához. Ez a szkript egy egy perces időközt, de a New-AzureSqlJobSchedule is támogatja a - DayInterval, - HourInterval, - MonthInterval, és - WeekInterval paraméterek. Hozható létre ütemezés, amely csak egyszer hajtható végre az átadott - Item parancsot.

Új ütemezés létrehozása:

   ```powershell
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Szeretné, hogy egy feladat megadott idő ütemezés szerint elvégzendő feladat eseményindító létrehozása

Egy feladat eseményindító szeretné, hogy a feladat végrehajtása egy ideje ütemezés szerint lehet definiálni. A következő PowerShell-parancsfájl segítségével hozzon létre egy feladatot az eseményindító.

Állítsa be az alábbi változókat, hogy a kívánt feladatot és ütemezése:

   ```powershell
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Egy ütemezett feladat ütemezés futtatásának leállítása társításának megszüntetése

Ismétlődő feladat-végrehajtási feladat eseményindító keresztül lemondásához, a feladat eseményindító távolíthatja el.
Egy feladat a végrehajtás alatt egy ütemezés használatával megfelelően leállítása feladat eseményindító távolítsa el a **Remove-AzureSqlJobTrigger** parancsmagot.

   ```powershell
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Rugalmas adatbázis-lekérdezés eredményei importálása Excelbe

 A lekérdezés eredményeit, egy Excel-fájlba importálhatja.

1. Indítsa el az Excel 2013-hoz.
2. Keresse meg a **adatok** menüszalagon.
3. Kattintson a **egyéb forrásokból származó** kattintson **az SQL Server**.

   ![Az Excel import más forrásokból](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. Az a **Adatkapcsolat varázsló** írja be a kiszolgáló nevét és bejelentkezési hitelesítő adatokat. Ezután kattintson a **Next** (Tovább) gombra.
5. A párbeszédpanel **válassza ki a kívánt adatokat tartalmazó adatbázisban**, jelölje be a **ElasticDBQuery** adatbázis.
6. Válassza ki a **ügyfelek** a listanézet táblát, és kattintson a **tovább**. Kattintson a **Befejezés**.
7. Az a **adatok importálása** űrlap **válassza ki, hogyan szeretné az adatok megtekintéséhez a munkafüzetet a**válassza **tábla** kattintson **OK**.

Az összes sort **ügyfelek** a különböző szegmensekben tárolt tábla, töltse fel az Excel-munkalapot.

## <a name="next-steps"></a>További lépések

Mostantól használhatja az Excel-adatok funkciók. A kapcsolati karakterlánc használata a kiszolgáló nevét, az adatbázis nevét és a hitelesítő adatok adatbázishoz való csatlakozáshoz a BI-ban és integrációs eszközök a rugalmas lekérdezés. Győződjön meg arról, hogy az SQL Server támogatott-e az eszköz adatforrásként. Tekintse meg a rugalmas lekérdezés adatbázis és a külső táblák csakúgy, mint bármely más SQL Server-adatbázis és kíván csatlakozni, az eszköz az SQL Server-táblákra.

### <a name="cost"></a>Költségek

Nem jár további költségekkel az Elastic Database query funkciójával. Azonban jelenleg ez a funkció csak a prémium és üzletileg kritikus adatbázisok és rugalmas készletekhez elérhető teljes pontként, de a szegmensek lehet bármely szolgáltatási rétegben.

Díjszabási információkért tekintse meg a [SQL Database szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->