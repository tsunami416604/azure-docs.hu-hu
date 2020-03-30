---
title: 'PowerShell: Az SQL Server áttelepítése SQL felügyelt példányra'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan migrálhat a helyszíni SQL Server kiszolgálóról az Azure SQL Database által felügyelt példányra az Azure PowerShell és az Azure Database Migration Service használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650724"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>SQL Server áttelepítése SQL Database felügyelt példányba a PowerShell & Az Azure Database Migration Service szolgáltatással

Ebben a cikkben az **Adventureworks2016-adatbázist** az SQL Server 2005 vagy újabb verzió egy helyszíni példányába telepíti át egy Azure SQL Database által felügyelt példányba a Microsoft Azure PowerShell használatával. Adatbázisok áttelepítése egy helyszíni SQL Server-példány egy Azure SQL Database felügyelt `Az.DataMigration` példány a microsoft Azure PowerShell modul használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Hozzon létre egy erőforráscsoportot.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet az Azure Database Migration Service egy példányában.
> * A migrálás futtatása.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk részletesen ismerteti az online és offline áttelepítések elvégzését.

## <a name="prerequisites"></a>Előfeltételek

A lépések végrehajtásához a következőkre van szükség:

* [SQL Server 2016-os vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) verziók (bármely kiadás).
* Az **AdventureWorks2016** adatbázis helyi példánya, amely [letölthető itt](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* A TCP/IP protokoll engedélyezése, amely alapértelmezés szerint le van tiltva az SQL Server Express telepítésével. Engedélyezze a TCP/IP protokollt a [Kiszolgálóhálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)című cikkben.
* A [Windows tűzfal konfigurálása adatbázis-motor eléréséhez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* Egy Azure SQL Database felügyelt példány. Az Azure SQL Database által felügyelt példányt az [Azure SQL Database felügyelt példányának](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)létrehozása című cikk ben található részletek követésével hozhatja létre.
* Az [Adatáttelepítési segéd](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióinak letöltése és telepítése.
* Az Azure Resource Manager telepítési modelljével létrehozott Microsoft Azure virtuális hálózat, amely az Azure Database Migration Service számára az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyről helyekre történő kapcsolatot a helyszíni forráskiszolgálókhoz.
* A helyszíni adatbázis és sémaáttelepítés kitöltött felmérése az Adatáttelepítési segéd használatával, az [SQL Server áttelepítési felmérésének végrehajtása](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)című cikkben leírtak szerint.
* A modul (0.7.2-es vagy újabb verzió) letöltése és telepítése a PowerShell-galériából az `Az.DataMigration` [Install-Module PowerShell parancsmag](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)használatával.
* Annak ellenőrzése, hogy a forrásSQL Server-példányhoz való csatlakozáshoz használt hitelesítő adatok [rendelkeznek-e CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel.
* Győződjön meg arról, hogy a hitelesítő adatok az Azure SQL Database felügyelt példánya célhoz való csatlakozáshoz használt hitelesítő adatok rendelkeznek a CONTROL DATABASE engedéllyel a cél Azure SQL Database felügyelt példány-adatbázisokhoz.

    > [!IMPORTANT]
    > Az online áttelepítések, már be kell állítania az Azure Active Directory hitelesítő adatait. További információ: A [portál használata az erőforrásokhoz hozzáférő Azure AD-alkalmazás és egyszerű szolgáltatás létrehozásáról](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)című témakörben olvashat.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Bejelentkezés Microsoft Azure-előfizetésbe

Jelentkezzen be Azure-előfizetésbe a PowerShell használatával. További információt a Bejelentkezés az [Azure PowerShellhasználatával című cikkben talál.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport egy logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése.

Hozzon létre egy [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) erőforráscsoportot a paranccsal.

A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *USA keleti régiójában.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Az Azure Database Migration Service példányának létrehozása

Az Azure Database Migration Service új példányát a `New-AzDataMigrationService` parancsmag használatával hozhatja létre.
Ez a parancsmag a következő szükséges paramétereket várja:

* *Az Azure Resource Group neve*. A parancs [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) segítségével létrehozhat egy Azure Resource group, mint korábban látható, és adja meg a nevét paraméterként.
* *A szolgáltatás neve*. Karakterlánc, amely megfelel az Azure Database Migration Service kívánt egyedi szolgáltatásnevének.
* *Hely*. A szolgáltatás helyét adja meg. Adjon meg egy Azure-adatközpont-helyet, például USA nyugati régióját vagy Délkelet-Ázsiát.
* *Sku*. Ez a paraméter a DMS Sku nevének felel meg. A jelenleg támogatott skunevek *a következők: Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Virtuális alhálózati azonosító*. Alhálózat létrehozásához használhatja a parancsmamot. [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig)

A következő példa létrehoz egy *MyDMS* nevű szolgáltatást az USA *keleti régiójában* található *MyDMSResourceGroup* erőforráscsoportban egy *MyVNET* nevű virtuális hálózat és egy *MySubnet*nevű alhálózat használatával.

> [!IMPORTANT]
> Az alábbi kódrészlet egy offline áttelepítéshez kapcsolódik, amely nem igényel egy prémium termékváltozaton alapuló Azure Database Migration Service példányt. Online áttelepítés esetén a -Sku paraméter értékének tartalmaznia kell egy prémium termékváltozatot.

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

Az Azure Database Migration Service-példány létrehozása után hozzon létre egy áttelepítési projektet. Az Azure Database Migration Service projekt csatlakozási adatokat igényel mind a forrás-, mind a célpéldányok, valamint a projekt részeként áttelepíteni kívánt adatbázisok listáját.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Adatbázis-kapcsolatinformáció-objektum létrehozása a forrás- és célkapcsolatokhoz

Az adatbázis-kapcsolat információja objektumot `New-AzDmsConnInfo` a parancsmag segítségével hozhatja létre, amely a következő paramétereket várja:

* *ServerType*. A kért adatbázis-kapcsolat típusa, például SQL, Oracle vagy MySQL. Sql használata az SQL Server és az Azure SQL.
* *Adatforrás .* Egy SQL Server-példány vagy az Azure SQL Database-példány neve vagy IP-címe.
* *AuthType*. A kapcsolat hitelesítési típusa, amely lehet SqlAuthentication vagy WindowsAuthentication.
* *TrustServerCertificate .* Ez a paraméter beállít egy értéket, amely azt jelzi, hogy a csatorna titkosítva van-e, miközben megkerüli a tanúsítványláncot a megbízhatóság érvényesítéséhez. Az érték `$true` lehet `$false`vagy .

A következő példa létrehoz egy Kapcsolatinformáció objektumot egy *MySourceSQLServer* nevű forrásSQL-kiszolgálóhoz sql hitelesítés használatával:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

A következő példa bemutatja a kapcsolatinformáció létrehozását egy "targetmanagedinstance.database.windows.net" nevű Azure SQL Database felügyelt példánykiszolgálóhoz sql-hitelesítés sel:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Adatbázisok biztosítása az áttelepítési projekthez

Hozzon létre `AzDataMigrationDatabaseInfo` egy objektumlistát, amely az Azure Database Migration Service projekt részeként határozza meg az adatbázisokat, amelyek paraméterként adhatók meg a projekt létrehozásához. A parancsmag `New-AzDataMigrationDatabaseInfo` segítségével hozhat `AzDataMigrationDatabaseInfo`létre.

A következő példa `AzDataMigrationDatabaseInfo` létrehozza a projektet az **AdventureWorks2016** adatbázishoz, és hozzáadja a projekt létrehozásához paraméterként megadandó listához.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Projektobjektum létrehozása

Végül létrehozhat egy Azure Database Migration Service projekt nevű *MyDMSProject* található *USA keleti részén* használatával, `New-AzDataMigrationProject` és adja hozzá a korábban létrehozott forrás- és célkapcsolatok és az áttelepítendő adatbázisok listáját.

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

## <a name="create-and-start-a-migration-task"></a>Áttelepítési feladat létrehozása és indítása

Ezután hozzon létre és indítson el egy Azure Database Migration Service feladatot. Ehhez a feladathoz mind a forráshoz, mind a célhoz, valamint az áttelepítendő adatbázistáblák listájához és az előfeltételként létrehozott projekthez már megadott adatokra van szükség.

### <a name="create-credential-parameters-for-source-and-target"></a>Hitelesítő adatok paramétereinek létrehozása a forráshoz és a célhoz

Hozzon létre kapcsolati biztonsági hitelesítő adatokat [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektumként.

A következő példa *pscredential* objektumok létrehozását mutatja be mind a forrás- és a célkapcsolatokhoz, a jelszavakat karakterlánc-változóként *$sourcePassword* és *$targetPassword.*

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Biztonsági másolat fileshare objektumának létrehozása

Most hozzon létre egy FileShare objektumot, amely a helyi SMB hálózati megosztást képviseli, amelyre az Azure Database Migration Service a parancsmag használatával biztonsági másolatot készíthet a `New-AzDmsFileShare` forrásadatbázisról.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Kijelölt adatbázis-objektum létrehozása

A következő lépés a forrás- és céladatbázisok `New-AzDmsSelectedDB` kiválasztása a parancsmag használatával.

A következő példa egyetlen adatbázis áttelepítése az SQL Serverkiszolgálóról egy Azure SQL Database felügyelt példányba:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Ha egy teljes SQL Server-példánynak szüksége van egy azure-sql-adatbázis által felügyelt példányra való feloldásra és váltásra, akkor az alábbiakban egy hurok at a forrásból az összes adatbázis hoz. A következő példában $Server, $SourceUserName és $SourcePassword esetén adja meg a forrás SQL Server adatait.

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

### <a name="sas-uri-for-azure-storage-container"></a>SAS URI az Azure storage tárolóhoz

Hozzon létre egy SAS-URI-t tartalmazó változót, amely hozzáférést biztosít az Azure Database Migration Service számára ahhoz a tárfiók-tárolóhoz, amelybe a szolgáltatás feltölti a biztonsági mentési fájlokat.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>További konfigurációs követelmények

Van néhány további követelmény, amelyet meg kell oldania, de ezek attól függően változnak, hogy offline vagy online áttelepítést hajt-e végre.

#### <a name="offline-migrations"></a>Offline áttelepítések

Csak kapcsolat nélküli áttelepítésesetén hajtsa végre a következő további konfigurációs feladatokat.

* **Válassza a bejelentkezések lehetőséget**. Az áttelepítendő bejelentkezések listájának létrehozása az alábbi példában látható módon:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Jelenleg az Azure Database Migration Service csak az SQL-bejelentkezések áttelepítését támogatja.

* **Válassza ki az ügyintézői feladatokat.** Az áttelepítendő ügynökfeladatok listájának létrehozása az alábbi példában látható módon:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Jelenleg az Azure Database Migration Service csak a T-SQL alrendszer feladatlépéseit tartalmazó feladatokat támogatja.

#### <a name="online-migrations"></a>Online áttelepítések

Csak online áttelepítésesetén hajtsa végre a következő további konfigurációs feladatokat.

* **Az Azure Active Directory alkalmazás konfigurálása**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Tárolási erőforrás konfigurálása**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Az áttelepítési feladat létrehozása és indítása

A `New-AzDataMigrationTask` parancsmag segítségével hozzon létre és indítson el egy áttelepítési feladatot.

#### <a name="specify-parameters"></a>Paraméterek megadása

##### <a name="common-parameters"></a>Gyakori paraméterek

Függetlenül attól, hogy offline vagy online `New-AzDataMigrationTask` áttelepítést hajt-e végre, a parancsmag a következő paramétereket várja:

* *TaskType*( Feladattípus ) . Az SQL Server és az Azure SQL Database felügyelt példányáttelepítési *típusa, a MigrateSqlServerSqlDbMi* típusú áttelepítési típusú áttelepítési típusú áttelepítési feladat várható. 
* *Erőforráscsoport neve*. A feladat létrehozásához tartozó Azure-erőforráscsoport neve.
* *A szolgáltatásneve*. Az Azure Database Migration Service példány, amelyben a feladat létrehozásához.
* *Projektnév*. Az Azure Database Migration Service projekt neve, amelyben a feladat létrehozásához. 
* *Feladatneve*. A létrehozandó feladat neve. 
* *Forráskapcsolat*. ADmsConnInfo objektum, amely a forrás SQL Server-kapcsolatot jelöli.
* *TargetConnection*. AzDmsConnInfo objektum, amely a cél Azure SQL Database felügyelt példánykapcsolatát jelöli.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a forráskiszolgálóhoz való csatlakozáshoz.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a célkiszolgálóhoz való csatlakozáshoz.
* *SelectedDatabase*( Kijelölt adatbázis ) . A forrás- és céladatbázis-leképezést képviselő AzDataMigrationSelectedDB objektum.
* *BackupFileShare*. Az Azure Database Migration Service által a forrásadatbázis biztonsági másolatait átvihető helyi hálózati megosztást képviselő FileShare objektum.
* *BackupBlobSasUri*. A SAS URI, amely az Azure Database Migration Service hozzáférést biztosít a tárfiók tároló, amelya szolgáltatás feltölti a biztonsági mentési fájlokat. Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját.
* *SelectedLogins*. Az áttelepítendő kijelölt bejelentkezések listája.
* *SelectedAgentJobs*. Az áttelepítendő kijelölt ügyintézői feladatok listája.

##### <a name="additional-parameters"></a>További paraméterek

A `New-AzDataMigrationTask` parancsmag olyan paramétereket is vár, amelyek egyediek az offline vagy online áttelepítés típusától.

* **Offline áttelepítések**. Offline áttelepítésesetén a `New-AzDataMigrationTask` parancsmag a következő paramétereket is elvárja:

  * *SelectedLogins*. Az áttelepítendő kijelölt bejelentkezések listája.
  * *SelectedAgentJobs*. Az áttelepítendő kijelölt ügyintézői feladatok listája.

* **Online áttelepítések**. Az online áttelepítések `New-AzDataMigrationTask` esetén a parancsmag a következő paramétereket is elvárja.

* *Az AzureActiveDirectoryApp*. Active Directory alkalmazás.
* *StorageResourceID*azonosítóval. A tárfiók erőforrásazonosítója.

#### <a name="create-and-start-an-offline-migration-task"></a>Kapcsolat nélküli áttelepítési feladat létrehozása és indítása

A következő példa létrehoz és elindít egy **myDMSTask**nevű offline áttelepítési feladatot:

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

#### <a name="create-and-start-an-online-migration-task"></a>Online áttelepítési feladat létrehozása és indítása

A következő példa létrehoz és elindít egy **myDMSTask**nevű online áttelepítési feladatot:

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

Az áttelepítés figyeléséhez hajtsa végre a következő feladatokat.

1. Az áttelepítés összes részletének egyesítése egy $CheckTask nevű változóba.

    Az áttelepítésrészleteinek, például tulajdonságoknak, állapotnak és adatbázis-adatoknak az egyesítéséhez használja a következő kódrészletet:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Használja `$CheckTask` a változót az áttelepítési feladat aktuális állapotának lekérése.

    Ha a `$CheckTask` változót az áttelepítési feladat aktuális állapotának lekérése érdekében szeretné leadni, figyelheti a futó áttelepítési feladatot a feladat állapottulajdonságának lekérdezésével, ahogy az a következő példában látható:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Az átállás végrehajtása (csak online áttelepítések esetén)

Az online áttelepítés sel az adatbázisok teljes biztonsági mentése és visszaállítása történik, majd a BackupFileShare-ben tárolt tranzakciónaplók visszaállításának folytatása.

Ha az Azure SQL Database felügyelt példányában lévő adatbázis frissül a legújabb adatokkal, és szinkronban van a forrásadatbázissal, elvégezheti az újraésszel.

A következő példa befejezi az átállást\áttelepítés. A felhasználók saját belátásuk szerint hivatkoznak erre a parancsra.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Az Azure Database Migration Service példányának törlése

Az áttelepítés befejezése után törölheti az Azure Database Migration Service-példányt:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>További források

További áttelepítési forgatókönyvekről (forrás-/célpárokról) a Microsoft [adatbázis-áttelepítési útmutatójában talál további tudnivalókat.](https://datamigration.microsoft.com/)

## <a name="next-steps"></a>További lépések

További információ az Azure database migration service szolgáltatásról a [Mi az Azure-adatbázis-áttelepítési szolgáltatás?](https://docs.microsoft.com/azure/dms/dms-overview)
