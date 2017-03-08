---
title: "PowerShell: Ismerkedés az Azure SQL Database szolgáltatással | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat PowerShell használatával. Megtanulhatja azt is, hogyan kérdezhet le adatbázisokat PowerShell használatával."
keywords: "új sql-adatbázis létrehozása,adatbázis beállítása"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 02/23/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 78d9194f50bcdc4b0db7871f2480f59b26cfa8f6
ms.openlocfilehash: 7b7273edfa33f297cb5dc30ef380b6a737787b33
ms.lasthandoff: 02/27/2017


---

# <a name="tutorial-provision-and-access-an-azure-sql-database-using-powershell"></a>Oktatóanyag: Azure SQL-adatbázis létrehozása és elérése a PowerShell használatával

Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan hajthatja végre a következő műveleteket a PowerShell-lel:

* Új Azure-erőforráscsoport létrehozása
* Azure SQL logikai kiszolgáló létrehozása
* Az Azure SQL Server tulajdonságainak megtekintése
* Kiszolgálószintű tűzfalszabály létrehozása
* Az AdventureWorksLT mintaadatbázis létrehozása önálló adatbázisként
* Az AdventureWorksLT mintaadatbázis tulajdonságainak megtekintése
* Üres önálló adatbázis létrehozása

Az oktatóanyag során a következő lépéseket is végre fogja hajtani:

* Csatlakozás a logikai kiszolgálóhoz és annak master adatbázisához
* A master adatbázis tulajdonságainak megtekintése
* Csatlakozás a mintaadatbázishoz
* A felhasználói adatbázis tulajdonságainak megtekintése

Mire végez az oktatóanyaggal, rendelkezésére fog állni egy Azure-erőforráscsoportban futó és logikai kiszolgálóhoz csatlakozó minta- és egy üres adatbázis. Ezen kívül rendelkezésére áll majd egy kiszolgálószintű tűzfalszabály is, amely úgy van konfigurálva, hogy engedélyezze a kiszolgálószintű rendszerbiztonsági tagnak a kiszolgálóra történő bejelentkezést egy megadott IP-címről (vagy egy IP-címtartományból). 

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 30 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).


> [!TIP]
> Ugyanezen feladatokat [az Azure Portalon](sql-database-get-started.md) is végrehajthatja egy, a kezdeti lépéseket ismertető oktatóanyagban.
>


## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Egy olyan fiókkal kell bejelentkeznie, amely tagja az előfizetés-tulajdonosi vagy a közreműködői szerepkörnek. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Új logikai SQL Server létrehozása az Azure PowerShell-lel

Egy erőforráscsoport szükséges, amely tartalmazza a kiszolgálót, ezért az első lépés egy új erőforráscsoport és kiszolgáló létrehozása ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)), vagy egy meglévőre való hivatkozás ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).


A következő kódrészletek egy erőforráscsoportot és egy Azure SQL Servert hoznak létre, ha azok még nem léteznek:

Az érvényes Azure-helyekkel és karakterlánc-formátumokkal kapcsolatban lásd a lentebbi [Segítséget nyújtó kódrészletek](#helper-snippets) szakaszt.
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>A logikai SQL Server tulajdonságainak megtekintése az Azure PowerShell-lel

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure PowerShell-lel

A tűzfalszabály beállításához ismernie kell a nyilvános IP-címét. Az IP-címét egy tetszőleges böngészőben ellenőrizheti (vannak olyan weboldalak, amelyek megadják ezt az információt). További részletek: [tűzfalszabályok](sql-database-firewall-configure.md).

A következő példa a [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) és a [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) parancsmagot használja egy hivatkozáshoz vagy egy új szabály létrehozásához. Ha a kódrészlet esetében a szabály már létezik, csak egy hivatkozást hoz létre arra, és nem frissíti a kezdő és záró IP-címet. Mindig módosíthatja úgy az **else** záradékot, hogy az a [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule) parancsmagot használja a létrehozáshoz vagy a frissítéshez.

> [!NOTE] 
> Az SQL Database-tűzfalat a kiszolgálón egyetlen IP-cím vagy egy teljes IP-címtartomány előtt is megnyithatja. A tűzfal megnyitását követően az SQL-rendszergazdák és -felhasználók a kiszolgáló bármely olyan adatbázisába bejelentkezhetnek, amelyhez érvényes hitelesítő adatokkal rendelkeznek.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule

# Allow Azure services to access the server
$serverFirewallRuleName2 = "allowAzureServices"
$serverFirewallStartIp2 = "0.0.0.0"
$serverFirewallEndIp2 = "0.0.0.0"

$myFirewallRule2 = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName2 -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule2)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName2"
   $myFirewallRule2 = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName2 -StartIpAddress $serverFirewallStartIp2 -EndIpAddress $serverFirewallEndIp2
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName2 already exists:"
}
$myFirewallRule2

```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Csatlakozás SQL Serverhez az Azure PowerShell-lel

Egy gyors lekérdezéssel ellenőrizze a főadatbázisban, hogy tud-e csatlakozni a kiszolgálóhoz. A következő kódrészlet a [.NET-keretrendszer SQL Server-szolgáltatója (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) segítségével csatlakozik és kérdezi le a kiszolgáló főadatbázisát. Egy kapcsolati karakterláncot állít össze az előző kódrészletekben használt változók alapján. A helyőrzőket cserélje le az SQL Server rendszergazdai bejelentkezési nevére és jelszavára, amelyet az előző lépésekben a kiszolgáló létrehozásához használt.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Új AdventureWorksLT mintaadatbázis létrehozása az Azure PowerShell-lel

A következő kódrészlet az AdventureWorksLT mintaadatbázis .bacpac fájlját importálja a [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport) parancsmag használatával. A .bacpac fájl egy nyilvános, írásvédett Azure Blob Storage-fiókban található. Az importálási parancsmag futtatása után az importálási folyamat állapotát a [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus) parancsmaggal figyelheti.


```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "SharedAccessKey"
$storageUri = "https://sqldbtutorial.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac"
$storageKey = "?"

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Adatbázis-tulajdonságok megtekintése az Azure PowerShell-lel

Az adatbázis létrehozása után megtekintheti annak egyes tulajdonságait.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Mintaadatbázis csatlakoztatása és lekérdezése az Azure PowerShell-lel

Egy gyors lekérdezéssel ellenőrizze, hogy lehet-e csatlakozni az AdventureWorksLT adatbázishoz. A következő kódrészlet a [.NET-keretrendszer SQL Server-szolgáltatója (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) segítségével csatlakozik és kérdezi le az adatbázist. Egy kapcsolati karakterláncot állít össze az előző kódrészletekben használt változók alapján. A helyőrzőt cserélje le az SQL Server rendszergazdai jelszavára.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Új üres adatbázis létrehozása az Azure PowerShell-lel

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Teljes Azure PowerShell-szkript kiszolgáló, tűzfalszabály és adatbázis létrehozásához



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"


# Storage account details for locating
# and accessing the sample .bacpac 
# Do Not Edit for this tutorial
$myStorageKeyType = "SharedAccessKey"
$myStorageUri = "https://sqldbtutorial.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac"
$myStorageKey = "?"



# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin

# Create or update server firewall rules
########################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule

# Allows Azure services to access the server
$serverFirewallRuleName2 = "allowAzureServices"
$serverFirewallStartIp2 = "0.0.0.0"
$serverFirewallEndIp2 = "0.0.0.0"

$myFirewallRule2 = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName2 -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule2)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName2"
   $myFirewallRule2 = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName2 -StartIpAddress $serverFirewallStartIp2 -EndIpAddress $serverFirewallEndIp2
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName2 already exists:"
}
$myFirewallRule2


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> Tanulás közben akár pénzt is megtakaríthat a használaton kívüli adatbázisok törlésével. Az Alapszintű kiadású adatbázisokat 7 napon belül visszaállíthatja. Kiszolgálót azonban semmiképp ne töröljön. Ha így tesz, sem a kiszolgálót, sem annak törölt adatbázisait nem tudja visszaállítani.

## <a name="helper-snippets"></a>Segítséget nyújtó kódrészletek

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> Tanulás közben akár pénzt is megtakaríthat a használaton kívüli adatbázisok törlésével. Az Alapszintű kiadású adatbázisokat hét napon belül visszaállíthatja. A kiszolgálót azonban ne törölje. Ha így tesz, sem a kiszolgálót, sem annak törölt adatbázisait nem tudja visszaállítani.
>

## <a name="next-steps"></a>Következő lépések
Most, hogy az első lépéseket ismertető oktatóanyag végére ért és létrehozott egy mintaadatokat tartalmazó adatbázist, további oktatóanyagokat szeretnénk a figyelmébe ajánlani, amelyekkel bővítheti meglévő tudását. 

- Az SQL Server hitelesítési oktatóanyagának kezdeti lépéseihez tekintse meg az [SQL-hitelesítés és -engedélyezés](sql-database-control-access-sql-authentication-get-started.md) című témakört.
- Az Azure Active Directory hitelesítési oktatóanyagának kezdeti lépéseihez tekintse meg az [Azure AD-hitelesítés és engedélyezés](sql-database-control-access-aad-authentication-get-started.md) című témakört.
* Ha a mintaadatbázist szeretné lekérdezni az Azure Portalon, tekintse meg a [Nyilvános előzetes verzió: Interaktív lekérdezések használata SQL-adatbázisok esetén](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/) című témakört.
* Ha munkája során az Excelt használja, olvassa el a [Connect to a SQL database in Azure with Excel](sql-database-connect-excel.md) (SQL-adatbázishoz való kapcsolódás az Azure-ban az Excellel) című cikket.
* Ha készen áll a kódolás elkezdésére, válasszon programozási nyelvet a [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md) (Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez) című cikk alapján.
* Ha át szeretné helyezni helyszíni SQL Server-adatbázisait az Azure-ba, olvassa el az [adatbázisok SQL Database-be történő áttelepítésével](sql-database-cloud-migrate.md) foglalkozó témakört.
* Ha a BCP parancssori eszköz használatával szeretne adatokat betölteni egy CSV-fájlból egy új táblába, olvassa el a [Loading data into SQL Database from a CSV file using BCP](sql-database-load-from-csv-with-bcp.md) (Adatok betöltése az SQL Database-be CSV-fájlból a BCP használatával) című cikket.
* Ha táblákat és más objektumokat szeretne létrehozni, tekintse meg a [táblák létrehozásával](https://msdn.microsoft.com/library/ms365315.aspx) foglalkozó oktatóanyag táblák létrehozását részletező fejezetét.

## <a name="additional-resources"></a>További források
[Mi az SQL Database?](sql-database-technical-overview.md)

