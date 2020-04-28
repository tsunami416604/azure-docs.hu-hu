---
title: PowerShell DNS-aliashoz
description: A New-AzSqlServerDNSAlias PowerShell-parancsmagok lehetővé teszik az új ügyfélkapcsolatok átirányítását egy másik Azure SQL Database-kiszolgálóra anélkül, hogy meg kellene érintenie az ügyfél konfigurációját.
keywords: DNS SQL Database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74420406"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell DNS-aliashoz Azure SQL Database

Ez a cikk egy PowerShell-szkriptet tartalmaz, amely bemutatja, hogyan kezelhető a Azure SQL Database DNS-aliasa.

> [!NOTE]
> Ez a cikk a Azure PowerShell az Module vagy az Azure CLI használatára lett frissítve. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig.
>
> Ha többet szeretne megtudni az az Module és a AzureRM kompatibilitásáról, tekintse meg [a Azure PowerShell az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakört. A telepítési utasításokért lásd: [Azure PowerShell telepítése](/powershell/azure/install-az-ps) vagy az [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>DNS-alias a kapcsolatok karakterláncában

Egy adott Azure SQL Database-kiszolgáló összekapcsolásához az ügyfél, például a SQL Server Management Studio (SSMS) a DNS-alias nevét is megadhatja a True Server neve helyett. A következő példa kiszolgálói sztringben az alias- *Unique-alias-Name* az első pont-tagolt csomópontot helyettesíti a négy csomópontos kiszolgáló sztringben:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Előfeltételek

Ha a jelen cikkben megadott demo PowerShell-szkriptet szeretné futtatni, a következő előfeltételek érvényesek:

- Azure-előfizetéssel és-fiókkal, az ingyenes próbaverzióért lásd: [Azure-próbaverziók](https://azure.microsoft.com/free/)
- Két Azure SQL Database-kiszolgáló

## <a name="example"></a>Példa

A következő kódrészlet a literál értékeknek több változóhoz való hozzárendelésével kezdődik.

A kód futtatásához szerkessze a helyőrző értékeit úgy, hogy azok megfeleljenek a rendszer valós értékeinek.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A használt parancsmagok a következők:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): DNS-aliast hoz létre a Azure SQL Database szolgáltatási rendszerben. Az alias az 1. adatbázis-kiszolgálóra hivatkozik.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): az SQL db Server 1-hez rendelt összes alias beolvasása és listázása.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): módosítja annak a kiszolgálónak a nevét, amelyre az alias hivatkozik, az 1. kiszolgálóról a 2. kiszolgálóra.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): távolítsa el az aliast a 2. adatbázis-kiszolgálóról az alias nevével.

A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

A `Get-Module -ListAvailable Az` verzió megkereséséhez használja a *PowerShell\_ISE. exe fájlját*.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A használt parancsok a következők:

- az [SQL Server DNS-alias Create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): létrehoz egy DNS-aliast a Azure SQL Database szolgáltatási rendszerben. Az alias az 1. adatbázis-kiszolgálóra hivatkozik.
- az [SQL Server DNS-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): az SQL db Server 1-hez rendelt összes alias beolvasása és listázása.
- az [SQL Server DNS-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): módosítja annak a kiszolgálónak a nevét, amelyre az alias hivatkozik, az 1. kiszolgálóról a 2. kiszolgálóra.
- az [SQL Server DNS-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): távolítsa el az aliast a 2. adatbázis-kiszolgálóról az alias nevével.

A telepítéshez vagy a frissítéshez lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>További lépések

A SQL Database DNS-alias funkciójának részletes ismertetését lásd: [DNS-alias az Azure SQL Database-](dns-alias-overview.md)hez.
