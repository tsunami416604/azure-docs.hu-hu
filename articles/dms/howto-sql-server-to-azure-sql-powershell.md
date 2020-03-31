---
title: 'Powershell: Sql Server áttelepítése SQL-adatbázisba'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan migrálhat a helyszíni SQL Server ről az Azure SQL Database-adatbázisra az Azure PowerShell és az Azure Database Migration Service használatával.
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
ms.openlocfilehash: f63f79402b457017257f1762c6ddc7e04c0ee1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650690"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Sql Server áttelepítése a helyszínen az Azure SQL Database-be az Azure PowerShell használatával

Ebben a cikkben az **Adventureworks2012** adatbázist az SQL Server 2016 vagy újabb, az SQL Server 2016 vagy újabb egy helyszíni példányába telepíti át egy Azure-beli SQL-adatbázisba a Microsoft Azure PowerShell használatával. Adatbázisok áttelepítése egy helyszíni SQL Server-példányból az Azure SQL `Az.DataMigration` Database a Microsoft Azure PowerShell modul használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Hozzon létre egy erőforráscsoportot.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Hozzon létre egy áttelepítési projektet egy Azure Database Migration Service-példányban.
> * A migrálás futtatása.

## <a name="prerequisites"></a>Előfeltételek

A lépések végrehajtásához a következőkre van szükség:

* [SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) verzió (bármely kiadás)
* A TCP/IP protokoll engedélyezése, amely alapértelmezés szerint le van tiltva az SQL Server Express telepítésével. Engedélyezze a TCP/IP protokollt a [Kiszolgálóhálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)című cikkben.
* A [Windows tűzfal konfigurálása adatbázis-motor eléréséhez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Egy Azure SQL Database-példány. Azure SQL Database-példányt az Azure Portalon létrehozott [Azure SQL-adatbázis létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)című cikk részleteinek követésével hozhat létre.
* [Adatáttelepítési segéd](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióval.
* Microsoft Azure virtuális hálózat létrehozása az Azure Resource Manager telepítési modelljével, amely az Azure Database Migration Service számára helyről helyekre történő kapcsolatot biztosít a helyszíni forráskiszolgálókhoz [az ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [a VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával.
* A helyszíni adatbázis és a sémaáttelepítés kitöltésének befejezése az Adatáttelepítési segéd használatával az [SQL Server áttelepítési felmérésének végrehajtása](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) című cikkben leírtak szerint
* Az Az.DataMigration modul letöltése és telepítése a PowerShell-galériából az [Install-Module PowerShell parancsmag](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)használatával ; győződjön meg róla, hogy megnyitja a powershell parancsablakot a rendszergazdaként futtatás használatával.
* Annak ellenőrzése, hogy a forrásSQL Server-példányhoz való csatlakozáshoz használt hitelesítő adatok [rendelkeznek-e CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel.
* Győződjön meg arról, hogy a hitelesítő adatok az Azure SQL DB-példány célhoz való csatlakozáshoz használt hitelesítő adatok rendelkeznek a CONTROL DATABASE engedéllyel a cél Azure SQL Database-adatbázisok.
* Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Bejelentkezés Microsoft Azure-előfizetésbe

A Bejelentkezés az [Azure PowerShelllel](https://docs.microsoft.com/powershell/azure/authenticate-azureps) című cikkben található útvonaltervek kel bejelentkezve az Azure-előfizetésbe a PowerShell használatával.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, mielőtt virtuális gépet hozna létre.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) paranccsal.

A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* régióban.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Az Azure Database Migration Service példányának létrehozása

Az Azure Database Migration Service új példányát a `New-AzDataMigrationService` parancsmag használatával hozhatja létre. Ez a parancsmag a következő szükséges paramétereket várja:

* *Az Azure Resource Group neve*. A [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) paranccsal létrehozhatja az Azure Resource group ot, ahogy korábban korábban megjelenítették, és paraméterként megadhatja a nevét.
* *A szolgáltatás neve*. Az Azure Database Migration Service kívánt egyedi szolgáltatásnevének megfelelő karakterlánc 
* *Hely*. A szolgáltatás helyét adja meg. Adjon meg egy Azure-adatközpont-helyet, például USA nyugati régióját vagy Délkelet-Ázsiát
* *Sku*. Ez a paraméter a DMS Sku nevének felel meg. A jelenleg támogatott Sku név *GeneralPurpose_4vCores*.
* *Virtuális alhálózati azonosító*. [A New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancsmag használatával alhálózatot hozhat létre. 

A következő példa létrehoz egy *MyDMS* nevű szolgáltatást az USA *keleti régiójában* található *MyDMSResourceGroup* erőforráscsoportban a *MyVNET* és a *MySubnet*nevű alhálózat használatával.

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

Adatbázis-kapcsolatinformáció-objektumot a `New-AzDmsConnInfo` parancsmag segítségével hozhat létre. Ez a parancsmag a következő paramétereket várja:

* *ServerType*. A kért adatbázis-kapcsolat típusa, például SQL, Oracle vagy MySQL. Sql használata az SQL Server és az Azure SQL.
* *Adatforrás .* Egy SQL Server-példány vagy Az Azure SQL-adatbázis neve vagy IP-címe.
* *AuthType*. A kapcsolat hitelesítési típusa, amely lehet SqlAuthentication vagy WindowsAuthentication.
* *A TrustServerCertificate* paraméter beállít egy értéket, amely azt jelzi, hogy a csatorna titkosítva van-e, miközben a tanúsítványláncon keresztül a megbízhatóság érvényesítéséhez lép. Az érték lehet igaz vagy hamis.

A következő példa létrehozza a Connection Info objektumot a Forrás SQL Server nevű MySourceSQLServer sql hitelesítés használatával:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

A következő példa a Kapcsolati adatok létrehozását mutatja be egy SQLAzureTarget nevű Azure SQL database server rendszerhez sql-hitelesítés használatával:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Adatbázisok biztosítása az áttelepítési projekthez

Hozzon létre `AzDataMigrationDatabaseInfo` egy objektumlistát, amely az Azure Database Migration projekt részeként adatbázisokat határoz meg, amelyek a projekt létrehozásának paramétereként megadhatók. A parancsmag `New-AzDataMigrationDatabaseInfo` az AzDataMigrationDatabaseInfo létrehozásához használható. 

A következő `AzDataMigrationDatabaseInfo` példa létrehozza az **AdventureWorks2016-adatbázis** projektjét, és hozzáadja azt a projekt létrehozási paramétereként megadandó listához.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Projektobjektum létrehozása

Végül létrehozhatja az Azure Database Migration project nevű *MyDMSProject* található *USA keleti részén* használatával, `New-AzDataMigrationProject` és hozzáadja a korábban létrehozott forrás- és célkapcsolatok és az áttelepítendő adatbázisok listáját.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Áttelepítési feladat létrehozása és indítása

Végül hozzon létre és indítsa el az Azure Database Migration feladatot. Az Azure Database Migration feladat szükséges kapcsolat hitelesítő adatokat mind a forrás és a cél és az adatbázistáblák listáját kell áttelepíteni mellett a már megadott adatok a projekt előfeltételként létrehozott.

### <a name="create-credential-parameters-for-source-and-target"></a>Hitelesítő adatok paramétereinek létrehozása a forráshoz és a célhoz

A kapcsolatbiztonsági hitelesítő adatok [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektumként hozhatók létre.

A következő példa *pscredential* objektumok létrehozását mutatja be mind a forrás-, mind a célkapcsolatokhoz, amelyek karakterlánc-változóként $sourcePassword *és* *$targetPassword*jelszavakat adnak meg.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Táblázatleképezés létrehozása és az áttelepítés forrás- és célparamétereinek kiválasztása

Az áttelepítéshez szükséges másik paraméter a táblák leképezése a forrástól az áttelepítésre váró célhoz. Olyan táblák szótárának létrehozása, amelyek leképezést biztosítnak a forrás- és a céltáblák között az áttelepítéshez. A következő példa bemutatja a forrás- és a céltáblák közötti hozzárendelést az AdventureWorks 2016-adatbázis emberi erőforrások sémája között.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A következő lépés a forrás- és céladatbázisok kiválasztása, valamint a vezérlőmag használatával paraméterként való áttelepítéshez szükséges táblaleképezés biztosítása, akövetkező `New-AzDmsSelectedDB` példában látható módon:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Az áttelepítési feladat létrehozása és indítása

A `New-AzDataMigrationTask` parancsmag segítségével hozzon létre és indítson el egy áttelepítési feladatot. Ez a parancsmag a következő paramétereket várja:

* *TaskType*( Feladattípus ) . Az SQL Server és az Azure SQL Database áttelepítési *típusa, a MigrateSqlSqlSqlDb* típusú áttelepítési feladat típusa várható. 
* *Erőforráscsoport neve*. A feladat létrehozásához tartozó Azure-erőforráscsoport neve.
* *A szolgáltatásneve*. Az Azure Database Migration Service példány, amelyben a feladat létrehozásához.
* *Projektnév*. Az Azure Database Migration Service projekt neve, amelyben a feladat létrehozásához. 
* *Feladatneve*. A létrehozandó feladat neve. 
* *Forráskapcsolat*. ADmsConnInfo objektum, amely a forrás SQL Server-kapcsolatot jelöli.
* *TargetConnection*. AzDmsConnInfo objektum, amely a cél Azure SQL Database-kapcsolatot jelöli.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a forráskiszolgálóhoz való csatlakozáshoz.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a célkiszolgálóhoz való csatlakozáshoz.
* *SelectedDatabase*( Kijelölt adatbázis ) . A forrás- és céladatbázis-leképezést képviselő AzDataMigrationSelectedDB objektum.
* *Sémaérvényesítés*. (nem kötelező, kapcsoló paraméter) Az áttelepítést követően elvégzi a sémaadatok összehasonlítását a forrás és a cél között.
* *DataIntegrityValidation*. (nem kötelező, kapcsoló paraméter) Az áttelepítést követően ellenőrzőösszeg-alapú adatintegritás-ellenőrzést hajt végre a forrás és a cél között.
* *QueryAnalysisValidation*. (nem kötelező, kapcsoló paraméter) Az áttelepítést követően gyors és intelligens lekérdezéselemzést hajt végre a forrásadatbázislekérdezéseinek beolvasásával, és végrehajtja azokat a célban.

A következő példa létrehoz és elindít egy myDMSTask nevű áttelepítési feladatot:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

A következő példa ugyanazt az áttelepítési feladatot hozza létre és indítja el, mint fent, de mindhárom ellenőrzést végrehajtja:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>A migrálás monitorozása

A futó áttelepítési feladat figyelése a feladat állapottulajdonságának lekérdezésével, akövetkező példában látható módon:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>A DMS-példány törlése

Az áttelepítés befejezése után törölheti az Azure DMS-példányt:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Következő lépés

* Tekintse át az áttelepítési útmutatót a Microsoft [adatbázis-áttelepítési útmutatójában.](https://datamigration.microsoft.com/)
