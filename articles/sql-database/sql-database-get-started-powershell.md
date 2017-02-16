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
ms.date: 12/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2a85b3dc1078bad9e5e2fc0ce0bec7e994b29150
ms.openlocfilehash: e3a9ba798639a9939d8c3d5330b21715ac4be53d


---

# <a name="sql-database-tutorial-get-started-with-azure-sql-database-servers-databases-and-firewall-rules-using-powershell"></a>SQL Database oktatóanyag: Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok a PowerShell használatával – első lépések

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

* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Egy olyan fiókkal kell bejelentkeznie, amely tagja az előfizetés-tulajdonosi vagy a közreműködői szerepkörnek. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

* Szüksége lesz az AdventureWorksLT mintaadatbázis .bacpac fájljára az Azure Blob Storage-ban.

### <a name="download-the-adventureworkslt-sample-database-bacpac-file-and-save-it-in-azure-blob-storage"></a>Az AdventureWorksLT mintaadatbázis .bacpac fájljának letöltése és mentése az Azure Blob Storage-ban

Ez az oktatóanyag egy új AdventureWorksLT adatbázis létrehozását mutatja be egy .bacpac fájl az Azure Storage-ból való importálása révén. Az első lépés az AdventureWorksLT.bacpac fájl egy másolatának beszerzése és feltöltése a Blob Storage-ba.
A következő lépések előkészítik a mintaadatbázis importálását:

1. [Töltse le az AdventureWorksLT.bacpac fájlt](https://sqldbbacpacs.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac), és mentse .bacpac fájlkiterjesztéssel.
2. [Hozzon létre egy tárfiókot](../storage/storage-create-storage-account.md#create-a-storage-account) – a tárfiókot létrehozhatja az alapértelmezett beállításokkal.
3. Hozzon létre egy új **tárolót**. Ehhez a tárfiókba lépve válassza a **Blobok** lehetőséget, majd kattintson a **+Tároló** elemre.
4. Töltse fel a .bacpac fájlt a tárfiókban lévő blobtárolóba. Ehhez használhatja a tároló oldalának tetején található **Feltöltés** gombot, vagy az [AzCopy](../storage/storage-use-azcopy.md#blob-upload) eszközt. 
5. Az AdventureWorksLT.bacpac mentését követően az oktatóanyag későbbi részeiben az importálási kódrészlethez szüksége lesz az URL-címre és a tárfiók kulcsára. 
   * Jelölje ki a bacpac-fájlt és másolja az URL-címét. Ez a következőhöz hasonló lesz: https://{tárfiók-neve}.blob.core.windows.net/{tároló-neve}/AdventureWorksLT.bacpac. A tárfiók oldalán kattintson az **Elérési kulcsok** elemre, és másolja a **key1** értékét.


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

A következő kódrészlet az AdventureWorksLT mintaadatbázis .bacpac fájlját importálja a [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport) parancsmag használatával. A .bacpac fájl az Azure Blob Storage-ban található. Az importálási parancsmag futtatása után az importálási folyamat állapotát a [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus) parancsmaggal figyelheti.
A $storageUri a portálra korábban feltöltött .bacpac fájl URL-cím tulajdonsága, amely a következőhöz hasonló: https://{tárfiók}.blob.core.windows.net/{tároló}/AdventureWorksLT.bacpac

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "StorageAccessKey"
$storageUri = "{storage-uri}" # URL of bacpac file you uploaded to your storage account
$storageKey = "{storage-key}" # key1 in the Access keys setting of your storage account

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

$myStorageKeyType = "StorageAccessKey"
# Get these values from your Azure storage account:
$myStorageUri = "{http://your-storage-account.blob.core.windows.net/your-container/AdventureWorksLT.bacpac}"
$myStorageKey = "{your-storage-key}"


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


# Create or update server firewall rule
#######################################

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

* Ha további információkat szeretne szerezni az Azure SQL Database biztonságával kapcsolatban, tekintse át az [adatbázis-biztonságot ismertető](sql-database-control-access-sql-authentication-get-started.md) témakört.
* Ha munkája során az Excelt használja, olvassa el a [Connect to a SQL database in Azure with Excel](sql-database-connect-excel.md) (SQL-adatbázishoz való kapcsolódás az Azure-ban az Excellel) című cikket.
* Ha készen áll a kódolás elkezdésére, válasszon programozási nyelvet a [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md) (Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez) című cikk alapján.
* Ha át szeretné helyezni helyszíni SQL Server-adatbázisait az Azure-ba, olvassa el az [adatbázisok SQL Database-be történő áttelepítésével](sql-database-cloud-migrate.md) foglalkozó témakört.
* Ha a BCP parancssori eszköz használatával szeretne adatokat betölteni egy CSV-fájlból egy új táblába, olvassa el a [Loading data into SQL Database from a CSV file using BCP](sql-database-load-from-csv-with-bcp.md) (Adatok betöltése az SQL Database-be CSV-fájlból a BCP használatával) című cikket.
* Ha táblákat és más objektumokat szeretne létrehozni, tekintse meg a [táblák létrehozásával](https://msdn.microsoft.com/library/ms365315.aspx) foglalkozó oktatóanyag táblák létrehozását részletező fejezetét.

## <a name="additional-resources"></a>További források
[Mi az SQL Database?](sql-database-technical-overview.md)



<!--HONumber=Jan17_HO3-->


