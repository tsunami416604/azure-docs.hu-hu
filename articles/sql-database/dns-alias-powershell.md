---
title: PowerShell DNS-aliashoz Azure SQL
description: A New-AzSqlServerDNSAlias PowerShell-parancsmagok lehetővé teszik az új ügyfélkapcsolatok átirányítását egy másik Azure SQL Database-kiszolgálóra anélkül, hogy meg kellene érintenie az ügyfél konfigurációját.
keywords: DNS SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: ebbca309b7f6acd071c7075e63e670a8efa49f4e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685324"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell DNS-aliashoz Azure SQL Database

Ez a cikk egy PowerShell-szkriptet tartalmaz, amely bemutatja, hogyan kezelhető a Azure SQL Database DNS-aliasa. A parancsfájl a következő parancsmagokat futtatja, amelyek a következő műveleteket végzik el:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A kódban használt parancsmagok a következők:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): új DNS-Alias létrehozása a Azure SQL Database szolgáltatási rendszerben. Az alias az 1. Azure SQL Database kiszolgálóra hivatkozik.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): az SQL db Server 1 rendszerhez rendelt összes DNS-alias beolvasása és listázása.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): módosítja annak a kiszolgálónak a nevét, amelyre az alias hivatkozik, az 1. kiszolgálóról az SQL db Server 2 verzióra.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): az alias nevének használatával távolítsa el a DNS-aliast az SQL db Server 2 kiszolgálóról.

## <a name="dns-alias-in-connection-string"></a>DNS-alias a kapcsolatok karakterláncában

Egy adott Azure SQL Database-kiszolgáló összekapcsolásához az ügyfél, például a SQL Server Management Studio (SSMS) a DNS-alias nevét is megadhatja a True Server neve helyett. A következő példa kiszolgálói sztringben az alias- *Unique-alias-Name* az első pont-tagolt csomópontot helyettesíti a négy csomópontos kiszolgáló sztringben:

- Példa kiszolgálói sztringre: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Előfeltételek

Ha a jelen cikkben megadott demo PowerShell-szkriptet szeretné futtatni, a következő előfeltételek érvényesek:

- Egy Azure-előfizetés és-fiók. Az ingyenes próbaverzióhoz kattintson a [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/]elemre.
- Azure PowerShell modul, a **New-AzSqlServerDNSAlias**parancsmaggal.
  - A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését][install-Az-ps-84p] ismertető cikket.
  - Futtassa a `Get-Module -ListAvailable Az;` a PowerShell\_ISE. exe fájlból a verzió megkereséséhez.
- Két Azure SQL Database-kiszolgáló.

## <a name="code-example"></a>Mintakód

A következő PowerShell-kód példa a literál értékeknek több változóhoz való hozzárendelésével kezdődik. A kód futtatásához először szerkesztenie kell az összes helyőrző értékét, hogy az megfeleljen a rendszer valós értékeinek. Vagy megvizsgálhatja a kódot. A kód konzoljának kimenetét is megadja.

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
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>A konzol tényleges kimenete a PowerShell-példából

A következő konzol kimenetét a rendszer átmásolta és beillesztette egy tényleges futtatásból.

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

A SQL Database DNS-alias funkciójának részletes ismertetését lásd: [Azure SQL Database DNS-aliasa][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
