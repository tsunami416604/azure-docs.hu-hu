---
title: SQL Server migrálása az Azure SQL Database felügyelt példány Database Migration Service és a PowerShell segítségével |} A Microsoft Docs
description: Megismerheti, hogyan kell áttelepíteni a helyszíni SQL Serverről az Azure SQL DB felügyelt példányába Azure PowerShell használatával.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: d83410efd26f8c2078d3abdb01d061db0b83d33d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233729"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Áttelepíteni a helyszíni SQL Server egy Azure SQL Database felügyelt példány az Azure PowerShell-lel
Ebben a cikkben telepít át a **Adventureworks2016** adatbázis visszaállítva a helyszíni példányát az SQL Server 2005 vagy fent egy Azure SQL Database felügyelt példány a Microsoft Azure PowerShell használatával. Adatbázisok migrálhatók a helyszíni SQL Server-példány, egy Azure SQL Database felügyelt példány használatával az `Az.DataMigration` a Microsoft Azure PowerShell-modulja.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Hozzon létre egy erőforráscsoportot.
> * Hozzon létre egy Azure Database Migration Service példányát.
> * Migrálási projekt létrehozása az Azure Database Migration Service egy példányát.
> * A migrálás futtatása.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk tartalmaz részletes online és offline áttelepítés elvégzéséhez.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez szüksége:

* [Az SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (bármely kiadás esetén).
* Egy helyi példányát a **AdventureWorks2016** adatbázis, amely letölthető [Itt](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Ahhoz, hogy a TCP/IP protokoll, amely az SQL Server Express telepítése alapértelmezés szerint le van tiltva. Engedélyezze a TCP/IP protokollt a következő cikk [engedélyezheti vagy tilthatja le a kiszolgálói hálózati protokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Konfigurálhatja a [hozzáféréshez a Windows tűzfal](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* Az Azure SQL Database felügyelt példánya. Létrehozhat egy Azure SQL Database felügyelt példány a következő a részletek a cikkben [hozzon létre egy Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Töltse le és telepítse [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 vagy újabb.
* Az Azure Virtual Network (VNet) használatával az Azure Resource Manager üzembe helyezési modell, amely biztosítja az Azure Database Migration Service-helyek közötti kapcsolatok a helyszíni adatforrás-kiszolgálók használatával létrehozott [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* A Data Migration Assistant használata című cikkben leírtak szerint a helyi adatbázis és séma áttelepítés egy befejezett értékelést [egy SQL Server migrálási felmérést végez](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Töltse le és telepítse a `Az.DataMigration` modul (0.7.2 verzió vagy újabb) a PowerShell-galériából használatával [Install-Module PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Győződjön meg arról, hogy rendelkezik-e az SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatokat, a [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel.
* Annak érdekében, hogy a cél Azure SQL-adatbázishoz való kapcsolódáshoz használt hitelesítő adatokat a felügyelt példány az adatbázis VEZÉRLÉSE engedéllyel rendelkezik a cél Azure SQL Database felügyelt példányok adatbázisai.

    > [!IMPORTANT]
    > Az online áttelepítéshez előkészít kell az Azure Active Directory hitelesítő adatok beállítása. További információkért tekintse meg a cikket [a portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be a Microsoft Azure-előfizetés

Jelentkezzen be az Azure-előfizetéshez PowerShell használatával. További információkért tekintse meg a cikket [jelentkezzen be az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure erőforrások üzembe helyezése és felügyelt.

Hozzon létre egy erőforráscsoportot a használatával a [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsot.

A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* a a *USA keleti Régiójában* régióban.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service-példány létrehozása

Azure Database Migration Service új példányának használatával hozhat létre a `New-AzDataMigrationService` parancsmagot.
Ennek a parancsmagnak a következő szükséges paraméterek:

* *Azure erőforráscsoport-nevet*. Használhat [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancs használatával hozzon létre egy Azure-erőforráscsoportot előzőekben ismertetettek szerint, és adja meg a paramétert a neve.
* *Szolgáltatásnév*. Karakterlánc, amely megfelel az Azure Database Migration Service a szolgáltatás kívánt egyedi nevét.
* *Hely*. A szolgáltatás helyét adja meg. Adjon meg egy Azure adatokat szolgáltató adatközpont elhelyezkedése, például az USA nyugati RÉGIÓJA vagy Délkelet-Ázsia.
* *Termékváltozat*. Ez a paraméter DMS Sku-név tartozik. Jelenleg támogatott Sku-nevek a következők *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Virtuális alhálózati azonosító*. A parancsmagot használhatja [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) alhálózat létrehozását.

A következő példában létrehozunk egy elnevezett szolgáltatás *MyDMS* erőforráscsoportban *MyDMSResourceGroup* található a *USA keleti Régiójában* nevűvirtuálishálózatothasználórégió *MyVNET* és a egy nevű alhálózatot *MySubnet*.

> [!IMPORTANT]
> Az alábbi kódrészlet olyan offline áttelepítés, amely nem igényel Azure Database Migration Service prémium szintű Termékváltozat alapján egy példányát. Egy online migrálás esetében - Sku paraméter értékét meg kell adni egy prémium szintű Termékváltozat.

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Miután létrehozott egy Azure Database Migration Service-példányt, hozzon létre egy migrálási projektet. Az Azure Database Migration Service-projekt kapcsolatadatok mind a forrás és cél-példányok, valamint az, hogy a projekt részeként migrálni kívánt adatbázisok listájának kell rendelkeznie.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Adatbázis-kapcsolati adatok objektum a forrás- és kapcsolatok létrehozása

Egy adatbázis-kapcsolati adatok objektum használatával is létrehozhat a `New-AzDmsConnInfo` parancsmagot, amely a következő paramétert vár:

* *ServerType*. Adatbázis-kapcsolatot kért, például az SQL, Oracle vagy MySQL típusú. SQL az SQL Server és az Azure SQL használata.
* *Adatforrás*. Név vagy IP-címét egy SQL Server-példányt vagy egy Azure SQL Database-példányban.
* *Érvénytelen AuthType*. A kapcsolat, amely lehet SqlAuthentication vagy WindowsAuthentication hitelesítési típusa.
* *TrustServerCertificate*. Ez a paraméter beállítása egy értéket, amely azt jelzi, hogy a csatorna titkosítva legyen-e megkerüli a the walking a tanúsítványlánc megbízható ellenőrzése közben. Az érték lehet `$true` vagy `$false`.

A következő példában létrehozunk egy kapcsolati adatok objektum a forrás SQL Server nevű *MySourceSQLServer* sql-hitelesítés használatával:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

A következő példa bemutatja egy Azure SQL Database felügyelt példány kiszolgáló sql-hitelesítéssel targetmanagedinstance.database.windows.net nevű kapcsolati adatok létrehozása:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Adja meg az adatbázisokat a migrálási projekt

Hozzon létre egy `AzDataMigrationDatabaseInfo` objektumokat, amely meghatározza az adatbázis részeként az Azure Database Migration Service projekt, amely adható meg paraméterként, a projekt létrehozásához. A parancsmagot használhatja `New-AzDataMigrationDatabaseInfo` létrehozása `AzDataMigrationDatabaseInfo`.

Az alábbi példa a `AzDataMigrationDatabaseInfo` project számára a **AdventureWorks2016** adatbázis, és hozzáadja azt a listát a projekt létrehozásához paraméterként megadott.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Projekt-objektum létrehozása

Végül, létrehozhat egy Azure Database Migration Service nevű projektet *MyDMSProject* található *USA keleti Régiójában* használatával `New-AzDataMigrationProject` , és adja hozzá a korábban létrehozott forrás- és kapcsolatok és a migrálni kívánt adatbázisok listája.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Hozzon létre, és indítsa el áttelepítési feladatot

Ezután hozzon létre, és a egy Azure Database Migration Service-feladat indítása. A feladathoz szükséges, mind a forrás és cél, valamint a adatbázis táblákat át kell a kapcsolati hitelesítő adatok és az előfeltételként létrehozott projekt már megadott információkat.

### <a name="create-credential-parameters-for-source-and-target"></a>Forrás és a cél hitelesítő adat paramétereinek létrehozása

Kapcsolat létrehozása a hitelesítő adatokat egy [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum.

Az alábbi példa bemutatja *PSCredential* objektumokat a forrás és a cél-kapcsolatok, karakterlánc típusú változót jelszavakat nyújtó *$sourcePassword* és *$ targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Hozzon létre egy biztonsági mentési FileShare objektumot

Most hozzon létre egy fájlmegosztás objektumot képviselő a helyi SMB hálózati megosztást, amelyhez az Azure Database Migration Service is igénybe vehet a forrás adatbázis biztonsági mentéseket a `New-AzDmsFileShare` parancsmagot.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Kiválasztott adatbázis-objektum létrehozása

A következő lépés az, hogy válassza ki a forrás- és adatbázisok használatával a `New-AzDmsSelectedDB` parancsmagot.

Az alábbi példában egy önálló adatbázis az SQL Serverből egy Azure SQL Database felügyelt példány való áttelepítés során van:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Ha teljes SQL Server-példány van szüksége egy lift-and-shift egy Azure SQL Database felügyelt példány, majd egy iterációt az összes adatbázis is a forrásból lejjebb tekinthetők meg. A következő példában $Server $SourceUserName és $SourcePassword, adja meg a forrás SQL Server részleteit.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Az Azure Storage-tároló SAS URI-t

Hozza létre a változót, amely tartalmazza az SAS URI-t, amelyhez a szolgáltatás a biztonsági mentési fájlokat tölt fel a tárfiók tárolójának hozzáférést biztosít az Azure Database Migration Service.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>További konfigurációs követelmények

Teljesítendő néhány további követelmények azonban eltérnek attól függően, hogy hajt végre egy online és kapcsolat nélküli áttelepítés.

#### <a name="offline-migrations"></a>Offline áttelepítés

Csak offline migrálás esetén hajtsa végre a következő további konfigurációs feladatokat.

* **Bejelentkezések kiválasztása**. Hozzon létre egy bejelentkezések a következő példában látható módon kell áttelepíteni:

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service jelenleg csak áttelepítése SQL-bejelentkezésekben támogatja.

* **Válassza ki az ügynök feladatainak**. Hozzon létre ügynök feladatok kell áttelepíteni az alábbi példában látható módon:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service jelenleg csak a T-SQL alrendszer feladat lépéseket feladatok támogatja.

#### <a name="online-migrations"></a>Online áttelepítések

Csak online migrálás esetén hajtsa végre a következő további konfigurációs feladatokat.

* **Az Azure Active Directory-alkalmazás konfigurálása**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Storage-erőforrások konfigurálása**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Hozzon létre, és az áttelepítési feladat indítása

Használja a `New-AzDataMigrationTask` parancsmag segítségével hozzon létre egy áttelepítési feladat elindításához.

#### <a name="specify-parameters"></a>Adja meg a paramétereket

##### <a name="common-parameters"></a>Az általános paraméterek

Függetlenül attól, hogy hajt végre egy online és kapcsolat nélküli áttelepítés a `New-AzDataMigrationTask` a parancsmagnak a következő paraméterekkel:

* *TaskType*. Áttelepítési feladat létrehozása az SQL Server az Azure SQL Database felügyelt példányába migrálási típus típusú *MigrateSqlServerSqlDbMi* várt. 
* *Erőforráscsoport-nevet*. Azure-erőforráscsoportot, amelyben létrehozza a feladat nevét.
* *Szolgáltatásnév*. Azure Database Migration Service példány, amelyben a feladat létrehozásához.
* *Projektnév*. Azure Database Migration Service-projekt, amelyben létrehozza a feladat neve. 
* *Feladatnév*. A létrehozandó feladat neve. 
* *SourceConnection*. AzDmsConnInfo a forrás SQL Server közötti kapcsolat képviselő objektum.
* *TargetConnection*. AzDmsConnInfo a cél Azure SQL Database felügyelt példányába kapcsolatot képviselő objektum.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum forrás-kiszolgálójához való kapcsolódás.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a célkiszolgálóhoz való kapcsolódáshoz.
* *SelectedDatabase*. A forrás- és adatbázisok közötti képviselő AzDataMigrationSelectedDB objektum.
* *BackupFileShare*. Fájlmegosztás-objektumot a helyi hálózatot képviselő oszthat meg, hogy az Azure Database Migration Service is igénybe vehet a forrás adatbázisok biztonsági másolatait.
* *BackupBlobSasUri*. Az SAS URI-t, amelyhez a szolgáltatás a biztonsági mentési fájlokat tölt fel a tárfiók tárolójának hozzáférést biztosít az Azure Database Migration Service. Ismerje meg, hogyan tehet szert az SAS URI blob-tároló.
* *SelectedLogins*. Kiválasztott bejelentkezések áttelepítése listája.
* *SelectedAgentJobs*. Kiválasztott ügynök feladatainak áttelepítése listája.

##### <a name="additional-parameters"></a>További paraméterek

A `New-AzDataMigrationTask` parancsmag is egyedi végzett offline vagy online állapotú, az áttelepítés típusú paramétereket vár.

* **Offline áttelepítés**. Az offline áttelepítéshez a `New-AzDataMigrationTask` parancsmag is vár a következő paraméterekkel:

  * *SelectedLogins*. Kiválasztott bejelentkezések áttelepítése listája.
  * *SelectedAgentJobs*. Kiválasztott ügynök feladatainak áttelepítése listája.

* **Online áttelepítések**. Az online migrálás esetén a `New-AzDataMigrationTask` parancsmag is vár a következő paraméterekkel.

* *AzureActiveDirectoryApp*. Active Directory-alkalmazás.
* *StorageResourceID*. Storage-fiók erőforrás-azonosító.

#### <a name="create-and-start-an-offline-migration-task"></a>Hozzon létre, és a egy offline áttelepítés feladat indítása

Az alábbi példa hoz létre, és a egy nevű offline migrálás feladatot indít **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Hozzon létre, és a egy online migrálási feladat indítása

Az alábbi példa létrehozza és elindítja az online migrálási feladat nevű **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>A migrálás monitorozása

A migrálás figyelésének, a következő feladatok végrehajtására.

1. Az átállás részletei kialakíthattunk $CheckTask nevű változóra.

    Migrálás részleteit, például a tulajdonságait, állapotát és adatbázis-információ az áttelepítés társított kombinálja, használja a következő kódrészletet:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Használja a `$CheckTask` -változó lekérdezésével az áttelepítési feladat jelenlegi állapota.

    Használatához a `$CheckTask` változó az áttelepítési feladat aktuális állapotának, nyomon követheti az áttelepítési feladat fut a feladat állapota tulajdonságának lekérdezésével, az alábbi példában látható módon:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Az átállás (online áttelepítések csak) végrehajtása

Egy online migrálás egy teljes biztonsági mentés és visszaállítás az adatbázisok, és csak ezután munkahelyi folytatódik a tranzakciós naplókhoz a BackupFileShare tárolt visszaállításának.

Ha az adatbázis egy Azure SQL Database felügyelt példány a legújabb adatokkal frissül, és szinkronban vannak a forrásadatbázis, egy átállításakor is elvégezheti.

Az alábbi példa a cutover\migration fog befejeződni. Felhasználók a saját belátása szerint ez a parancs meghívása.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Az Azure Database Migration Service-példány törlése

Az áttelepítés befejezése után törölheti az Azure Database Migration Service-példány:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>További források

További áttelepítése forgatókönyvek (forrás – cél párok) kapcsolatos információkért lásd a Microsoft [adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>További lépések

A cikkben talál további információt az Azure Database Migration Service [Mi az az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
