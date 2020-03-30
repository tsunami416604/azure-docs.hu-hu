---
title: PowerShell virtuális hálózati végpontokhoz és szabályok egy- és készletezésű adatbázisokhoz
description: PowerShell-parancsfájlokat biztosít az Azure SQL Database és az Azure Synapse virtuális szolgáltatásvégpontjainak létrehozásához és kezeléséhez.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124699"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: Hozzon létre egy virtuális szolgáltatás végpontés virtuális hálózati szabály SQL

*A virtuális hálózati szabályok* egy tűzfalbiztonsági szolgáltatás, amely szabályozza, hogy az adatbázis-kiszolgáló az egyetlen adatbázisok és rugalmas készlet az Azure [SQL Database](sql-database-technical-overview.md) vagy az [azure-beli adatbázisok](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) a virtuális hálózatok ban küldött kommunikáció.

> [!IMPORTANT]
> Ez a cikk az Azure SQL-kiszolgálóra, valamint az Azure Synapse-ban az Azure SQL-kiszolgálón létrehozott SQL Database-adatbázisra és adattárházra is vonatkozik. Az egyszerűség kedvéért az SQL Database az SQL Database és az Azure Synapse hivatkozva használatos. Ez a cikk *nem* vonatkozik az Azure SQL Database **felügyelt példányüzembe** helyezésére, mert nem rendelkezik szolgáltatás-végpontdal.

Ez a cikk egy PowerShell-parancsfájlt tartalmaz, amely a következő műveleteket követi:

1. Létrehoz egy Microsoft Azure *virtuális szolgáltatás végpontot* az alhálózaton.
2. Hozzáadja a végpontot az Azure SQL Database-kiszolgáló tűzfalához, és *virtuális hálózati szabályt*hoz létre.

A szabály létrehozásának motivációit a következő ismerteti: [Virtuális szolgáltatás végpontok az Azure SQL Database-hez.][sql-db-vnet-service-endpoint-rule-overview-735r]

> [!TIP]
> Ha mindössze annyit kell, hogy értékelje vagy adja hozzá a virtuális szolgáltatás *végponttípus nevét* az alhálózathoz, átugorhatja a [közvetlenebb PowerShell-parancsfájlt.](#a-verify-subnet-is-endpoint-ps-100)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

## <a name="major-cmdlets"></a>Főbb parancsmagok

Ez a cikk kiemeli a **New-AzSqlServerVirtualNetworkRule** parancsmag, amely hozzáadja az alhálózati végpontot az Azure SQL Database-kiszolgáló hozzáférés-vezérlési listájához (ACL), ezáltal létrehozva egy szabályt.

Az alábbi lista a **New-AzSqlServerVirtualNetworkRule**hívásra való felkészüléshez futtatandó többi *fő* parancsmag sorozatát mutatja be. Ebben a cikkben ezek a hívások a ["Virtuális hálózati szabály" 3.](#a-script-30)

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Alhálózati objektum létrehozása.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Létrehozza a virtuális hálózatot, így az alhálózat.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Virtuális szolgáltatás végpontot rendel az alhálózathoz.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Megőrzi a virtuális hálózaton végrehajtott frissítéseket.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Miután az alhálózat végpont, hozzáadja az alhálózatot virtuális hálózati szabályként az Azure SQL Database-kiszolgáló ACL-jéhez.
   - Ez a parancsmag a **-IgnoreMissingVNetServiceEndpoint**paramétert kínálja, amely az Azure RM PowerShell-modul 5.1.1-es verziójából indul.

## <a name="prerequisites-for-running-powershell"></a>A PowerShell futtatásának előfeltételei

- Már bejelentkezhet az Azure-ba, például az [Azure Portalon][http-azure-portal-link-ref-477t]keresztül.
- PowerShell-parancsfájlok már futtatható.

> [!NOTE]
> Győződjön meg arról, hogy a szolgáltatásvégpontok be vannak kapcsolva a kiszolgálóhoz hozzáadni kívánt virtuális hálózat/alhálózat számára, ellenkező esetben a virtuális hálózat tűzfalszabályának létrehozása sikertelen lesz.

## <a name="one-script-divided-into-four-chunks"></a>Egy szkript négy részre osztva

A bemutató PowerShell-parancsfájl kisebb parancsfájlok sorozatára van osztva. A divízió megkönnyíti a tanulást és rugalmasságot biztosít. A parancsfájlokat a megadott sorrendben kell futtatni. Ha nincs ideje most futtatni a szkripteket, a tényleges teszt kimenet után jelenik meg script 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>1. parancsfájl: Változók

Ez az első PowerShell-parancsfájl értékeket rendel a változókhoz. A következő parancsfájlok ezektől a változóktól függenek.

> [!IMPORTANT]
> A parancsfájl futtatása előtt módosíthatja az értékeket, ha szeretné. Ha például már rendelkezik erőforráscsoporttal, érdemes lehet az erőforráscsoport nevét hozzárendelt értékként írni.
>
> Az előfizetés nevét a parancsfájlba kell beírni.

### <a name="powershell-script-1-source-code"></a>PowerShell-parancsfájl 1 forráskódja

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>2. parancsfájl: Előfeltételek

Ez a parancsfájl előkészíti a következő parancsfájlt, ahol a végpont művelet. Ez a parancsfájl a következő felsorolt elemeket hozza létre, de csak akkor, ha még nem léteznek. Kihagyhatja a 2-es parancsfájlt, ha biztos benne, hogy ezek az elemek már léteznek:

- Azure-erőforráscsoport
- Azure SQL Adatbázis-kiszolgáló

### <a name="powershell-script-2-source-code"></a>PowerShell-parancsfájl 2-es forráskódja

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>3. parancsfájl: Végpont és szabály létrehozása

Ez a parancsfájl alhálózattal hoz létre virtuális hálózatot. Ezután a parancsfájl hozzárendeli a **Microsoft.Sql** végpont típusát az alhálózathoz. Végül a parancsfájl hozzáadja az alhálózatot az SQL Database-kiszolgáló hozzáférés-vezérlési listájához (ACL), így létrehoz egy szabályt.

### <a name="powershell-script-3-source-code"></a>PowerShell-parancsfájl 3-as forráskódja

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>4. forgatókönyv: Tisztítás

Ez a végső parancsfájl törli azokat az erőforrásokat, amelyeket az előző parancsfájlok hoztak létre a bemutatóhoz. A parancsfájl azonban megerősítést kér, mielőtt törölné a következőket:

- Azure SQL Adatbázis-kiszolgáló
- Azure erőforráscsoport

Futtathatja script 4 bármikor után script 1 befejeződik.

### <a name="powershell-script-4-source-code"></a>PowerShell-parancsfájl 4-es forráskódja

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Annak ellenőrzése, hogy az alhálózat végpont-e

Lehet, hogy van egy alhálózat, amely már hozzá van rendelve a **Microsoft.Sql** típusnév, ami azt jelenti, hogy már egy virtuális szolgáltatás végpont. Az Azure [Portal][http-azure-portal-link-ref-477t] segítségével virtuális hálózati szabályt hozhat létre a végpontról.

Vagy lehet, hogy nem biztos abban, hogy az alhálózat rendelkezik a **Microsoft.Sql** típusnév. Az alábbi PowerShell-parancsfájl futtatásával végrehajthatja az alábbi műveleteket:

1. Győződjön meg arról, hogy az alhálózat rendelkezik-e **microsoft.sql** típusnévvel.
2. Ha nincs meg, rendelje hozzá a típusnevet.
    - A parancsfájl a *megerősítést*kéri, mielőtt a hiányzó típusnevet alkalmazna.

### <a name="phases-of-the-script"></a>A szkript fázisai

A PowerShell-parancsfájl fázisai:

1. Jelentkezzen be az Azure-fiókjába, amelyre ps-munkamenetenként csak egyszer van szükség.  Változók hozzárendelése.
2. Keresse meg a virtuális hálózatot, majd az alhálózatot.
3. Az alhálózat **Microsoft.Sql** végpontkiszolgáló-típusként van címkézve?
4. Adjon hozzá egy **Microsoft.Sql**típusú virtuális szolgáltatás végpontot az alhálózaton.

> [!IMPORTANT]
> A parancsfájl futtatása előtt a parancsfájl tetején a $-változókhoz rendelt értékeket kell szerkesztenie.

### <a name="direct-powershell-source-code"></a>Közvetlen PowerShell-forráskód

Ez a PowerShell-parancsfájl nem frissít semmit, kivéve, ha igennel válaszol, ha megerősítést kér. A parancsfájl hozzáadhatja a **Microsoft.Sql** típusnevet az alhálózathoz. De a parancsfájl csak akkor próbálja meg a hozzáadást, ha az alhálózatnem rendelkezik a típusnévvel.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
