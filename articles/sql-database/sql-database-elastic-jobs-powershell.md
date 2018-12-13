---
title: PowerShell-lel a rugalmas feladatok létrehozása és kezelése |} A Microsoft Docs
description: PowerShell az Azure SQL Database-készletek kezelésére használja
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: powershell
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: de395dc4f862e57030fba1d77de78eabe44a3da8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278457"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>PowerShell (előzetes verzió) használatával az SQL Database rugalmas feladatok létrehozása és kezelése


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


A PowerShell API-k **rugalmas adatbázis-feladatok** (az előzetes verzió), amelyekkel meghatározott felhasználói csoporttal szemben, amely végrehajtja a szkriptek adatbázisok. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet **rugalmas adatbázis-feladatok** PowerShell-parancsmagok használatával. Lásd: [rugalmas feladatok áttekintése](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Az ingyenes próbaverzióra, lásd: [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Az Elastic Database-eszközökkel létrehozott adatbázisok egy csoportja. Lásd: [Ismerkedés az Elastic Database-eszközök](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/overview).
* **Elastic Database-feladatok** PowerShell csomag: Lásd: [telepítése rugalmas adatbázis-feladatok](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetés
Válassza ki az előfizetést az előfizetés-azonosítót kell (**- SubscriptionId**) vagy az előfizetés neve (**- SubscriptionName**). Ha több előfizetéssel rendelkezik, futtathatja a **Get-AzureRmSubscription** parancsmagot, és másolja a kívánt előfizetési információkat az eredményt állítsa be. Ha már rendelkezik az előfizetés adatait, futtassa az alapértelmezett, azaz a cél a feladatok létrehozását és kezelését az előfizetés beállítása a következő parancsmagot:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

A [PowerShell ISE-ben](https://technet.microsoft.com/library/dd315244.aspx) fejlesztéséhez, és hajtsa végre a PowerShell-szkriptek használatát a rugalmas adatbázis-feladatok használata ajánlott.

## <a name="elastic-database-jobs-objects"></a>Rugalmas adatbázis-feladatok objektumok
A következő táblázat felsorolja az összes objektum típusú ki **rugalmas adatbázis-feladatok** annak leírását és a megfelelő PowerShell API-k együtt.

<table style="width:100%">
  <tr>
    <th>Objektumtípus</th>
    <th>Leírás</th>
    <th>Kapcsolódó PowerShell API-k</th>
  </tr>
  <tr>
    <td>Hitelesítő adat</td>
    <td>Felhasználónév és jelszó parancsprogramok vagy alkalmazás DACPACs az adatbázisokhoz való csatlakozáshoz használhat. <p>A jelszó küldése és az Elastic Database-feladatok adatbázisban való tárolás előtt van titkosítva.  A jelszót a rendszer visszafejti az Elastic Database-feladatok szolgáltatás a létrehozott és feltöltött a telepítési parancsfájl a hitelesítő adatok használatával.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Szkript</td>
    <td>A Transact-SQL-szkript több adatbázisban a végrehajtáshoz használandó.  A parancsfájl kell, hogy idempotensek legyenek, mivel a szolgáltatás újra megpróbálja a hibák után parancsfájlja hozhatóak létre.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>ADATRÉTEGBELI ALKALMAZÁSCSOMAG</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Adatrétegbeli alkalmazás </a> csomag több adatbázisban alkalmazni.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Adatbázis-cél</td>
    <td>Az Azure SQL Database mutató adatbázis és a kiszolgáló nevét.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Szilánkleképezés-térkép cél</td>
    <td>Egy adatbázis cél és a egy hitelesítő adatot, egy rugalmas horizontális skálázási térképet tárolt információk meghatározásához kombinációja.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Egyéni gyűjtemény célja</td>
    <td>Az adatbázisok végrehajtási együttesen használandó meghatározott csoport.</td>
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
    <p>Paraméterekkel történő aktiválásához vagy ütemezés szerint kielégítésére szolgáló feladat meghatározása.</p>
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
    <p>Hajtsa végre a parancsprogramot, vagy egy cél-hibákkal rendelkező adatbázis-kapcsolatok hitelesítő adatok használatával egy adatrétegbeli ALKALMAZÁSCSOMAGOT alkalmazása teljesítéséhez szükséges feladatokat tartalmazó tároló olyan végrehajtási szabályzatra összhangban kezeli.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>A feladat-végrehajtási feladat</td>
    <td>
    <p>A feladat teljesítéséhez munkahelyi egyedi egysége.</p>
    <p>Ha egy feladat a feladat sikeresen nem tudja hajtsa végre, az eredményül kapott kivételre vonatkozó üzenet a rendszer naplózza és a egy új, egyező feladat tevékenység létrejön és végrehajtott nevezhetnek, a megadott végrehajtási házirend.</p></p>
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
    <p>Vezérlők feladat-végrehajtási időtúllépések, az Újrapróbálkozási korlát és a próbálkozások közötti időintervallumot.</p>
    <p>Elastic Database-feladatok alapértelmezett feladat-végrehajtási házirendjét, amelyek a feladat sikertelen feladatok lényegében végtelen újrapróbálkozások miatt az exponenciális visszatartással az egyes újrapróbálkozások közötti intervallumok tartalmazza.</p>
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
    <p>Időalapú specifikáció kerül sor, vagy ütemezhet időközönként, vagy egyszerre a végrehajtáshoz.</p>
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
    <p>Egy feladat és a egy ütemezés, történő aktiválásához feladat ütemezés szerint közötti leképezéseket.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Támogatott rugalmas adatbázis-feladatok csoport típusa
A feladat végrehajtja a Transact-SQL (T-SQL) szkriptek vagy DACPACs alkalmazásának adatbázisok egy csoportja között. Adatbázisok csoportjának futtatandó feladat elküldésekor a feladat "kibővíti" a gyermekfeladatok, ahol minden egyes hajtja végre a kért futtatást a egy önálló adatbázis a csoport be. 

Csoportokat hozhat létre két típusa van: 

* [Horizontális skálázási térképet](sql-database-elastic-scale-shard-map-management.md) csoportot: Egy feladat elküldésekor, amelyekre horizontálispartíció-térkép, a feladat lekérdezi a szegmenstérkép határozza meg az aktuális szegmens körét, és majd hoz létre gyermek mindegyik szegmenshez feladatok horizontális skálázási térképet.
* Egyéni gyűjtési csoportja: Adatbázisok egyéni meghatározott készlete. Amikor egy feladat egyéni gyűjtemény célozza, hoz létre gyermek feladatokat az egyes adatbázisok jelenleg az egyéni gyűjtemény.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Állítsa be a rugalmas adatbázis-csatlakozási feladatok
A kapcsolat kell állítani a feladatok *adatbázis vezérlése* a feladatok API-k használata előtt. Ez a parancsmag futtatása aktiválja a felugró ablak a felhasználónevet és a rugalmas adatbázis-feladatok telepítésekor létrehozott jelszót kér egy hitelesítőadat-ablakban. Ebben a témakörben közölt összes példák feltételezik, hogy az első lépéshez már végbementek.

Nyissa meg a rugalmas adatbázis-feladatok kapcsolatot:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Titkosított hitelesítő adatokat az Elastic Database-feladatokban
Adatbázis-hitelesítő adatok szúrhatók be, a feladatok *adatbázis vezérlése* a jelszóval titkosított. Fontos tárolhatjuk a hitelesítő adatokat ahhoz, hogy egy későbbi időpontban végrehajtandó feladatok (feladatütemezések használatával).

Titkosítás a telepítési szkript részeként létrehozott tanúsítvány van elfoglalva. A telepítési parancsfájlt hoz létre, és feltölti a tanúsítványt visszafejtését is a tárolt titkosított jelszavakat az Azure Cloud Service-be. Az Azure Cloud Service később tárolja a nyilvános kulcsot a feladatokban *adatbázis vezérlése* lehetővé teszi a megadott jelszó titkosítását anélkül, hogy a tanúsítványt kell helyileg telepíteni a PowerShell API-t vagy az Azure portál felület .

A hitelesítő adatok jelszavakat olyan titkosított és a rugalmas adatbázis-feladatok objektumok a csak olvasási hozzáféréssel rendelkező felhasználók biztonságos. Azonban lehetséges, hogy egy rosszindulatú felhasználó olvasási és írási hozzáféréssel az Elastic Database-feladatok objektumok bontsa ki a jelszót. Hitelesítő adatok is felhasználják a feladat-végrehajtások tervezték. Hitelesítő adatok továbbítódnak céladatbázis kapcsolatok létesítéséhez. Jelenleg nem vonatkoznak korlátozások az egyes hitelesítő adatokat használni a céladatbázisok, rosszindulatú felhasználó sikerült felvenni a rosszindulatú felhasználó kezében egy adatbázis egy adatbázis cél. A felhasználó ezt követően sikerült elindítani egy feladatot, ezt az adatbázist a hitelesítő adatok jelszó szerezhet célzó.

Ajánlott biztonsági eljárások a rugalmas adatbázis-feladatok a következők:

* Az API-k felhasználását korlátozza megbízható személyekre.
* Hitelesítő adatokkal kell rendelkeznie a minimálisan szükséges a tevékenység végrehajtásához.  További információk láthatók belül ez [engedélyezési és engedélyek](https://msdn.microsoft.com/library/bb669084.aspx) SQL Server MSDN-cikkben.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Több adatbázisban egy titkosított hitelesítő adatokat, a feladat végrehajtása létrehozása
Egy új titkosított hitelesítő adat létrehozása a [ **Get-Credential parancsmag** ](/powershell/module/microsoft.powershell.security/get-credential) kérni fogja a felhasználónevet és jelszót, amely átadható a [ **New-AzureSqlJobCredential parancsmag** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Hitelesítő adatainak frissítése
Ha módosítja a jelszavak, a [ **Set-AzureSqlJobCredential parancsmag** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) és állítsa be a **CredentialName** paraméter.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Az Elastic Database szegmens térkép cél meghatározása
Egy szegmens készlet szemben az összes adatbázist feladat végrehajtásához (használatával létrehozott [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)), használja a horizontálispartíció-térkép célhelyként adatbázis. Ebben a példában az Elastic Database ügyfélkódtár használatával létrehozott szilánkokra osztott alkalmazás szükséges. Lásd: [Ismerkedés az Elastic Database-eszközök minta](sql-database-elastic-scale-get-started.md).

A szegmenstérkép-kezelő adatbázis állítson be egy adatbázis célként, és majd a különleges szegmenstérkép meg kell adni célként.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Hozzon létre egy T-SQL parancsfájl végrehajtása több adatbázisban
Amikor létrehozza a T-SQL parancsfájl-végrehajtás, mindenképpen ajánlatos hozhat létre, ha meg [idempotens](https://en.wikipedia.org/wiki/Idempotence) és rugalmas meghibásodásokkal szemben. Elastic Database-feladatok egy parancsfájl végrehajtásának próbálkozik újra, ha végrehajtási hiba, függetlenül a besorolás, a hiba fordul elő.

Használja a [ **New-AzureSqlJobContent parancsmag** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) hozhat létre, és mentse a parancsfájl végrehajtása és a beállított a **- ContentName** és **- CommandText** a paraméterek.

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
Ha egy fájlt a T-SQL-szkript van meghatározva, ennek használatával importálja a parancsfájlt:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Több adatbázisban egy T-SQL parancsfájl végrehajtási frissítése
Ez a PowerShell-szkript frissíti a T-SQL parancs szövege a következő egy meglévő parancsfájl.

Állítsa be az alábbi változókat, hogy a kívánt szkriptet definíciójának kell beállítani:

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

### <a name="to-update-the-definition-to-an-existing-script"></a>A definíció frissítésének az egy meglévő parancsfájl
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Létrehoz egy feladatot a parancsfájl végrehajtása horizontálispartíció-térkép között
A PowerShell-szkript elindít egy feladatot, a szkript végrehajtásához egy rugalmasan méretezhető szegmenstérkép az egyes szegmensek között.

Állítsa be a kívánt parancsfájlt, és a cél megfelelően az alábbi változókat:

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
A PowerShell-parancsprogram egy meglévő feladat végrehajtása:

A következő változót kell végrehajtani a kívánt feladat neve megfelelően frissítse:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Egy egyetlen feladat-végrehajtási állapotának lekéréséhez
Használja a [ **Get-AzureSqlJobExecution parancsmag** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) és állítsa be a **JobExecutionId** paraméter segítségével megtekintheti a feladat-végrehajtási állapotát.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Ugyanaz, mint **Get-AzureSqlJobExecution** parancsmagot a **IncludeChildren** paramétert gyermek feladatvégrehajtások, nevezetesen a meghatározott állapotban minden egyes adatbázison feladat-végrehajtási állapotának megtekintése a feladat által megcélzott.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Az állapot megtekintéséhez között több feladat-végrehajtások
A [ **Get-AzureSqlJobExecution parancsmag** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) több választható paraméterek: több feladatvégrehajtások szűrt keresztül megadott paraméterek megjelenítéséhez használható. A következő mutat be néhányat. használja a Get-AzureSqlJobExecution lehetséges módja:

Kérje le az összes aktív felső szintű feladat-végrehajtások:

    Get-AzureSqlJobExecution

Minden felső szintű feladat céljából, beleértve az inaktív feladat-végrehajtások lekéréséhez:

    Get-AzureSqlJobExecution -IncludeInactive

A megadott feladat végrehajtási Azonosítóhoz, beleértve az inaktív feladat-végrehajtások az összes gyermek feladatvégrehajtások lekéréséhez:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Egy ütemezés használatával létrehozott összes feladat-végrehajtások beolvasása / feladat-kombináció, beleértve az inaktív feladatok:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

A megadott horizontális skálázási térképet, beleértve az inaktív feladatok célzó összes feladat beolvasása:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

A megadott egyéni gyűjteményét, beleértve az inaktív feladatok célzó összes feladat beolvasása:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Belül egy adott feladat-végrehajtási feladat feladat-végrehajtások listájának beolvasása:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Kérje le a feladat feladat-végrehajtási részleteit:

A következő PowerShell-parancsfájl segítségével egy feladat a feladat a végrehajtás, amelyek akkor különösen hasznos, ha a hibakeresés végrehajtása érdekében részleteinek megtekintéséhez.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Sikertelen feladat feladat-végrehajtások belül lekéréséhez
A **JobTaskExecution objektum** a feladatot egy üzenet egyik tulajdonságát együtt élettartama tulajdonság tartalmazza. Ha egy feladat a feladat végrehajtása sikertelen volt a életciklus tulajdonságot állítja be *sikertelen* és az üzenet egyik tulajdonságát állítja be az eredményül kapott kivételre vonatkozó üzenet, és a stack. Ha egy feladat sikertelen volt, fontos, hogy egy adott feladat sikertelen feladatok részleteinek megtekintéséhez.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Várjon, amíg befejeződik a feladat végrehajtása
A következő PowerShell-parancsfájl segítségével várnia a feladat feladat végrehajtásához:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Hozzon létre egy egyéni végrehajtási házirend
Elastic Database-feladatok feladatok indításakor alkalmazható egyéni végrehajtási házirendek létrehozását támogatja.

Végrehajtási házirendek definiálása jelenleg engedélyezése:

* Név: A végrehajtási házirend azonosítója.
* Feladat időtúllépése: Teljes idő, mielőtt a feladat meg lesz szakítva Elastic Database-feladatok által.
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
* Kísérletek maximális száma: 2 147 483 647

A kívánt végrehajtási házirend létrehozása:

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
A frissíteni kívánt végrehajtási szabályzat frissítése:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Feladatok megszakítása
Elastic Database-feladatok támogatja a megszakítási kérések feladatok.  Elastic Database-feladatok egy jelenleg végrehajtás alatt feladat megszakítási kérelmet észleli, ha megkísérli leállítani a feladatot.

Elastic Database-feladatok is hajtsa végre a megszakítási két különböző módja van:

1. A Mégse gombra a jelenleg teljesítés alatt feladatokat: Ha lemondás észlel, amíg egy tevékenység fut, akkor lemondás belül a feladat jelenleg végrehajtás alatt aspektusa történt kísérlet.  Példa: Ha jelenleg lemondás megkísérelt végrehajtani egy hosszan futó lekérdezést, a lekérdezés megszakításához kísérlet lesz.
2. Tevékenység-újrapróbálkozások megszakítása: Lemondás végrehajtási feladat konfigurálása előtt a vezérlő szál észlelése esetén a vezérlőelem szál ne indítsa el a feladatot, és deklarálja a kérelmet, mert meg lett szakítva.

Ha egy feladat törlése a szülőfeladat van szükség, a megszakítási kérelemre váró a szülő feladat és az összes hozzá tartozó gyermekfeladatok lesz figyelembe.

Megszakítási kérelmet küldeni, használja a [ **Stop-AzureSqlJobExecution parancsmag** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) és állítsa be a **JobExecutionId** paraméter.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Törli a feladatot, és aszinkron módon történik a feladatelőzmények
Elastic Database-feladatok támogatja az aszinkron feladatok törlése. Egy feladat is törlésre, és a rendszer törli a feladatot és annak feladatelőzmények összes feladat-végrehajtások a feladat befejezése után. A rendszer nem automatikusan megszakítja a aktív feladat-végrehajtások.  

Meghívása [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) megszakítja az aktív feladat-végrehajtások.

A törlési feladat indításához használja a [ **Remove-AzureSqlJob parancsmag** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) és állítsa be a **JobName** paraméter.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Egy egyéni adatbázis-tároló létrehozása
Egyéni adatbázis célok közvetlen végrehajtási vagy egy egyéni adatbázis csoportban, hogy határozhatja meg. Ha például mert **rugalmas készletek** vannak még nem közvetlenül támogatott PowerShell API-k segítségével, létrehozhat egy egyéni adatbázis célként és egy egyéni adatbázis gyűjtemény cél számára, amely magában foglalja a készletben található összes adatbázishoz.

Állítsa be a kívánt adatbázis adatait tükrözik a következő változókat:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Egy egyéni adatbázis-gyűjtemény cél létrehozása
Használja a [ **New-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) parancsmag használatával adja meg egy egyéni adatbázis-gyűjtemény cél végrehajtásának engedélyezéséhez több meghatározott adatbázis-cél között. Miután létrehozott egy adatbázis-csoportban, adatbázisok társítható az egyéni gyűjtemény célja.

Állítsa be az alábbi változókat, hogy a kívánt egyéni gyűjtemény cél konfigurációját tükrözzék:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Adatbázisok hozzáadása egy egyéni adatbázis-gyűjtemény cél
Adatbázis hozzáadása egy adott egyéni gyűjtemény használja a [ **Add-AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) parancsmagot.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Tekintse át az adatbázisok egy egyéni adatbázis-gyűjtemény célon belül
Használja a [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) beolvasásához a gyermek adatbázisok egy egyéni adatbázis-gyűjtemény célon belül maradjon. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Hozzon létre egy feladatot, amely egy szkript végrehajtása egy egyéni adatbázis-gyűjtemény cél között
Használja a [ **New-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) parancsmaggal hozzon létre egy feladatot egy egyéni adatbázis-gyűjtemény cél által meghatározott adatbázisok csoportjain. Elastic Database-feladatok fog bontsa ki a feladat be több, az egyéni adatbázis-gyűjtemény cél társított minden egyes adatbázishoz tartozó gyermekfeladatok, és győződjön meg arról, hogy a parancsfájl végrehajtása minden adatbázison. Újra fontos, hogy a parancsfájlok idempotens rugalmas való próbálkozások.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Adatgyűjtés több adatbázisban
Egy feladat segítségével hajtsa végre a lekérdezést adatbáziscsoportok közötti és az eredményt elküldik egy adott táblához. A táblázat az egyes adatbázisok a lekérdezés eredményeinek megtekintéséhez utólag kérdezhetők le. Ez a lekérdezés végrehajtása több adatbázis közötti aszinkron módszert kínál. Sikertelen bejelentkezési kísérletek automatikusan kezeli, az újrapróbálkozások keresztül.

A megadott célpartícióra tábla automatikusan létrejön, ha még nem található. Az új táblázat visszaadott eredménykészlet sémája megegyezik. Ha a parancsfájl több eredménykészletet adja vissza, rugalmas adatbázis-feladatok csak küldeni az első a céltáblázatban.

A következő PowerShell-parancsfájl szkriptet hajt végre, és összegyűjti az eredményeket a megadott táblába. Ez a szkript feltételezi, hogy egy T-SQL-szkript létrejött-e egyetlen eredményhalmaz adja vissza, amely és, hogy létrejött-e egy egyéni adatbázis-gyűjtemény cél.

Ez a szkript a [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) parancsmagot. Állítsa be a parancsfájlt, a hitelesítő adatok és a végrehajtási célként paraméterek:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Hozhat létre, és a gyűjtemény adatforgatókönyvek feladat indítása
Ez a szkript a [ **Start-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) parancsmagot.

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

## <a name="to-schedule-a-job-execution-trigger"></a>Egy feladat-végrehajtási trigger ütemezése
A következő PowerShell-parancsfájl segítségével hozzon létre egy ismétlődő ütemezés szerint. Ez a szkript egy perces időszakban, de [ **New-AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) - DayInterval, - HourInterval, - MonthInterval, és - WeekInterval paraméter is támogatja. Hozható létre ütemezés, amely csak egyszer hajtható végre az átadott - Item parancsot.

Új ütemezés létrehozása:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Az eseményindítóhoz megadott idő ütemezés szerint elvégzendő feladatok
Egy feladat eseményindító szeretné, hogy a feladat végrehajtása egy ideje ütemezés szerint lehet definiálni. A következő PowerShell-parancsfájl segítségével hozzon létre egy feladatot az eseményindító.

Használat [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) , és állítsa be a következő változókat, hogy a kívánt feladatot és ütemezése:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Az egy ütemezett feladat ütemezés futtatásának leállítása társításának megszüntetése
Ismétlődő feladat-végrehajtási feladat eseményindító keresztül lemondásához, a feladat eseményindító távolíthatja el. Egy feladat a végrehajtás alatt egy ütemezés használatával megfelelően leállítása feladat eseményindító távolítsa el a [ **Remove-AzureSqlJobTrigger parancsmag**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Feladat eseményindítók ütemezést kötött beolvasása
A következő PowerShell-parancsprogram beszerzése és megjelenítése a feladat eseményindítók egy adott időpontban ütemezéshez regisztrált használható.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Feladat eseményindítók lekéréséhez kötött egy feladathoz
Használat [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) beszerzése és a egy regisztrált feladatot tartalmazó megjelenítéséhez.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Több adatbázisban egy adatrétegbeli alkalmazás (DACPAC) végrehajtási létrehozása
Egy adatrétegbeli ALKALMAZÁSCSOMAG létrehozásához lásd: [Adatrétegbeli alkalmazások](https://msdn.microsoft.com/library/ee210546.aspx). Egy ALKALMAZÁSCSOMAG üzembe helyezéséhez használja a [New-AzureSqlJobContent parancsmag](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). Az adatrétegbeli ALKALMAZÁSCSOMAGOT, a szolgáltatás elérhetővé kell tenni. Javasoljuk, hogy egy létrehozott adatrétegbeli ALKALMAZÁSCSOMAG feltöltése az Azure Storage, és hozzon létre egy [közös hozzáférésű Jogosultságkód](../storage/common/storage-dotnet-shared-access-signature-part-1.md) az adatrétegbeli ALKALMAZÁSCSOMAG esetében.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Több adatbázisban egy adatrétegbeli alkalmazás (DACPAC) végrehajtási frissítése
Elastic Database-feladatok belül regisztrált meglévő DACPACs átirányítása az új URI-k lehet frissíteni. Használja a [ **Set-AzureSqlJobContentDefinition parancsmag** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) frissíteni az adatrétegbeli ALKALMAZÁSCSOMAGOT URI-t a meglévő regisztrált adatrétegbeli ALKALMAZÁSCSOMAG:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>A feladat egy adatrétegbeli alkalmazás (DACPAC) alkalmazhatók a adatbázisok létrehozása
Egy adatrétegbeli ALKALMAZÁSCSOMAG létrehozása rugalmas adatbázis-feladatokban, után egy feladatot az adatrétegbeli ALKALMAZÁSCSOMAGOT igyekszik adatbáziscsoportok hozható létre. A következő PowerShell-parancsfájl segítségével adatrétegbeli ALKALMAZÁSCSOMAGOT feladat létrehozása egy egyéni adatbázis-gyűjtemény között:

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
