---
title: 'Gyors útmutató: Hozzon létre egy Azure SQL Data Warehouse – Azure PowerShell-lel |} A Microsoft Docs'
description: Gyorsan létrehozhat egy SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabály és data warehouse-bA az Azure PowerShell használatával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/15/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 22ccc11d1eb74fd868fd740f02311baeb5ac5987
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54433611"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Gyors útmutató: Létrehozása és lekérdezése az Azure SQL data warehouse az Azure PowerShell használatával

Gyorsan létrehozhat egy Azure SQL data warehouse, Azure PowerShell-lel.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Az oktatóanyaghoz az Azure PowerShell-modul 5.1.1-es vagy újabb verziója szükséges. A jelenleg rendelkezésére álló verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. 


> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure-előfizetés használatával jelentkezzen be a [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Add-AzureRmAccount
```

Tekintse meg, hogy melyik előfizetéssel használja, futtassa [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Ha szeretne egy másik előfizetést, mint az alapértelmezett, futtassa [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
Set-AzureRmContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Változók létrehozása

Ebben a rövid útmutatóban a szkriptekben használt változókat határozhat meg.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
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

Hozzon létre egy [Azure SQL logikai kiszolgáló](../sql-database/sql-database-logical-servers.md) használatával a [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) parancsot. A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel. A következő példában létrehozunk egy véletlenszerűen elnevezett kiszolgálót az erőforráscsoportban nevű rendszergazda felhasználó `ServerAdmin` jelszóval `ChangeYourAdminPassword1`. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

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
> SQL Database és az SQL Data Warehouse az 1433-as porton keresztül kommunikálnak. Csatlakozás a vállalati hálózaton belülről próbál, ha a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni az Azure SQL Serverhez, kivéve, ha az informatikai részleg megnyitja az 1433-as porton.
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

* **RequestedServiceObjectiveName**: Mennyisége [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) , a kért. Ez a mennyiség növelése növeli a számítási költségeket. A támogatott értékek listáját lásd: [memória- és egyidejűségi korlátok](memory-and-concurrency-limits.md).
* **DatabaseName**: A létrehozandó SQL Data Warehouse neve.
* **Kiszolgálónév**: A létrehozásához használt kiszolgáló neve.
* **ResourceGroupName**: Az erőforráscsoportot használja. Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Kiadás**: "Datawarehouse értéket" kell lennie az SQL Data Warehouse létrehozása.

A választható paraméterek a következők:

- **CollationName**: Ha nincs megadva alapértelmezett rendezése az sql_latin1_general_cp1_ci_as rendezést alkalmazza. Rendezés nem módosítható az adatbázisok.
- **MaxSizeBytes**: Egy adatbázis alapértelmezett maximális mérete 10 GB-os.

A paraméterbeállításokkal további információkért lásd: [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül. 

> [!TIP]
> Ha azt tervezi, hogy a rövid útmutató későbbi oktatóanyagokkal dolgozik tovább, ne törölje az erőforrásokat létrehozott ebben a rövid útmutatóban. Ha nem szeretné folytatni, használja az alábbi lépéseket az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást törli.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>További lépések

Sikeresen létrehozott egy adattárházat, és létrehozott egy tűzfalszabályt, csatlakozott az data warehouse-ba, és futtatott néhány lekérdezést. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.
> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
