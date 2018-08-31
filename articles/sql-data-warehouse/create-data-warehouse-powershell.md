---
title: 'Rövid útmutató: Hozzon létre egy Azure SQL Data Warehouse – Azure PowerShell-lel |} A Microsoft Docs'
description: Gyorsan létrehozhat egy SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabály és data warehouse-bA az Azure PowerShell használatával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/01/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ecde7cb3662fc80e7968acfcac99bc8f28e8b15b
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287573"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Rövid útmutató: Létrehozása és lekérdezése az Azure SQL data warehouse az Azure PowerShell használatával

Gyorsan létrehozhat egy Azure SQL data warehouse, Azure PowerShell-lel.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Az oktatóanyaghoz az Azure PowerShell-modul 5.1.1-es vagy újabb verziója szükséges. A jelenleg rendelkezésére álló verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 


> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) parancs használatával, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Add-AzureRmAccount
```

A használt előfizetés megtekintéséhez futtassa a [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) parancsot.

```powershell
Get-AzureRmSubscription
```

Ha nem az alapértelmezett előfizetést kívánja használni, futtassa a [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription) parancsot.

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
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
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Hozzon létre egy logikai kiszolgálót

Hozzon létre egy [Azure SQL logikai kiszolgáló](../sql-database/sql-database-logical-servers.md) használatával a [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) parancsot. A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel. A következő példában létrehozunk egy véletlenszerűen elnevezett kiszolgálót az erőforráscsoportban egy `ServerAdmin` nevű és `ChangeYourAdminPassword1` jelszavú rendszergazdai bejelentkezéssel. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL kiszolgálószintű tűzfalszabály](../sql-database/sql-database-firewall-configure.md) használatával a [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) parancsot. Kiszolgálószintű tűzfalszabály lehetővé teszi, hogy a külső alkalmazások, például az SQL Server Management Studio vagy az SQLCMD segédprogram szeretne csatlakozni az SQL data warehouse az SQL Data Warehouse szolgáltatás tűzfalán keresztül. A következő példában a tűzfal csak más Azure-erőforrások számára van nyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database és az SQL Data Warehouse az 1433-as porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni az Azure SQL Serverhez, kivéve, ha az informatikai részleg megnyitja az 1433-as porton.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Adattárház létrehozása mintaadatok használatával
Ez a példa létrehoz egy data warehouse használatával a korábban definiált változókat.  Azt határozza meg a szolgáltatási cél DW400, amely egy alacsonyabb költségű kiindulási pont, a data warehouse-hoz. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

A szükséges paraméterek a következők:

* **RequestedServiceObjectiveName**: mennyisége [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) kért. Ez a mennyiség növelése növeli a számítási költségeket. A támogatott értékek listáját lásd: [memória- és egyidejűségi korlátok](memory-and-concurrency-limits.md).
* **DatabaseName**: A létrehozandó SQL Data Warehouse neve.
* **Kiszolgálónév**: a kiszolgáló, a létrehozásához használt nevét.
* **ResourceGroupName**: A használt erőforráscsoport. Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Edition**: Egy SQL Data Warehouse létrehozásához a „DataWarehouse” értéket kell megadni.

A választható paraméterek a következők:

- **CollationName**: Ha nincs megadva, az alapértelmezett rendezés: SQL_Latin1_General_CP1_CI_AS. Az adatbázisok rendezése nem módosítható.
- **MaxSizeBytes**: Alapértelmezés szerint az adatbázisok maximális mérete 10 GB.

A paraméterbeállításokkal további információkért lásd: [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül. 

> [!TIP]
> Ha azt tervezi, hogy az ezt követő rövid útmutatókkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>További lépések

Az eddigiekben létrehozott egy adattárházat, hozzá egy tűzfalszabályt, csatlakozott hozzá, és futtatott néhány lekérdezést. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.
> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
