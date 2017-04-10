---
title: "Azure PowerShell: SQL-adatbázis létrehozása | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat az Azure Portal használatával."
keywords: "oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 90dc7e4a07f2a3c514c25b4031128f4df4e32aab
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Önálló Azure SQL-adatbázis létrehozása a PowerShell használatával

A PowerShell használatával Azure-erőforrások hozhatók létre és kezelhetők a parancssorból vagy szkriptekben. Ez az útmutató azt ismerteti, hogyan helyezhet üzembe Azure SQL-adatbázist a PowerShell használatával egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) található [Azure-erőforráscsoportban](../azure-resource-manager/resource-group-overview.md).

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy az [Azure PowerShell](/powershell/azureps-cmdlets-docs) legújabb verziója van telepítve. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Hozzon létre egy logikai kiszolgálót

Hozzon létre egy [Azure SQL Database logikai kiszolgálót](sql-database-features.md) a [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) paranccsal. A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel. A következő példában létrehozunk egy véletlenszerűen elnevezett kiszolgálót az erőforráscsoportban egy `ServerAdmin` nevű és `ChangeYourAdminPassword1` jelszavú rendszergazdai bejelentkezéssel. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL Database kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md) a [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) paranccsal. A kiszolgálószintű tűzfalszabályok lehetővé teszik, hogy külső alkalmazások, például az SQL Server Management Studio vagy az SQLCMD segédprogram az SQL Database szolgáltatás tűzfalán keresztül csatlakozzon egy SQL-adatbázishoz. A következő példában létrehozunk egy tűzfalszabályt egy előre meghatározott címtartományhoz, amely ebben a példában az IP-címek teljes lehetséges tartományát lefedi. Cserélje le ezeket az előre meghatározott értékeket a külső IP-címére vagy IP-címtartományára. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist

Hozzon létre egy üres, [S0 teljesítményszintű](sql-database-service-tiers.md) SQL-adatbázist a kiszolgálón a [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) paranccsal. Az alábbi példa egy `mySampleDatabase` nevű adatbázist hoz létre. Igény szerint cserélje le ezt az előre meghatározott értéket.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épít. Ha azt tervezi, hogy az ezt rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti az ehhez a rövid útmutatóhoz létrehozott összes erőforrást.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

- Az SQL Server Management Studióval történő csatlakozáshoz és lekérdezéshez lásd [az SSMS segítségével történő csatlakozással és lekérdezéssel](sql-database-connect-query-ssms.md) foglalkozó témakört.
- Ha a Visual Studióval szeretne kapcsolódni, lásd a [Visual Studióval végzett csatlakozásról és lekérdezésről](sql-database-connect-query.md) szóló témakört.
* Az SQL Database műszaki áttekintését az [SQL Database szolgáltatást ismertető](sql-database-technical-overview.md) témakörben találja.

