---
title: 'PowerShell: SQL Server migrálása SQL felügyelt példányra'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan telepítheti át a helyszíni SQL Serverról Azure SQL Database felügyelt példányra Azure PowerShell és a Azure Database Migration Service használatával.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77650724"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>SQL Server migrálása SQL Database felügyelt példányra PowerShell-& Azure Database Migration Service

Ebben a cikkben a **Adventureworks2016** -adatbázist a SQL Server 2005 vagy újabb rendszerű helyszíni példányra telepíti át Azure SQL Database felügyelt példányra Microsoft Azure PowerShell használatával. Az adatbázisokat áttelepítheti egy helyszíni SQL Server-példányról egy Azure SQL Database felügyelt példányra az `Az.DataMigration` Microsoft Azure PowerShell moduljának használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Hozzon létre egy erőforráscsoportot.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service egy példányában.
> * A migrálás futtatása.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk részletesen ismerteti az online és az offline áttelepítések elvégzését is.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a következőkre lesz szüksége:

* [SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (bármely kiadás).
* A **AdventureWorks2016** -adatbázis egy helyi példánya, amely [innen](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)tölthető le.
* A TCP/IP protokoll engedélyezéséhez, amely alapértelmezés szerint le van tiltva SQL Server Express telepítéssel. Engedélyezze a TCP/IP protokollt a [kiszolgáló hálózati protokolljának engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)című cikkben leírtak szerint.
* A [Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* Egy Azure SQL Database felügyelt példány. Azure SQL Database felügyelt példány létrehozásához kövesse a [Azure SQL Database felügyelt példány létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)című cikkben található részleteket.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 vagy újabb verzió letöltése és telepítése.
* A Azure Resource Manager üzemi modellel létrehozott Microsoft Azure Virtual Network, amely a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosítja Azure Database Migration Service a helyek közötti kapcsolatot a helyszíni forráskiszolgálóról.
* A helyszíni adatbázis és a séma áttelepítésének befejezett értékelése Data Migration Assistant használatával, a [SQL Server áttelepítési felmérés végrehajtása](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)című cikkben leírtak szerint.
* A `Az.DataMigration` modul (0.7.2 vagy újabb verzió) letöltése és telepítése a PowerShell-Galéria az [install-Module PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)használatával.
* Győződjön meg arról, hogy a forrás SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkeznek a [vezérlési kiszolgáló](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel.
* Annak biztosítása érdekében, hogy a cél Azure SQL Database felügyelt példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek a VEZÉRLÉSi adatbázis engedéllyel a célként Azure SQL Database felügyelt példány adatbázisain.

    > [!IMPORTANT]
    > Online áttelepítéshez már be kell állítania a Azure Active Directory hitelesítő adatait. További információkért tekintse meg az [erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatás létrehozása a portál használatával](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)című cikket.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be Microsoft Azure-előfizetésbe

Jelentkezzen be az Azure-előfizetésbe a PowerShell használatával. További információkért tekintse meg a következő cikket: [bejelentkezés Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik.

Hozzon létre egy erőforráscsoportot a [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancs használatával.

A következő példában létrehozunk egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service-példány létrehozása

Azure Database Migration Service új példányát a `New-AzDataMigrationService` parancsmag használatával hozhatja létre.
Ez a parancsmag a következő szükséges paramétereket várja:

* *Azure-erőforráscsoport neve*. A [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) paranccsal létrehozhat egy Azure-erőforráscsoportot a korábban bemutatott módon, és paraméterként megadhatja a nevét.
* *Szolgáltatás neve*. A Azure Database Migration Service kívánt egyedi szolgáltatásnév megfelelő karakterlánc.
* *Hely*. Megadja a szolgáltatás helyét. Itt adhatja meg az Azure-beli adatközpont helyét, például az USA nyugati régióját vagy Délkelet-Ázsiában.
* *SKU*. Ez a paraméter a DMS SKU-nevének felel meg. A jelenleg támogatott SKU-nevek *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Virtuális alhálózati azonosító*. Alhálózat létrehozásához használhatja a [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancsmagot.

A következő példa egy *MyDMS* nevű szolgáltatást hoz létre az *USA keleti* régiójában, a *MyVNET* nevű virtuális hálózat és egy *MySubnet*nevű alhálózat használatával. *MyDMSResourceGroup*

> [!IMPORTANT]
> Az alábbi kódrészlet egy offline áttelepítésre vonatkozik, amelyhez prémium SKU alapján nem szükséges Azure Database Migration Service példánya. Online áttelepítéshez a-SKU paraméter értékének tartalmaznia kell egy prémium SKU-t.

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

Azure Database Migration Service példány létrehozása után hozzon létre egy áttelepítési projektet. Egy Azure Database Migration Service projekthez szükség van a forrás-és a célhely kapcsolati adataira, valamint a projekt részeként áttelepíteni kívánt adatbázisok listájára.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Adatbázis-kapcsolati információs objektum létrehozása a forrás és a cél kapcsolatokhoz

A `New-AzDmsConnInfo` parancsmag használatával létrehozhat egy adatbázis-elérhetőségi adatobjektumot, amely a következő paramétereket várja:

* *ServerType*. A kért adatbázis-kapcsolatok típusa, például az SQL, az Oracle vagy a MySQL. Az SQL használata a SQL Server és az Azure SQL használatához.
* *Adatforrás*. SQL Server példány vagy Azure SQL Database példány neve vagy IP-címe.
* *AuthType*. A kapcsolatok hitelesítési típusa, amely lehet SqlAuthentication vagy WindowsAuthentication.
* *TrustServerCertificate*. Ezzel a paraméterrel megadható egy érték, amely jelzi, hogy a csatorna titkosítva van-e, miközben megkerüli a megbízhatósági kapcsolat ellenőrzéséhez szükséges tanúsítványláncot Az érték lehet `$true` vagy `$false`.

Az alábbi példa egy *MySourceSQLServer* nevű, a forráshoz SQL Server, SQL-hitelesítéssel elnevezett kapcsolattípus-objektumot hoz létre:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

A következő példa azt mutatja be, hogyan hozható létre a (z) "targetmanagedinstance.database.windows.net" nevű Azure SQL Database felügyelt példány-kiszolgáló kapcsolatok adatai SQL-hitelesítés használatával:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Adatbázisok biztosítása az áttelepítési projekthez

Hozzon létre egy `AzDataMigrationDatabaseInfo` olyan objektumot, amely az adatbázisokat a Azure Database Migration Service projekt részeként adja meg, amely a projekt létrehozására szolgáló paraméterként is megadható. A létrehozásához `AzDataMigrationDatabaseInfo`használhatja a `New-AzDataMigrationDatabaseInfo` parancsmagot.

A következő példa létrehozza a `AzDataMigrationDatabaseInfo` projektet a **AdventureWorks2016** -adatbázishoz, és hozzáadja azt a listához, amelyet paraméterként kíván megadni a projekt létrehozásához.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Projekt objektum létrehozása

Végezetül létrehozhat egy *MyDMSProject* nevű Azure Database Migration Service projektet, amely az *USA keleti* régiójában található `New-AzDataMigrationProject` , és hozzáadja a korábban létrehozott forrás-és cél-kapcsolatokat, valamint az áttelepítendő adatbázisok listáját.

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

## <a name="create-and-start-a-migration-task"></a>Áttelepítési feladat létrehozása és elindítása

Ezután hozzon létre és indítson el egy Azure Database Migration Service feladatot. Ez a feladat a forrás és a cél kapcsolati hitelesítő adatait, valamint az áttelepítendő adatbázistáblák listáját, valamint az előfeltételként létrehozott projekttel már megadott adatokat is megköveteli.

### <a name="create-credential-parameters-for-source-and-target"></a>Hitelesítőadat-paraméterek létrehozása a forrás és a cél számára

Hozzon létre kapcsolatbiztonsági hitelesítő adatokat [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektumként.

Az alábbi példa az *PSCredential* objektumok létrehozását mutatja be mind a forrás-, mind a cél kapcsolathoz, és a jelszavakat karakterlánc-változókként *$sourcePassword* és *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Biztonsági mentési fájlmegosztás objektum létrehozása

Most hozzon létre egy fájlmegosztás objektumot, amely a helyi SMB hálózati megosztást jelképezi, amelyhez Azure Database Migration Service a `New-AzDmsFileShare` parancsmag használatával elvégezheti a forrás-adatbázis biztonsági mentését.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Kiválasztott adatbázis-objektum létrehozása

A következő lépés a forrás-és cél-adatbázisok kiválasztása a `New-AzDmsSelectedDB` parancsmag használatával.

Az alábbi példa egy önálló adatbázis SQL Serverról egy Azure SQL Database felügyelt példányra való áttelepítését szemlélteti:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Ha egy teljes SQL Server-példányhoz egy Azure SQL Database felügyelt példányra van szükség, akkor az összes adatbázis a forrásból való elvégzéséhez egy hurkot kell megadnia. A következő példában $Server, $SourceUserName és $SourcePassword esetében adja meg a forrás SQL Server részleteit.

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

### <a name="sas-uri-for-azure-storage-container"></a>SAS URI az Azure Storage-tárolóhoz

Hozzon létre egy olyan változót, amely tartalmazza a SAS URI-t, amely hozzáférést biztosít a Azure Database Migration Service számára a Storage-fiók tárolójához, amelyhez a szolgáltatás feltölti a biztonságimásolat-fájlokat.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>További konfigurációs követelmények

Néhány további követelményt is el kell végeznie, de attól függően eltérnek, hogy offline vagy online áttelepítést végez.

#### <a name="offline-migrations"></a>Offline Migrálás

Csak offline áttelepítések esetén hajtsa végre a következő további konfigurációs feladatokat.

* **Válassza a bejelentkezések lehetőséget**. Hozza létre az áttelepítendő bejelentkezések listáját az alábbi példában látható módon:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > A Azure Database Migration Service jelenleg csak az SQL-bejelentkezések áttelepítését támogatja.

* **Válassza ki az ügynök feladatait**. Az áttelepíteni kívánt ügynök-feladatok listájának létrehozása az alábbi példában látható módon:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > A Azure Database Migration Service jelenleg csak a T-SQL alrendszer feladat lépéseit támogató feladatokat támogatja.

#### <a name="online-migrations"></a>Online Migrálás

Csak online Migrálás esetén hajtsa végre a következő további konfigurációs feladatokat.

* **Azure Active Directory alkalmazás konfigurálása**

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

### <a name="create-and-start-the-migration-task"></a>Az áttelepítési feladat létrehozása és elindítása

Az áttelepítési feladat létrehozásához és elindításához használja a `New-AzDataMigrationTask` parancsmagot.

#### <a name="specify-parameters"></a>Paraméterek megadása

##### <a name="common-parameters"></a>Gyakori paraméterek

Függetlenül attól, hogy offline vagy online áttelepítést végez-e, `New-AzDataMigrationTask` a parancsmag a következő paramétereket várja:

* *TaskType*. A SQL Server létrehozásához létrehozandó áttelepítési feladat típusa Azure SQL Database felügyelt példány áttelepítési típusának *MigrateSqlServerSqlDbMi* várt. 
* *Erőforráscsoport neve*. Azon Azure-erőforráscsoport neve, amelyben létre kívánja hozni a feladatot.
* *Szolgáltatásnév*. Azure Database Migration Service példány, amelyben létre kívánja hozni a feladatot.
* *Projektnév*. Azure Database Migration Service projekt neve, amelyben létre kívánja hozni a feladatot. 
* *Feladatnév*. A létrehozandó feladat neve. 
* *SourceConnection*. A forrás-SQL Server kapcsolatokat jelképező AzDmsConnInfo objektum.
* *TargetConnection*. A célként Azure SQL Database felügyelt példány-kapcsolatokat jelképező AzDmsConnInfo objektum.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a forráskiszolgálón való csatlakozáshoz.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a célkiszolgálóra való csatlakozáshoz.
* *SelectedDatabase*. A forrás-és céladatbázis-leképezést jelképező AzDataMigrationSelectedDB objektum.
* *BackupFileShare*. Az a helyi hálózati megosztást jelképező fájlmegosztás objektum, amelyet a Azure Database Migration Service a forrás-adatbázis biztonsági másolatait felhasználva.
* *BackupBlobSasUri*. Az SAS URI-ja, amely hozzáférést biztosít a Azure Database Migration Service számára a Storage-fiók tárolójához, amelyhez a szolgáltatás feltölti a biztonságimásolat-fájlokat. Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját.
* *SelectedLogins*. Az áttelepítendő kiválasztott bejelentkezések listája.
* *SelectedAgentJobs*. Az áttelepítendő kiválasztott ügynök-feladatok listája.

##### <a name="additional-parameters"></a>További paraméterek

A `New-AzDataMigrationTask` parancsmag emellett olyan paramétereket is vár, amelyek egyediek az elvégezhető áttelepítési típushoz, offline vagy online állapotú.

* **Offline áttelepítés**. Az offline áttelepítések esetében `New-AzDataMigrationTask` a parancsmag a következő paramétereket is várja:

  * *SelectedLogins*. Az áttelepítendő kiválasztott bejelentkezések listája.
  * *SelectedAgentJobs*. Az áttelepítendő kiválasztott ügynök-feladatok listája.

* **Online Migrálás**. Az online áttelepítéshez a `New-AzDataMigrationTask` parancsmag a következő paramétereket is várja.

* *AzureActiveDirectoryApp*. Active Directory alkalmazás.
* *StorageResourceID*. A Storage-fiók erőforrás-azonosítója.

#### <a name="create-and-start-an-offline-migration-task"></a>Offline áttelepítési feladat létrehozása és elindítása

Az alábbi példa egy **myDMSTask**nevű offline áttelepítési feladatot hoz létre és indít el:

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

#### <a name="create-and-start-an-online-migration-task"></a>Online áttelepítési feladat létrehozása és elindítása

Az alábbi példa egy **myDMSTask**nevű Online áttelepítési feladatot hoz létre és indít el:

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

1. Összevonja az összes áttelepítési részletet egy $CheckTask nevű változóba.

    Az áttelepítéssel kapcsolatos adatok, például a tulajdonságok, az állapot és az adatbázis-információk összevonásához használja az alábbi kódrészletet:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Használja a `$CheckTask` változót az áttelepítési feladat aktuális állapotának lekéréséhez.

    Ha a `$CheckTask` változót az áttelepítési feladat aktuális állapotának lekéréséhez szeretné használni, a feladat állapot tulajdonságának lekérdezésével figyelheti a futtatott áttelepítési feladatot, ahogy az az alábbi példában is látható:

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

## <a name="performing-the-cutover-online-migrations-only"></a>A átváltás végrehajtása (csak online Migrálás esetén)

Az online áttelepítés során az adatbázisok teljes biztonsági mentését és visszaállítását hajtja végre, majd a BackupFileShare tárolt tranzakciónaplók visszaállításával folytatja a munkát.

Ha az adatbázis egy Azure SQL Database felügyelt példányban frissül a legfrissebb adatokkal, és szinkronizálva van a forrás-adatbázissal, átváltás is elvégezheti.

A következő példa végrehajtja a cutover\migration. A felhasználó saját belátása szerint hívja meg ezt a parancsot.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service példányának törlése

Az áttelepítés befejezése után törölheti az Azure Database Migration Service példányt:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>További háttéranyagok

További információkat a további áttelepítési forgatókönyvekről (forrás/cél párok) a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/)talál.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Azure Database Migration Serviceről a cikk [Mi a Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview)című cikkben.
