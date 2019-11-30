---
title: 'Rövid útmutató: Warehouse létrehozása – Azure PowerShell'
description: Gyorsan létrehozhat egy SQL Database logikai kiszolgálót, a kiszolgálói szintű tűzfalszabályok és az adattárházat Azure PowerShell használatával.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 44abf23ce632aa1ac630eab3313ffd675eef1a0b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559159"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Gyors útmutató: Azure SQL Data Warehouse létrehozása és lekérdezése Azure PowerShell

Azure PowerShell használatával gyorsan létrehozhat egy Azure SQL Data Warehouse.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancs használatával, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)futtatásával megtekintheti, hogy melyik előfizetést használja.

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [set-AzContext](/powershell/module/az.accounts/set-azcontext)parancsot.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
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

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Hozzon létre egy logikai kiszolgálót

Hozzon létre egy [Azure SQL logikai kiszolgálót](../sql-database/sql-database-logical-servers.md) a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancs használatával. A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel. Az alábbi példa egy véletlenszerűen elnevezett kiszolgálót hoz létre az erőforráscsoporthoz egy `ServerAdmin` nevű rendszergazdai felhasználóval és `ChangeYourAdminPassword1`jelszavával. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL Server-szintű tűzfalszabályok](../sql-database/sql-database-firewall-configure.md) a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) parancs használatával. A kiszolgálói szintű tűzfalszabályok lehetővé teszik egy külső alkalmazás, például SQL Server Management Studio vagy a SQLCMD segédprogram számára, hogy a SQL Data Warehouse szolgáltatás tűzfalan keresztül kapcsolódjon egy SQL Data Warehouse. A következő példában a tűzfal csak más Azure-erőforrások számára van nyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database és SQL Data Warehouse kommunikáció az 1433-as porton keresztül. Ha vállalati hálózaton belülről próbál csatlakozni, lehetséges, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, akkor nem fog tudni csatlakozni az Azure SQL Server-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg a 1433-es portot.
>


## <a name="create-a-data-warehouse"></a>Adattárház létrehozása
Ez a példa egy adattárházat hoz létre a korábban definiált változók használatával.  Meghatározza a szolgáltatás célját DW100c, amely az adattárház alacsonyabb díjas kiindulási pontja. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

A szükséges paraméterek a következők:

* **RequestedServiceObjectiveName**: a kért [adatraktár-egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) mennyisége. Az érték növelése növeli a számítási költségeket. A támogatott értékek listáját a [memória és a Egyidejűség korlátai](memory-concurrency-limits.md)című részben tekintheti meg.
* **Databasename**: a létrehozandó SQL Data Warehouse neve.
* **Kiszolgálónév**: a létrehozáshoz használt kiszolgáló neve.
* **ResourceGroupName**: az Ön által használt erőforráscsoport. Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Edition**: Egy SQL Data Warehouse létrehozásához a „DataWarehouse” értéket kell megadni.

A választható paraméterek a következők:

- **CollationName**: Ha nincs megadva, az alapértelmezett rendezés: SQL_Latin1_General_CP1_CI_AS. A rendezés nem módosítható egy adatbázison.
- **MaxSizeBytes**: az adatbázis alapértelmezett maximális mérete 240TB. A maximális méret korlátozza a sortárindex létrehozását-adatmennyiséget. Az oszlopos adatmennyiségek korlátlan tárhelyet biztosítanak.

A paraméter beállításairól további információt a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)című témakörben talál.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül. 

> [!TIP]
> Ha azt tervezi, hogy továbbra is a későbbi gyors útmutatók használatával dolgozik, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti a rövid útmutatóban létrehozott összes erőforrást a Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Következő lépések

Létrehozott egy adattárházat, létrehozott egy tűzfalszabályot, kapcsolódott az adattárházhoz, és futtat néhány lekérdezést. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.
> [!div class="nextstepaction"]
>[Az adatSQL Data Warehouseba való betöltés](load-data-from-azure-blob-storage-using-polybase.md)
