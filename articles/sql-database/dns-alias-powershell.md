---
title: A DNS-alias az Azure SQL PowerShell |} A Microsoft Docs
description: Például a New-AzureRMSqlServerDNSAlias PowerShell-parancsmagok lehetővé teszik új ügyfélgépi kapcsolatokat átirányítása egy másik Azure SQL Database-kiszolgálóhoz, érintse meg minden olyan ügyfél-konfiguráció nélkül.
keywords: DNS-sql-adatbázis
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,amagarwa,maboja
manager: craigg
ms.date: 02/05/2018
ms.openlocfilehash: 095e6c6d59bf73bb74e2d8fbe3d1506601ab533e
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471118"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Az Azure SQL Database DNS-alias PowerShell

Ez a cikk azt mutatja be, hogyan kezelheti DNS-alias az Azure SQL Database PowerShell-parancsfájl. A szkript futtatása a következő parancsmagokat, amelyek a következő műveleteket hajtja végre:

A példakódban alkalmazott parancsmagok a következők:

- [Új-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): egy új DNS-alias létrehozása az Azure SQL Database szolgáltatás rendszerben. Az alias az Azure SQL Database-kiszolgáló 1 hivatkozik.
- [A GET-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): lekérés és listázás 1 SQL-adatbázis-kiszolgálóhoz rendelt összes DNS-aliasokat.
- [A Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): módosítja a kiszolgáló nevét, amely az alias konfigurálva van kiszolgálóról 1, 2. az SQL DB kiszolgáló hivatkoznak.
- [A Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): a DNS-alias eltávolítása az SQL DB kiszolgáló 2, az alias nevét.

Az előző PowerShell-parancsmagok lettek hozzáadva a **azurerm.SQL-hez** modul 5.1.1-es verziótól kezdve.

## <a name="dns-alias-in-connection-string"></a>DNS-aliast a kapcsolati karakterlánc

Egy adott Azure SQL Database-kiszolgálóhoz csatlakozhat, például az SQL Server Management Studio (SSMS) ügyfél biztosíthat helyett a tényleges kiszolgáló neve DNS-alias nevét. A következő példa kiszolgáló karakterlánc alias *bármely-egyedi-alias-name* váltja fel a négy csomópontból álló kiszolgáló karakterlánc első ponttal elválasztott csomópont:

- Példa kiszolgáló karakterlánc: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Előfeltételek

Ha szeretne a bemutatóra ebben a cikkben megadott PowerShell-parancsprogram futtatása, a következő előfeltételek vonatkoznak:

- Egy Azure-előfizetések és -fiók. Az ingyenes próbaverzióra, kattintson a [ https://azure.microsoft.com/free/ ] [ https://azure.microsoft.com/free/].
- Az Azure PowerShell-modult, a parancsmag **a New-AzureRMSqlServerDNSAlias**.
  - Telepítéshez vagy frissítéshez olvassa [Azure PowerShell-modul telepítését][install-azurerm-ps-84p].
  - Futtatás `Get-Module -ListAvailable AzureRM;` a PowerShellben\_ise.exe, a verzió megkereséséhez.
- Két Azure SQL Database-kiszolgálók.

## <a name="code-example"></a>Mintakód

A következő PowerShell-példakód először konstansértékekkel hozzárendelése értékkel a változókat. A kód futtatásához először szerkesztenie kell az összes a helyőrzők értékeit a rendszer tényleges értékekre. Vagy csak akkor is tanulmánya a kódot. És a konzol kimenete a kód is.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>A PowerShell-példa tényleges konzolkimenete

A következő konzolkimenet volt másolt és a egy tényleges futtató beillesztett.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>További lépések

A DNS-Alias funkció teljes ismertetése az SQL Database, lásd: [DNS-alias az Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md
