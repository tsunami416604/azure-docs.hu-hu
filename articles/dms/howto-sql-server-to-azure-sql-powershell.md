---
title: SQL-kiszolgáló áttelepítése a Microsoft Azure PowerShell használata Azure adatbázis áttelepítési szolgáltatás modulja helyszíni az Azure SQL Adatbázishoz |} Microsoft Docs
description: Ismerje meg, telepíthetők át a helyszíni SQL Server Azure SQL Azure PowerShell használatával.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/05/2018
ms.openlocfilehash: d70d39f027df1fba7934c4b752b1dd2100f2e8b9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Telepítse át a helyszíni SQL Server Azure SQL Database az Azure PowerShell
Ebben a cikkben az áttelepítést a **Adventureworks2012** adatbázis visszaállítása egy helyszíni példányát az SQL Server 2016 vagy újabb Azure SQL adatbázis a Microsoft Azure PowerShell használatával. Telepíthet át adatbázisok a helyszíni SQL Server-példány az Azure SQL Database segítségével a `AzureRM.DataMigration` a Microsoft Azure PowerShell modul.

Ebből a cikkből megismerheti, hogyan:
> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot.
> * Az Azure-adatbázis áttelepítési szolgáltatás egy példányának létrehozásakor.
> * Egy Azure adatbázis áttelepítési szolgáltatáspéldány áttelepítési projekt létrehozása
> * Az áttelepítéshez.

## <a name="prerequisites"></a>Előfeltételek
Ezek a lépések elvégzéséhez szüksége:

- [SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (minden kiadás)
- SQL Server Express telepítése alapértelmezés szerint a TCP/IP-protokoll le van tiltva. Engedélyezze az következő a [jelen cikkben lévő utasítások](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Konfigurálhatja a [Windows tűzfalat a hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Egy Azure SQL Database-példányt. Egy Azure SQL Database-példányt hozhat létre a következő cikkben található részletes [Azure SQL-adatbázis létrehozása az Azure portálon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Adatok áttelepítése Segéd](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 vagy újabb.
- Az Azure-adatbázis áttelepítési szolgáltatás szükséges egy VNETET az Azure Resource Manager telepítési modell, amely webhelyek kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával vagy használatával létrehozott [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Befejeződött az adatok áttelepítési Segéd használatával, a cikkben leírtak szerint a helyi adatbázis és séma áttelepítés értékelése [ a SQL Server-áttelepítési elemzés végrehajtása](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Töltse le és telepítse a AzureRM.DataMigration modul a PowerShell-galériában bu a [Install-modul PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Az adatforrás SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatokat kell rendelkeznie a [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedéllyel.
- Azure SQL-adatbázis a célpéldány való kapcsolódáshoz használt hitelesítő adatok a cél Azure SQL Database-adatbázisok a FELADATVEZÉRLŐ ADATBÁZISHOZ engedéllyel kell rendelkeznie.
- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be a Microsoft Azure-előfizetés
A cikk utasításokat használata [jelentkezzen be Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) jelentkezzenek be az Azure-előfizetése PowerShell használatával.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, a virtuális gépek létrehozása előtt.

Hozzon létre egy erőforráscsoportot használatával a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) parancsot. 

Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *EastUS* régióban.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Egy Azure adatbázis áttelepítési szolgáltatáspéldány létrehozása 
Új Azure adatbázis áttelepítési Service-példány használatával hozhat létre a `New-AzureRmDataMigrationService` parancsmag. Ennek a parancsmagnak a következő paramétereket:
- *Azure erőforráscsoport-név*. Használhat [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) parancs előzőekben látható Azure erőforráscsoport létrehozása és a name paramétert adja meg.
- *Szolgáltatásnév*. Karakterlánc, amely megfelel a kívánt egyedi szolgáltatásnév Azure adatbázis áttelepítési szolgáltatás 
- *Hely*. Adja meg a szolgáltatás helyét. Adjon meg egy az Azure data center helyet, például az USA nyugati régiója vagy Délkelet-Ázsia
- *Termékváltozat*. Ez a paraméter DMS termékváltozat felel meg. Jelenleg támogatott termékváltozat nevek *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Virtuális alhálózati azonosító*. Parancsmag [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) egy alhálózat létrehozásához. 

Az alábbi példa létrehoz egy szolgáltatást nevű *MyDMS* erőforráscsoportban *MyDMSResourceGroup*, található *USA keleti régiója* régió nevű virtuális hálózat használatával *MyVNET* és nevű alhálózat *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Áttelepítési-projekt létrehozása
Miután létrehozta az Azure adatbázis áttelepítési szolgáltatáspéldány, hozzon létre egy áttelepítési projektet. Az Azure-adatbázis áttelepítése szolgáltatás projekt mind a forrás és cél példányok, valamint a projekt részeként áttelepíteni kívánt adatbázisok listája kapcsolódási információt igényel.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Egy adatbázis kapcsolati objektum a forrás- és kapcsolatok létrehozása
Egy adatbázis kapcsolati objektum segítségével létrehozható a `New-AzureRmDmsConnInfo` parancsmag. Ennek a parancsmagnak az alábbi paraméterekkel:
- *ServerType*. Adatbázis-kapcsolatot kért, például az SQL, Oracle vagy MySQL típusa. Az SQL Server és SQL Azure SQL használata.
- *DataSource*. A név vagy IP-egy SQL-példány vagy az SQL Azure-kiszolgáló.
- *Hitelesítés típusa*. A kapcsolat, amely lehet SqlAuthentication vagy WindowsAuthentication hitelesítési típus.
- *TrustServerCertificate* paraméter egy olyan értéket, amely jelzi, hogy a csatorna megkerülése érdekében a tanúsítványlánc érvényesítése megbízhatósági során titkosítva legyen-e. Értéke true vagy false lehet.

Az alábbi példakód létrehozza Kapcsolatinformáció objektum forrás sql-hitelesítéssel MySQLSourceServer nevű SQL-kiszolgáló 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Következő példa bemutatja, Kapcsolatinformáció nevű sql-hitelesítéssel MySQLAzureTarget SQL Azure adatbázis-kiszolgáló létrehozása

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Adja meg az áttelepítési projekt adatbázisok
Létrehozhat egy `AzureRmDataMigrationDatabaseInfo` , amely meghatározza, hogy az Azure-adatbázis áttelepítésének részeként adatbázisok projektjében objektumok paraméterként megadható a projekt létrehozásához. A parancsmag `New-AzureRmDataMigrationDatabaseInfo` AzureRmDataMigrationDatabaseInfo létrehozására használható. 

Az alábbi példakód létrehozza `AzureRmDataMigrationDatabaseInfo` adatbázis AdventureWorks2016 projektre, és hozzáadja azt a lista paraméterként a projekt létrehozásához meg kell adni.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>A projekt objektum létrehozása
Végül hozhat létre Azure-adatbázis áttelepítése projekt nevű *MyDMSProject* található *USA keleti régiója* használatával `New-AzureRmDataMigrationProject` , valamint hozzáadja a korábban létrehozott forrása és célja kapcsolatok és listája adatbázisok áttelepítése.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Hozzon létre, és indítsa el az áttelepítési feladatok
Végül hozzon létre, és indítsa el az Azure-adatbázis áttelepítési feladat. Az Azure adatbázis áttelepítési feladathoz kapcsolat hitelesítő adatokat a forrás és cél- és adatbázis telepíthető át előfeltételként létrehozott projekt már foglalt információk mellett táblák listáját. 

### <a name="create-credential-parameters-for-source-and-target"></a>Forrás és cél paramétereinek hitelesítő adat létrehozása
Kapcsolat hitelesítő adatokat hozhatók létre a [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum. 

A következő példa bemutatja *PSCredential* jelszavak karakterlánc típusú változót, így a forrás- és cél kapcsolatok objektumok *$sourcePassword* és *$ targetPassword*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Hozzon létre egy térkép és jelölje be forrás- és célparaméterei legyenek az áttelepítéshez
Az áttelepítéshez szükséges egy másik paraméter van táblák forrásból át kell cél leképezése. Hozzon létre szótár olyan táblázatot, amely az áttelepítés forrás és cél táblák közötti leképezéseket. A következő példa bemutatja a forrás és cél táblák közötti megfeleltetés emberi erőforrások séma az AdventureWorks 2016 adatbázis.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A következő lépés az, hogy válassza ki a forrás- és adatbázisokat, és adja meg a tábla hozzárendelésének történő áttelepítéséhez paraméterként a `New-AzureRmDmsSqlServerSqlDbSelectedDB` parancsmag, a következő példában látható módon:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Hozzon létre, és indítsa el az áttelepítési feladatok

Használja a `New-AzureRmDataMigrationTask` parancsmag segítségével hozzon létre egy áttelepítési feladat elindításához. Ennek a parancsmagnak az alábbi paraméterekkel:
- *TaskType*. Áttelepítési feladat létrehozása az SQL Server SQL Azure áttelepítési típus típusú *MigrateSqlServerSqlDb* várt. 
- *Erőforráscsoport neve*. A feladat létrehozásához Azure erőforráscsoport nevét.
- *Szolgáltatásnév*. Az Azure adatbázis áttelepítési szolgáltatáspéldány, amelyben a feladat létrehozásához.
- *Projektnév*. A feladat létrehozásához Azure-adatbázis áttelepítése projekt neve. 
- *Feladatnév*. A létrehozandó feladat nevét. 
- *Adatforrás-kapcsolat*. Adatforrás-kapcsolatot képviselő AzureRmDmsConnInfo objektum.
- *Cél a kapcsolat*. AzureRmDmsConnInfo a cél kapcsolatot képviselő objektum.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum a forrás-kiszolgálójához való kapcsolódás.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektum célkiszolgálóhoz való kapcsolódáshoz.
- *SelectedDatabase*. A forrás- és adatbázis-leképezés képviselő AzureRmDmsSqlServerSqlDbSelectedDB objektum.

Az alábbi példa létrehoz és myDMSTask nevű áttelepítési feladat elindítása:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>A figyelő az áttelepítés
Az áttelepítési feladat fut a feladat a következő példában látható módon állapot tulajdonságának lekérdezésével figyelhető meg:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>További lépések
- Tekintse át az áttelepítési útmutató a Microsoft [adatbázis áttelepítési útmutató](https://datamigration.microsoft.com/).