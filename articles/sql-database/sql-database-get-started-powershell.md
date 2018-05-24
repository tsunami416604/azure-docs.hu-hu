---
title: 'Azure PowerShell: SQL-adatbázis létrehozása | Microsoft Docs'
description: Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat az Azure Portal használatával.
keywords: oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: PowerShell
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 91c92de4d7c94cceec69b19647b1fe0bf31915c4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-single-azure-sql-database-using-powershell"></a>Önálló Azure SQL-adatbázis létrehozása a PowerShell használatával

A PowerShell használatával Azure-erőforrások hozhatók létre és kezelhetők a parancssorból vagy szkriptekben. Ez az útmutató azt ismerteti, hogyan helyezhet üzembe Azure SQL-adatbázist a PowerShell használatával egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) található [Azure-erőforráscsoportban](../azure-resource-manager/resource-group-overview.md).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Az oktatóanyaghoz az Azure PowerShell-modul 4.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancs használatával, és kövesse a képernyőn megjelenő útmutatást.

```powershell
Connect-AzureRmAccount
```

## <a name="create-variables"></a>Változók létrehozása

Ebben a rövid útmutatóban a szkriptekben használt változókat határozhat meg.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Hozzon létre egy logikai kiszolgálót

Hozzon létre egy [Azure SQL Database logikai kiszolgálót](sql-database-features.md) a [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) paranccsal. A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel. A következő példában létrehozunk egy véletlenszerűen elnevezett kiszolgálót az erőforráscsoportban egy `ServerAdmin` nevű és `ChangeYourAdminPassword1` jelszavú rendszergazdai bejelentkezéssel. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL Database kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md) a [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) parancs használatával. A kiszolgálószintű tűzfalszabályok lehetővé teszik, hogy külső alkalmazások, például az SQL Server Management Studio vagy az SQLCMD segédprogram az SQL Database szolgáltatás tűzfalán keresztül csatlakozzon egy SQL-adatbázishoz. A következő példában a tűzfal csak más Azure-erőforrások számára van nyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Hozzon létre egy adatbázist a kiszolgálón mintaadatokkal

Hozzon létre egy [S0 teljesítményszintű](sql-database-service-tiers-dtu.md) adatbázist a kiszolgálón a [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) parancs használatával. Az alábbi példa egy `mySampleDatabase` nevű adatbázist hoz létre, és betölti az AdventureWorksLT mintaadatokat ebbe az adatbázisba. Igény szerint cserélje ki ezeket az előre meghatározott értékeket (az ebben a gyűjteményben lévő többi rövid útmutató az ebben a rövid útmutatóban lévő értékekre épít).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksLT" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül.

> [!TIP]
> Ha azt tervezi, hogy az ezt követő rövid útmutatóval dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>További lépések

- Most, hogy rendelkezik egy adatbázissal, [csatlakoztathatja a kedvenc eszközeinek vagy nyelveinek egyikét, és lekérdezéseket hajthat végre velük](sql-database-connect-query.md).
- Az első adatbázis megtervezésével, a táblák létrehozásával és az adatok beszúrásával kapcsolatos információkért tekintse meg az alábbi oktatóanyagok egyikét:
 - [Az első Azure SQL-adatbázis megtervezése SSMS használatával](sql-database-design-first-database.md)
  - [Azure SQL-adatbázis megtervezése, és kapcsolódás C# és ADO.NET használatával](sql-database-design-first-database-csharp.md)

