---
title: "Azure SQL Database beállítása a PowerShell segítségével | Microsoft Docs"
description: "Az Azure SQL Database kezelése a PowerShell segítségével."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: adad6b8e27e0996559d5e6dacb8dd60fbf52a631
ms.openlocfilehash: 0c1ce1c29e447d9db4ef0df7873ef89cb835abee


---
# <a name="managing-azure-sql-database-using-powershell"></a>Azure SQL Database kezelése a PowerShell használatával
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-manage-portal.md)
> * [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Ez a témakör az Azure SQL Database számos feladatának elvégzéséhez használt PowerShell-parancsmagokat mutatja be. A teljes listát az [Azure SQL Database-parancsmagok](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx) című részben tekintheti meg.

> [!TIP]
> A kiszolgálók és a kiszolgálóalapú tűzfalak létrehozásáról, a kiszolgáló tulajdonságainak megtekintéséről, a master adatbázishoz való csatlakozásról és az onnan való lekérdezésről, a mintaadatbázis, illetve üres adatbázis létrehozásáról, az adatbázis tulajdonságainak lekérdezéséről, valamint a mintaadatbázishoz való kapcsolódásról és az abból való lekérdezésről szóló oktatóanyagok az [Első lépéseket ismertető oktatóanyagban](sql-database-get-started-powershell.md) találhatók.
>

## <a name="how-do-i-create-a-resource-group"></a>Hogyan hozható létre erőforráscsoport?
Ha erőforráscsoportot szeretne létrehozni az SQL Database és a kapcsolódó Azure-erőforrások számára, használja a [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx) parancsmagot.

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).
Részletes útmutató: [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure PowerShell-lel történő használatának első lépései](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server"></a>Hogyan hozható létre SQL Database-kiszolgáló?
SQL Database-kiszolgáló létrehozásához használja a [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx) parancsmagot. A *server1* helyére írja be a kiszolgáló nevét. A kiszolgáló nevének minden Azure SQL Database-kiszolgáló esetében egyedinek kell lennie. Ha a kiszolgálónév már foglalt, hibaüzenet jelenik meg. A parancs végrehajtása több percet is igénybe vehet. Az erőforráscsoportnak már léteznie kell az előfizetésben.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

További tudnivalók a kiszolgálókról: [Az SQL Database funkciói](sql-database-features.md). Részletes útmutató: [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure PowerShell-lel történő használatának első lépései](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>Hogyan hozható létre tűzfalszabály az SQL Database-kiszolgálón?
A kiszolgálóhoz való hozzáféréshez szükséges tűzfalszabály létrehozásához használja a [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) parancsmagot. Futtassa a következő parancsot a kezdő és a záró IP-címeket kicserélve az ügyfél által használt valós címmel. Az erőforráscsoportnak és a kiszolgálónak már léteznie kell az előfizetésben.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Ha engedélyezni szeretné más Azure-szolgáltatásoknak a kiszolgálóhoz való hozzáférést, hozzon létre egy tűzfalszabályt, és állítsa a `-StartIpAddress` és az `-EndIpAddress` paraméter értékét egyaránt **0.0.0.0**-ra. Ez a speciális szabály minden Azure-forgalmat engedélyez a kiszolgálón.

További információ: [Azure SQL Database-tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx). Részletes útmutató: [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure PowerShell-lel történő használatának első lépései](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database"></a>Hogyan hozható létre SQL Database-adatbázis?
SQL Database-adatbázis létrehozásához használja a [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) parancsmagot. Az erőforráscsoportnak és a kiszolgálónak már léteznie kell az előfizetésben. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

További információ: [Mi az az SQL Database?](sql-database-technical-overview.md). Részletes útmutató: [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure PowerShell-lel történő használatának első lépései](sql-database-get-started-powershell.md).

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>Hogyan változtatható az SQL Database teljesítményszintje?
A teljesítményszint változtatásához növelje vagy csökkentse az adatbázis méretét a [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) parancsmag segítségével. Az erőforráscsoportnak, a kiszolgálónak és az adatbázisnak már léteznie kell az előfizetésben. Állítsa a `-RequestedServiceObjectiveName` értékét (az alábbi kódrészlethez hasonlóan) egyetlen szóközre az Alapszintű csomagban. Állítsa a következőre: *S0*, *S1*, *P1*, *P6* stb., az előző, egyéb csomagokra vonatkozó példához hasonlóan.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

További információk: [Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken](sql-database-service-tiers.md). Mintaparancsprogram a [Minta PowerShell-parancsprogram az SQL Database szolgáltatás- és teljesítményszintjének módosításához](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database) című részben található.

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>Hogyan másolható SQL Database-adatbázis ugyanazon kiszolgálóra?
Ha SQL Database-adatbázist szeretne másolni ugyanazon kiszolgálóra, használja a [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) parancsmagot. Állítsa a `-CopyServerName` és `-CopyResourceGroupName` értékeket a forrásadatbázis-kiszolgálóéval és az erőforráscsoportéval azonos értékre.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

További információk az [Azure SQL Database másolása](sql-database-copy.md) című részben. Mintaparancsprogram az [SQL Database PowerShell-parancsfájl másolása](sql-database-copy-powershell.md#example-powershell-script) című részben található.

## <a name="how-do-i-delete-a-sql-database"></a>Hogyan törölhető egy SQL Database-adatbázis?
SQL Database-adatbázis törlése használja a [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx) parancsmagot. Az erőforráscsoportnak, a kiszolgálónak és az adatbázisnak már léteznie kell az előfizetésben.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>Hogyan törölhető egy SQL Database-kiszolgáló?
SQL Database-kiszolgáló törléséhez használja a [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx) parancsmagot.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>Hogyan hozhatók létre és kezelhetők rugalmas készletek a PowerShell használatával?
A rugalmas készletek PowerShell-lel való létrehozásának részleteit az [Új rugalmas készlet létrehozása a PowerShell-lel](sql-database-elastic-pool-create-powershell.md) című részben tekintheti meg.

A rugalmas készletek PowerShell-lel való kezelésének részleteit a [Rugalmas készlet figyelése és kezelése a PowerShell-lel](sql-database-elastic-pool-manage-powershell.md) című részben tekintheti meg.

## <a name="related-information"></a>Kapcsolódó információk
* [Azure SQL Database-parancsmagok](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure parancsmag-referencia](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Jan17_HO1-->


