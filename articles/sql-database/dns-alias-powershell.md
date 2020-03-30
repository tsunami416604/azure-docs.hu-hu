---
title: PowerShell DNS-aliashoz
description: A PowerShell-parancsmagok, például a New-AzSqlServerDNSAlias lehetővé teszik az új ügyfélkapcsolatok átrendezését egy másik Azure SQL Database-kiszolgálóra anélkül, hogy bármilyen ügyfélkonfigurációt meg kellene érintenie.
keywords: dns sql adatbázis
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420406"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell dns aliashoz az Azure SQL Database-hez

Ez a cikk egy PowerShell-parancsfájlt tartalmaz, amely bemutatja, hogyan kezelheti az Azure SQL Database DNS-aliasait.

> [!NOTE]
> Ez a cikk frissült az Azure PowerShell Az modul vagy az Azure CLI használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig.
>
> Az Az modul és az AzureRM-kompatibilitás ról az [Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben olvashat bővebben. A telepítési útmutatót az [Azure PowerShell telepítése](/powershell/azure/install-az-ps) vagy [az Azure CLI telepítése című](/cli/azure/install-azure-cli)témakörben találja.

## <a name="dns-alias-in-connection-string"></a>DNS-alias a kapcsolati karakterláncban

Egy adott Azure SQL Database-kiszolgáló csatlakoztatásához egy ügyfél, például az SQL Server Management Studio (SSMS) a dns-alias nevet adja meg a valódi kiszolgálónév helyett. A következő példakiszolgálói karakterláncban az *alias bármely egyedi alias-name* lecseréli a négy csomópontkiszolgálói karakterlánc első pont-tagolt csomópontját:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Előfeltételek

Ha a jelen cikkben megadott demó PowerShell-parancsfájlt szeretné futtatni, a következő előfeltételek érvényesek:

- Egy Azure-előfizetés és -fiók ingyenes próbaverzióért tekintse meg az [Azure-próbaverziókat](https://azure.microsoft.com/free/)
- Két Azure SQL-adatbázis-kiszolgáló

## <a name="example"></a>Példa

A következő kódpélda úgy kezdődik, hogy több változóhoz rendel literális értékeket.

A kód futtatásához a helyőrző értékeket úgy szerkesztheti, hogy azok megfeleljenek a rendszer valós értékeinek.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A felhasznált parancsmagok a következők:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): DNS-aliast hoz létre az Azure SQL Database szolgáltatásrendszerben. Az alias az 1- es adatbázis-kiszolgálóra hivatkozik.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Az SQL DB 1 kiszolgálóhoz rendelt összes alias bekésése és listázása.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Módosítja azt a kiszolgálónevet, amelyre az alias hivatkozik, az 1-es kiszolgálótól a 2-es kiszolgálóig.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Az alias eltávolítása az adatbázis-kiszolgálóról 2, az alias nevének használatával.

A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

Használja `Get-Module -ListAvailable Az` a *powershell\_ise.exe*, hogy megtalálja a verziót.

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

- [az sql server dns-alias létrehozása:](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)DNS-alias létrehozása az Azure SQL Database szolgáltatásrendszerben. Az alias az 1- es adatbázis-kiszolgálóra hivatkozik.
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Az SQL DB 1 kiszolgálóhoz rendelt összes alias bekéselése és listázása.
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Módosítja azt a kiszolgálónevet, amelyre az alias hivatkozik, az 1-es kiszolgálótól a 2-es kiszolgálóig.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Az alias eltávolítása az adatbázis-kiszolgálóról 2, az alias nevének használatával.

A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.

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

Az SQL Database DNS-aliasszolgáltatásának teljes magyarázatát az [Azure SQL-adatbázis DNS-aliasa című témakörben található.](dns-alias-overview.md)
