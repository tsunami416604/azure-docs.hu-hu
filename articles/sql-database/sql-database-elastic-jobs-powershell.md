---
title: PowerShell-lel rugalmas feladatok létrehozásához és kezeléséhez |} Microsoft Docs
description: Az Azure SQL Database-készletek kezelésére szolgáló PowerShell
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 425213f66a4b9960cf68a40c935fedff12f9f75d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>SQL Database PowerShell (előzetes verzió) segítségével a rugalmas feladatok létrehozásához és kezeléséhez

A PowerShell API-khoz, **rugalmas adatbázis-feladatok** (az előzetes verzió), lehetővé teszik, hogy olyan adatbázisok, amely végrehajtja a parancsfájlok csoportját. Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti **rugalmas adatbázis-feladatok** PowerShell-parancsmagok használatával. Lásd: [rugalmas feladatok áttekintése](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ingyenes próbaverzió, lásd: [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* A rugalmas adatbázis eszközzel létrehozott adatbázisok készleteit. Lásd: [Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/overview).
* **Rugalmas adatbázis-feladatok** PowerShell csomag: lásd: [telepítése rugalmas adatbázis-feladatok](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetéshez
Válassza ki az előfizetést az előfizetés-azonosítót kell (**- SubscriptionId**) vagy az előfizetés nevét (**- SubscriptionName**). Ha több előfizetéssel rendelkezik futtathatja a **Get-AzureRmSubscription** parancsmagot, és másolja a kívánt előfizetés-adatokat, az eredmény az beállítása. Miután az előfizetési adatai, futtassa az alapértelmezett, nevezetesen a cél a feladatok létrehozását és kezelését az előfizetés beállításához a következő parancsmagot:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

A [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) ajánlott használatra történő fejlesztéséhez és a PowerShell-szkriptek használatát a rugalmas adatbázis-feladatok végrehajtása.

## <a name="elastic-database-jobs-objects"></a>A rugalmas adatbázis-feladatok objektumok
A következő táblázat felsorolja az összes objektum típusú kimenő **rugalmas adatbázis-feladatok** együtt a leírása és a megfelelő PowerShell API-k.

<table style="width:100%">
  <tr>
    <th>Objektumtípus</th>
    <th>Leírás</th>
    <th>Kapcsolódó PowerShell API-k</th>
  </tr>
  <tr>
    <td>Hitelesítő adat</td>
    <td>Felhasználónév és jelszó parancsprogramok végrehajtását vagy DACPACs alkalmazásának adatbázisok való kapcsolódáskor használ. <p>A jelszó küldése és a rugalmas adatbázis-feladatok adatbázis tárolási előtt titkosítva.  A rugalmas adatbázis-feladatok szolgáltatás a hitelesítő adat létrehozása és fel kell tölteni a telepítési parancsfájl segítségével visszafejti a jelszót.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Szkript</td>
    <td>Transact-SQL parancsfájl végrehajtása az adatbázisok közötti használt.  A parancsfájl a kell kell lennie az idempotent, mivel a szolgáltatás megpróbálja hibák után a parancsfájl végrehajtása lett létrehozva.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Adatrétegbeli alkalmazás </a> az adatbázisok közötti alkalmazni kívánt csomagot.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Adatbázis-cél</td>
    <td>Egy Azure SQL-adatbázisra mutató adatbázis és a kiszolgáló nevét.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>A shard térkép cél</td>
    <td>Egy adatbázis célként és egy rugalmas adatbázist shard leképezés tárolt információk meghatározásához használt hitelesítő adatot kombinációja.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Egyéni gyűjtemény célja</td>
    <td>A végrehajtás együttesen használandó adatbázisok meghatározott csoportja.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Egyéni gyűjtemény gyermek cél</td>
    <td>Egy egyéni gyűjteményből hivatkozott adatbázis cél.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Feladat</td>
    <td>
    <p>Egy feladat végrehajtásának elindítása vagy ütemezés szerint teljesítéséhez használható paramétereinek meghatározása.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Feladat végrehajtása</td>
    <td>
    <p>Egy parancsfájl vagy egy DACPAC alkalmazott hitelesítő adatok használatával az adatbázis-kapcsolat hibákkal célja az teljesítéséhez szükséges feladatokat tartalmazó tároló egy végrehajtási házirend megfelelően kezeli.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>A feladat a végrehajtás feladat</td>
    <td>
    <p>Egyetlen munkaegység a feladatok teljesítése érdekében kapcsolódott.</p>
    <p>Ha egy feladat sikeresen nem képes hajtható végre, az eredményül kapott Kivételüzenet naplózza a rendszer, és egy új egyező feladata létrejön és a megadott végrehajtási házirend megfelelően hajtotta végre.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Feladat-végrehajtási házirend</td>
    <td>
    <p>Vezérlők sikertelen feladat-végrehajtási időtúllépés, a újrapróbálkozási korlátozások és a próbálkozások közötti időintervallumot.</p>
    <p>Rugalmas adatbázis-feladatok tartalmaz egy alapértelmezett feladat végrehajtási házirendet, amely a feladat sikertelen feladatok gyakorlatilag végtelen újrapróbálkozások okozhat a exponenciális leállítási az intervallumok között minden próbálkozzon újra.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Ütemezés</td>
    <td>
    <p>Ideje alapján kerül sor, vagy a feladatról időköz, vagy egy alkalommal végrehajtási előírása.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Feladat indítja el</td>
    <td>
    <p>A feladatok és az ütemezés szerint eseményindító feladat végrehajtása a kívánt ütemezést közötti leképezést.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Támogatott rugalmas adatbázis-feladatok csoportban típusok
A feladat végrehajtja a Transact-SQL (T-SQL) parancsfájlok vagy DACPACs alkalmazásának adatbázisok csoportja között. Amikor egy feladat adatbázisok csoportja között hajtható végre, a feladat "kiterjeszti" a gyermek feladatok, ahol minden egyes hajtja végre a kért végrehajtási egyetlen adatbázis csoport be. 

A csoportokat, amelyek hozhat létre két típusa van: 

* [A shard térkép](sql-database-elastic-scale-shard-map-management.md) csoport: amikor egy feladatot, amelyekre a shard térképet, a feladat lekérdezi az aktuális meg a szilánkok a shard térkép, és majd létrehozza gyermek minden shard feladataihoz a shard térkép.
* Egyéni gyűjtési csoportjának: egy egyénileg definiált adatbázisok készleteit. Ha egy feladat egyéni gyűjtemény célja, azt gyermek feladatok létrehozása az egyes adatbázisok jelenleg az egyéni gyűjteményben.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Állítsa be a rugalmas feladatok a kapcsolat
A kapcsolat kell állítani a feladat *feladatvezérlő adatbázishoz* a feladatok API-k használata előtt. Egy hitelesítő adat ablakot, ahol a felhasználónevet és a rugalmas feladatok telepítésekor létrehozott jelszót kérő felugró futtatja ezt a parancsmagot váltja ki. Ebben a témakörben közölt összes példák feltételezik, hogy az első lépéshez már végbementek.

Nyissa meg a rugalmas feladatok kapcsolatot:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>A rugalmas feladatok belül titkosított hitelesítő adatokat
Adatbázis-hitelesítő adatok szúrhatók be, a feladatok *feladatvezérlő adatbázishoz* a jelszóval titkosított. Fontos engedélyezhetik a feladatokat hajthatnak végre egy későbbi időpontban (használatával a feladatok ütemezésének) hitelesítő adatok tárolását.

Titkosítási működik, hozza létre a telepítési parancsfájl részeként tanúsítvány keresztül. A telepítési parancsfájlt hoz létre, és a tanúsítvány tölt be az Azure-Felhőszolgáltatásban tárolt titkosított jelszavak visszafejtéséhez. Az Azure Cloud Service később tárolja a nyilvános kulcsot a feladatok belül *feladatvezérlő adatbázishoz* lehetővé teszi a megadott jelszóval titkosításához anélkül, hogy helyileg telepíteni kell a tanúsítványt a PowerShell API-t vagy Azure portál felület .

A hitelesítő adatok jelszavak titkosított és védett, csak olvasási hozzáféréssel rendelkező felhasználók a rugalmas adatbázis-feladatok objektumok. Azonban lehetséges, hogy egy rosszindulatú felhasználó rugalmas adatbázis-feladatok objektumok olvasási és írási hozzáféréssel rendelkező bontsa ki a jelszót. Hitelesítő adatok is felhasználják feladat végrehajtások készültek. Hitelesítő adatai továbbítódnak céladatbázisokhoz kapcsolatok létesítéséhez. Jelenleg nincs korlátozás az egyes hitelesítő adatot használja a céladatbázisokhoz, rosszindulatú felhasználó hozzáadhatja a rosszindulatú felhasználók vezérlése alatt adatbázis egy adatbázis cél. A felhasználó később volt az adatbázis ahhoz, hogy a hitelesítésre szolgáló jelszó célzó feladat indítása.

Ajánlott biztonsági eljárások a rugalmas adatbázis-feladatok a következők:

* A megbízható személyek API-használatát korlátozása.
* Hitelesítő adatok a feladat végrehajtásához szükséges a lehető legkevesebb jogosultsággal kell rendelkeznie.  További információ látható belül ez [engedélyezési és engedélyek](https://msdn.microsoft.com/library/bb669084.aspx) SQL Server MSDN-cikk tárgyalja.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Az adatbázisok közötti egy titkosított hitelesítő adatokat, a feladat végrehajtása létrehozásához
Egy új titkosított hitelesítő adat létrehozása a [ **Get-Credential parancsmag** ](https://technet.microsoft.com/library/hh849815.aspx) egy felhasználónevet és jelszót, amely átadhatók kérni fogja a [ **New-AzureSqlJobCredential parancsmag**](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Hitelesítő adatok frissítése
Ha jelszó módosításához használja a [ **Set-AzureSqlJobCredential parancsmag** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) és állítsa be a **CredentialName** paraméter.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Egy rugalmas adatbázist shard térkép célhoz meghatározása
Shard csoportban lévő összes adatbázisokhoz egy feladat végrehajtásához (használatával létrehozott [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md)), használja a shard leképezését adatbázis céljaként. Ebben a példában az Elastic Database ügyféloldali kódtár használatával létrehozott szilánkos alkalmazás szükséges. Lásd: [Ismerkedés a rugalmas adatbázis eszközök minta](sql-database-elastic-scale-get-started.md).

A szilánkok manager adatbázist be kell állítani egy adatbázis célként, és majd a megadott shard térkép meg kell adni, célként.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Az adatbázisok közötti végrehajtási T-SQL parancsfájl létrehozása
T-SQL-parancsfájlok végrehajtásra létrehozásakor ajánlott hozhat létre, azok [idempotent](https://en.wikipedia.org/wiki/Idempotence) és refs-hibákkal szemben. Rugalmas adatbázis-feladatok parancsfájl végrehajtásának próbálkozik, ha végrehajtási hiba, függetlenül a besorolás, a hiba lép fel.

Használja a [ **New-AzureSqlJobContent parancsmag** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) létrehozása és mentése a parancsfájl végrehajtása és a beállított a **- ContentName** és **- CommandText** paraméterek.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Új parancsfájl létrehozása fájlból
Ha a T-SQL parancsfájl fájlban van definiálva, ennek segítségével importálja a parancsfájl:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Az adatbázisok közötti egy T-SQL-parancsfájlt végrehajtó frissítése
A PowerShell parancsfájl a T-SQL egy meglévő parancsfájl parancsszövege frissíti.

Állítsa be a következő változókat kell beállítani a kívánt parancsfájl definíciójának megfelelően:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>A definíció frissíteni egy meglévő parancsfájl
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>A parancsfájl végrehajtása shard térképre között feladat létrehozása
A PowerShell parancsfájl keresztül minden shard rugalmasan méretezhető shard leképezés indít el egy parancsfájl végrehajtásának feladatot.

Állítsa be a következő változókat, hogy tükrözze a kívánt parancsfájlt és a cél:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>A feladat végrehajtásához
A PowerShell-parancsfájl egy létező feladat végrehajtása:

Frissítse a következő változót a kívánt feladat neve végrehajtott megfelelően:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Egyetlen feladat-végrehajtási állapotának beolvasása
Használja a [ **Get-AzureSqlJobExecution parancsmag** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) és állítsa be a **JobExecutionId** paraméter segítségével megtekintheti a feladat végrehajtási állapotát.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Ugyanazon **Get-AzureSqlJobExecution** parancsmagot a **IncludeChildren** paraméter gyermek feladat végrehajtások, nevezetesen a minden feladat végrehajtása a feladat által megcélzott minden adatbázison meghatározott állapotban állapotának megtekintéséhez.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Több feladat végrehajtások közötti az állapot megtekintése
A [ **Get-AzureSqlJobExecution parancsmag** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) több választható paraméterek: több feladat végrehajtások, a megadott paramétereknek szűrt megjelenítéséhez használható. A következő mutatja be a Get-AzureSqlJobExecution használatának lehetséges módjai közül:

Lekéri az összes aktív felső szintű feladat végrehajtások:

    Get-AzureSqlJobExecution

Lekéri az összes felső szintű feladat végrehajtások, beleértve az inaktív feladat végrehajtások:

    Get-AzureSqlJobExecution -IncludeInactive

Lekéri az összes gyermek feladat végrehajtások inaktív feladat végrehajtások többek között a megadott feladat végrehajtási Azonosítóhoz:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Ütemezés használatával létrehozott összes feladat végrehajtások beolvasása / feladat-kombináció, beleértve az inaktív feladatok:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Lekéri az összes feladat megadott shard térképre, beleértve az inaktív feladatokat célcsoportkezelést:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Lekéri az összes feladat a megadott egyéni gyűjtemény, beleértve az inaktív feladatokat célcsoportkezelést:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Feladat feladat végrehajtások belül egy adott feladat végrehajtási listájának beolvasása:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Feladat végrehajtási részlete beolvasása:

A következő PowerShell-parancsfájl segítségével egy feladat a feladat a végrehajtás, amely akkor különösen hasznos, ha végrehajtási hibakeresésére részleteinek megtekintéséhez.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Sikertelen feladat feladat végrehajtások belül beolvasása
A **JobTaskExecution objektum** egy tulajdonság az életciklus a feladat együtt egy üzenettulajdonságot tartalmaz. Ha egy feladat a feladat végrehajtása sikertelen volt, a életciklus tulajdonság úgy lesz beállítva, *sikertelen* és üzenettulajdonság állítja be az eredményül kapott hibaüzenetet, és a verem. Ha egy feladat sikertelen volt, fontos, amely egy adott feladat sikertelen feladat-feladatok részletes adatainak megtekintéséhez.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Várjon a feladat-végrehajtás befejeződésére
A következő PowerShell-parancsfájl segítségével Várjon, amíg a feladat feladat elvégzéséhez:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Egyéni végrehajtási házirend létrehozása
Rugalmas adatbázis-feladatok feladat indításakor alkalmazható egyéni végrehajtási házirendek létrehozását támogatja.

Végrehajtási házirendek definiálása jelenleg engedélyezése:

* Name: A végrehajtási házirend azonosítója.
* Feladat időtúllépése: Teljes idő előtt rugalmas adatbázis-feladatok megszakítja a feladatot.
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

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Egy egyéni végrehajtási házirend frissítése
A frissíteni kívánt végrehajtási házirend frissítése:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Feladatok megszakítása
Rugalmas adatbázis-feladatok a feladatok megszakítását kérelmeket támogatja.  Ha a rugalmas adatbázis-feladatok jelenleg végrehajtás alatt álló feladat a lemondási kérelmet észlel, akkor megkísérli a feladat leállítása.

Rugalmas adatbázis-feladatok is hajtsa végre a megszakítási két különböző módja van:

1. Jelenleg feldolgozás alatt álló feladatok Mégse: törlése közben jelenleg fut egy feladat észleli, ha a megszakítási kísérli meg a rendszer a jelenleg végrehajtás alatt álló szempontja, hogy a feladat belül.  Példa: Ha egy hosszú ideig tartó jelenleg végrehajtás alatt álló lekérdezés során a törlése megkísérlése, az a lekérdezés kísérlet lesz.
2. Tevékenység-újrapróbálkozások megszakítása: törlése a vezérlő szál észlelésekor feladat a végrehajtás elindítása előtt, a vezérlő szál elkerülése érdekében a feladat futtatására és a kérelem deklarálható megszakítottként.

A feladat megszakításának szülő-feladat van szükség, ha a lemondási kérelmet szembeni szerződéses kötelezettségeket a szülő feladat és összes a gyermek-feladatokkal.

Megszakítási kérelmet küldeni, használja a [ **Stop-AzureSqlJobExecution parancsmag** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) és állítsa be a **JobExecutionId** paraméter.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Törli a feladatot, és aszinkron módon feladatelőzmények
Rugalmas adatbázis-feladatok támogatja az aszinkron feladatok törlése. Egy feladat is törlésre, és a rendszer törli a feladatot, és a feladatelőzményekben összes feladat végrehajtások a feladat befejezése után. A rendszer nem fogja automatikusan megszakítja a aktív feladat végrehajtások.  

Invoke [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) megszakítja az aktív feladat végrehajtások.

Törlési feladat indításához használja a [ **Remove-AzureSqlJob parancsmag** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) és állítsa be a **JobName** paraméter.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Egy egyéni adatbázis-tároló létrehozása
Egyéni adatbázis célok közvetlen végrehajtásra vagy egy egyéni adatbázis csoportban foglalható adhat meg. Például mert **rugalmas készletek** van még nem támogatott PowerShell API-val, létrehozhat egy egyéni adatbázis célként és egy egyéni adatbázis gyűjtemény célként, ami magában foglalja a készlet összes adatbázisát.

Állítsa be a következő változókat, hogy tükrözze a kívánt adatbázis adatait:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Egy egyéni adatbázis gyűjtemény tároló létrehozásához
Használja a [ **New-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) parancsmag végrehajtásának engedélyezéséhez több meghatározott adatbázis cél között egyéni adatbázis gyűjtemény célja meghatározásához. Egy adatbázis-csoport létrehozása után adatbázisok társítható egyéni gyűjtemény célja.

Állítsa be a kívánt egyéni gyűjtemény konfigurációjához megfelelően a következő változókat:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Adatbázisok hozzáadása egy egyéni adatbázis-gyűjtemény célja
Egy adatbázis hozzáadása egy adott egyéni gyűjtemény használja a [ **Add-AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) parancsmag.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Tekintse át az adatbázisokat egy egyéni adatbázis gyűjtemény a cél
Használja a [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) parancsmag egyéni adatbázis gyűjtemény célja a gyermek adatbázis beolvasása. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Hozzon létre egy feladatot, a parancsfájl végrehajtása egy egyéni adatbázis-gyűjtemény célja között
Használja a [ **New-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) parancsmag segítségével hozzon létre egy feladatot, egy egyéni adatbázis gyűjtemény tároló által definiált adatbázisok csoportja ellen. Rugalmas adatbázis-feladatok fog kibővítheti a feladat több gyermek feladat minden egyes adatbázis megfelelő társított egyéni adatbázis gyűjtemény célja, és győződjön meg arról, hogy a parancsfájl végrehajtása minden egyes adatbázison. Ebben az esetben fontos, hogy-e parancsfájlokat idempotent való ismételt próbálkozás rugalmasak lehetnek.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Az adatbázisok közötti adatok gyűjtése
Egy feladat használatával lekérdezés végrehajtása adatbázisok csoportja között, és az eredményt elküldik egy adott táblához. A tábla minden egyes adatbázisból a lekérdezési eredmények megtekintése érdekében bekövetkeztek kérdezhetők le. Ez a lekérdezés végrehajtása több adatbázis közötti aszinkron módszert kínál. Sikertelen bejelentkezési kísérletek újrapróbálkozások keresztül automatikusan kezeli.

A megadott célhely tábla automatikusan létrejön, ha még nem létezik. Az új táblázat felel meg a séma, a visszaadott eredményhalmaz. A parancsfájl több eredménykészlet adja vissza, ha a rugalmas adatbázis-feladatok csak küldeni az első a célként megadott táblája.

A következő PowerShell-parancsfájl hajt végre egy parancsfájlt, és összegyűjti az eredményeket egy megadott táblába. Ez a parancsfájl feltételezi, hogy egy T-SQL parancsfájl létrejött-e amelyek kimenete egy eredményhalmaz és, hogy létrejött-e egy egyéni adatbázis-gyűjtemény célja.

A parancsfájl a [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) parancsmag. A parancsfájl, a hitelesítő adatokat és a végrehajtási cél paraméterek beállításához:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Hozzon létre, és elindíthat egy feladatot a adatáttelepítések gyűjtemény esetében
A parancsfájl a [ **Start-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) parancsmag.

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>A feladat végrehajtási eseményindító ütemezése
A következő PowerShell-parancsfájl segítségével hozzon létre egy ismétlődő ütemezés szerint. A parancsfájl a perces időközt, de [ **New-AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) - DayInterval, - HourInterval, - MonthInterval, és - WeekInterval paramétereket is támogatja. Csak egyszer hajtható végre ütemezéseket is létrehozható, hogy - alkalommal.

Új ütemezés létrehozása:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Elindítható egy feladat végrehajtása ütemezéssel
Egy feladat eseményindító szeretné, hogy a feladat végrehajtása idő ütemezés szerint lehet megadni. A következő PowerShell-parancsfájl segítségével hozzon létre egy feladat eseményindító.

Használjon [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) és a következő változókat, hogy a kívánt feladat és ütemezése:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>A feladat ütemezés futtatásának leállítása ütemezett társításának megszüntetése
Megszüntetheti a feladatról feladat végrehajtása a feladat indítási keresztül, a feladat indítási távolíthatja el. Távolítsa el a feladat eseményindító megfelelően egy ütemezés használatával végrehajtott egy feladatot leállítja a [ **Remove-AzureSqlJobTrigger parancsmag**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Feladat eseményindítók ütemezést kötve beolvasása
A következő PowerShell-parancsfájl segítségével beszerzése és megjelenítése a feladat eseményindítók adott ütemezést regisztrálva.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Egy feladat kötött feladat eseményindítók beolvasása
Használjon [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) beszerzése és tartalmazó regisztrált feladat ütemezésének megjelenítése.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Az adatbázisok közötti végrehajtásra adatrétegbeli alkalmazás (DACPAC) létrehozása
Egy DACPAC létrehozásához lásd: [Adatrétegbeli alkalmazások](https://msdn.microsoft.com/library/ee210546.aspx). Egy DACPAC telepítéséhez használja a [New-AzureSqlJobContent parancsmag](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). A DACPAC a szolgáltatás elérhetőnek kell lennie. Javasoljuk, hogy a létrehozott DACPAC feltöltése az Azure Storage, és hozzon létre egy [közös hozzáférésű Jogosultságkód](../storage/common/storage-dotnet-shared-access-signature-part-1.md) a DACPAC számára.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Az adatbázisok közötti egy adatrétegbeli alkalmazás (DACPAC) végrehajtásra frissítése
Rugalmas adatbázis-feladatok belül regisztrálva meglévő DACPACs frissíthető új URI mutasson. Használja a [ **Set-AzureSqlJobContentDefinition parancsmag** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) frissítésére a meglévő DACPAC URI regisztrált DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Az adatbázisok közötti egy adatrétegbeli alkalmazás (DACPAC) alkalmazandó feladat létrehozása
Miután egy DACPAC rugalmas adatbázis-feladatok belül létrehozott, egy feladat a DACPAC alkalmazhatók a adatbázisok csoportja is létrehozható. A következő PowerShell-parancsfájl segítségével hozzon létre egy DACPAC feladatot egy egyéni gyűjtéshez. az adatbázisok között:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
