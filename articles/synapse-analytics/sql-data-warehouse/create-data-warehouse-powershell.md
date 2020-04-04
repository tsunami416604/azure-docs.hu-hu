---
title: Synapse SQL-készlet létrehozása és lekérdezése az Azure PowerShell használatával
description: Gyorsan létrehozhat egy Synapse SQL-készlet logikai kiszolgálót egy kiszolgálószintű tűzfalszabállyal az Azure PowerShell használatával.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 57564e9dffd6022e1e4fe464b4b26a5bb8eb318b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631325"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-with-azure-powershell"></a>Rövid útmutató: Synapse SQL-készlet létrehozása és lekérdezése az Azure PowerShell használatával

Hozzon létre egy Synapse SQL-készletet (adatraktárt) az Azure Synapse Analytics-ben az Azure PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

> [!IMPORTANT]
> Sql készlet létrehozása új számlázható szolgáltatást eredményezhet.  További információ: [Azure Synapse Analytics pricing](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

A Használt előfizetés megtekintéséhez futtassa a [Get-AzSubscription futtassa.](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```powershell
Get-AzSubscription
```

Ha az alapértelmezettnél eltérő előfizetést kell használnia, futtassa a [Set-AzContext programot.](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

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
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Hozzon létre egy logikai kiszolgálót

Hozzon létre egy [Azure SQL-logikai kiszolgálót](../../sql-database/sql-database-logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) paranccsal. A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel. A következő példa létrehoz egy véletlenszerűen elnevezett kiszolgálót `ServerAdmin` az erőforráscsoportban egy rendszergazdai névvel és egy `ChangeYourAdminPassword1`jelszóval. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL-kiszolgálószintű tűzfalszabályt](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) paranccsal. A kiszolgálószintű tűzfalszabály lehetővé teszi, hogy egy külső alkalmazás, például az SQL Server Management Studio vagy az SQLCMD segédprogram az SQL-készletszolgáltatás tűzfalán keresztül csatlakozzon egy SQL-készlethez.

A következő példában a tűzfal csak más Azure-erőforrások számára van nyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Az SQL-végpontok az 1433-as porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, előfordulhat, hogy az 1433-as porton keresztüli kimenő forgalmat a hálózat tűzfala nem engedélyezi. Ha igen, akkor csak akkor tud csatlakozni az Azure SQL-kiszolgálóhoz, ha az informatikai részleg megnyitja az 1433-as portot.
>

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

A következő példa létrehoz egy SQL-készletet a korábban definiált változók használatával.  A szolgáltatáscélkitűzést DW100c néven határozza meg, amely az SQL-készlet alacsonyabb költségű kiindulópontja.

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

* **RequestedServiceObjectiveName**: A kért [adattárház egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) mennyisége. Ennek az összegnek a növelése növeli a számítási költséget. A támogatott értékek listáját a [memória- és egyidejűségi korlátok című témakörben tetszései között található.](memory-concurrency-limits.md)
* **DatabaseName**: A létrehozás során létrehozott SQL-készlet neve.
* **Kiszolgálónév**: A létrehozáshoz használt kiszolgáló neve.
* **ResourceGroupName**: A használt erőforráscsoport. Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Kiadás**: SQL-készlet létrehozásához "DataWarehouse" legyen.

A választható paraméterek a következők:

* **CollationName**: Ha nincs megadva, az alapértelmezett rendezés: SQL_Latin1_General_CP1_CI_AS. Az adategyeztetés nem módosítható az adatbázisban.
* **MaxSizeBytes**: Az adatbázis alapértelmezett maximális mérete 240 TB. A maximális méret korlátozza a sortár adatait. Az oszlopos adatok korlátlan tárhelye van.

A paraméterbeállításokkal kapcsolatos további információkért lásd: [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül.

> [!TIP]
> Ha azt tervezi, hogy folytatja a munkát a későbbi rövid útmutatók, ne törölje az erőforrásokat létrehozott ebben a rövid útmutatóban. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az azure-beli portálon ez a rövid útmutató által létrehozott összes erőforrást.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>További lépések

Létrehozott egy SQL-készletet, létrehozott egy tűzfalszabályt, amely az SQL-készlethez kapcsolódik, és futtat néhány lekérdezést. További információért folytassa az [Adatok betöltése az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md) cikkhez.
