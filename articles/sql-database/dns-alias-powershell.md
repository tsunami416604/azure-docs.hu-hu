---
title: DNS-alias az Azure SQL PowerShell |} Microsoft Docs
description: "Például a New-AzureRMSqlServerDNSAlias PowerShell-parancsmagok lehetővé teszik új ügyfélkapcsolatok átirányítja egy másik Azure SQL Database-kiszolgálóhoz anélkül, hogy minden ügyfél-konfigurációt érint."
keywords: "DNS-sql-adatbázis"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: 
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/31/2018
ms.reviewer: dmalik;amagarwa;maboja
ms.author: genemi
ms.openlocfilehash: 9d4aa9ddc0d77556e1511ea0a7914d94a227895f
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>A DNS-Alias az Azure SQL Database PowerShell

Ez a cikk bemutatja, hogyan kezelheti a DNS-alias az Azure SQL Database PowerShell parancsfájlt tartalmaz. A parancsprogram lefut a következő parancsmagokat, amelyek a következő műveleteket hajtja végre:


A példakódban a parancsmagok a következők:
- [Új AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): egy új DNS-alias létrehozása az Azure SQL Database szolgáltatás rendszerben. Az alias az Azure SQL Database-kiszolgálóhoz 1 hivatkozik.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Get, és az SQL adatbázis-kiszolgálót 1 rendelt összes DNS-alias listája.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): módosítja a kiszolgáló nevét, amely az alias konfigurálva van tekintse meg, SQL DB server 2 1-kiszolgálóról.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): a DNS-alias eltávolítása SQL adatbázis-kiszolgálót, 2, a-alias nevét.


Az előző PowerShell-parancsmagok lettek hozzáadva a **AzureRm.Sql** 5.1.1-es verziójától kezdve modul.




#### <a name="dns-alias-in-connection-string"></a>A kapcsolati karakterláncban a DNS-alias

Adott Azure SQL Database-kiszolgálóhoz kapcsolódni, például az SQL Server Management Studio (SSMS) ügyfél biztosíthat igaz kiszolgáló neve helyett a DNS-alias nevét. A következő példa kiszolgáló karakterláncban a alias *bármely-egyedi-aliasnév* a felváltja a négy csomópont server karakterlánc első ponttal elválasztott csomópontja:
- Példa kiszolgáló karakterlánc: `any-unique-alias-name.database.windows.net`.



## <a name="prerequisites"></a>Előfeltételek

Ha azt szeretné, a bemutató cikkben megadott PowerShell-parancsprogram futtatásához, a következő előfeltételek vonatkoznak:

- Azure-előfizetések és fiókot. Kattintson egy ingyenes próbaverzióra [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].

- Az Azure PowerShell-modult, parancsmaggal **New-AzureRMSqlServerDNSAlias**.
    - Telepítéséhez vagy frissítéséhez, lásd: [telepítése Azure PowerShell modul][install-azurerm-ps-84p].
    - Futtatás `Get-Module -ListAvailable AzureRM;` PowerShell\_ise.exe, a verzió található.

- Két Azure SQL Database-kiszolgálók.

## <a name="code-example"></a>Mintakód

A következő PowerShell-példakód indításhoz literálértékek hozzárendelése több változót. A kódra, először módosítsa a rendszer valós értékekre helyőrző értékeket. Vagy csak is tanulmányozni a kódot. És a konzol kimenetét a kódot is.


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
    Login-AzureRmAccount -SubscriptionName $SubscriptionName;
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


#### <a name="actual-console-output-from-the-powershell-example"></a>Tényleges konzol kimenetét a PowerShell-példa

A következő konzol kimeneti volt másolja, és egy tényleges futtató beillesztett.


```
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

A DNS-Alias funkció teljes leírását az SQL Database, lásd: [DNS-alias az Azure SQL Database][dns-alias-overview-37v].



<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

