---
title: "A virtuális hálózati Szolgáltatásvégpontok és szabályok az SQL PowerShell |} Microsoft Docs"
description: "PowerShell-parancsfájlok létrehozását és kezelését virtuális Szolgáltatásvégpontok az Azure SQL adatbázis biztosít."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/15/2017
ms.author: genemi
ms.openlocfilehash: 9f58ea34dad5d4436c13b64653040bd2a57c299e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="use-powershell-to-create-a-virtual-service-endpoint-and-rule-for-azure-sql-database"></a>Hozzon létre egy virtuális végpontját, és a szabály az Azure SQL Database PowerShell használatával

Ez a cikk nyújt, és elmagyarázza, egy PowerShell-parancsfájlt, amely a következő műveleteket hajtja végre:

1. Létrehoz egy Microsoft Azure *virtuális szolgáltatásvégpont* az alhálózaton.
2. A végpont hozzáadása a tűzfal az Azure SQL Database-kiszolgáló létrehozásához egy *virtuális hálózati szabály*.

Olyan szabály létrehozására a célokat ismerteti: [virtuális Szolgáltatásvégpontok az Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Ha szüksége értékeléséhez, vagy adja hozzá a virtuális szolgáltatásvégpont *típusnév* SQL-adatbázis az alhálózathoz, ugorjon előre szélesebb [PowerShell-parancsfájl közvetlen](#a-verify-subnet-is-endpoint-ps-100).

#### <a name="major-cmdlets"></a>A jelentős parancsmagok

Ez a cikk emeli ki nevű parancsmag **New-AzureRmSqlServerVirtualNetworkRule**, amely a alhálózati végpont hozzáadása az Azure SQL Database-kiszolgálóhoz, így a szabályt hoz létre hozzáférési szabálygyűjtemény (ACL).

Az alábbi listán azt mutatja be, a másik *fő* parancsmagokat, futtatnia kell a hívása előkészítése **New-AzureRmSqlServerVirtualNetworkRule**. Ebben a cikkben az adott hívások tagolni [parancsfájl 3 "virtuális hálózati szabály"](#a-script-30):

1. [Új AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig): egy alhálózat-objektumot hoz létre.

2. [Új-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork): adjon neki az alhálózat a virtuális hálózatot hoz létre.

3. [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig): az alhálózat egy virtuális végpontot rendel.

4. [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork): továbbra is fennáll a frissítéseket a virtuális hálózat.

5. **Új AzureRmSqlServerVirtualNetworkRule**: Miután az alhálózat egy végpontot, hozzáadja az alhálózat virtuális hálózati szabály, a hozzáférés-vezérlési lista, az Azure SQL Database-kiszolgálóhoz.

#### <a name="prerequisites-for-running-powershell"></a>A PowerShell futtatásához Előfeltételek

- Már bejelentkezhet az Azure-ba, többek között keresztül a [Azure-portálon][http-azure-portal-link-ref-477t].
- PowerShell-parancsfájlok már futtathatja.

#### <a name="one-script-divided-into-four-chunks"></a>Egy parancsfájl négy adattömbök felosztva.

A PowerShell-parancsfájl bemutató kisebb parancsfájlok sorozatát van osztva. Megkönnyíti a tanulási és rugalmasságot biztosít. A parancsfájlok a megjelölt sorrendben kell futtatni. Ha most idő nem rendelkezik a parancsfájlok futtatásához, a tényleges tesztkimenet parancsfájl 4 után jelenik meg.






<a name="a-script-10" />

## <a name="script-1-variables"></a>Parancsfájl-1: változók

Az első PowerShell-parancsfájl változók hozzárendeli az értékeket. A későbbi parancsfájlok változókhoz függ.

> [!IMPORTANT]
> Ez a parancsfájl futtatása előtt szerkesztheti az értékeket, ha szeretné. Például ha már rendelkezik egy erőforráscsoport, érdemes az erőforráscsoport neve, a hozzárendelt érték szerkesztéséhez.
>
>  Az előfizetés nevét módosítani kell a parancsfájlba.

#### <a name="powershell-script-1-source-code"></a>PowerShell parancsfájl 1 forráskód

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Login-AzureRmAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName $SubscriptionName;

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

## <a name="script-2-prerequisites"></a>Parancsfájl-2: Előfeltételek

Ez a parancsfájl előkészíti a következő parancsfájlt, ahol a végpont művelete. Ez a parancsfájl létrehozza, a következő elemeket, de csak felsorolt, ha még nem léteznek. Parancsfájl 2 kihagyhatja, ha biztos benne, hogy ezek az elemek már létezik:

- Azure-erőforráscsoportot
- Az Azure SQL Database-kiszolgálóhoz

#### <a name="powershell-script-2-source-code"></a>PowerShell parancsfájl 2 forráskód

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzureRmResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzureRmResourceGroup `
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

$sqlDbServer = Get-AzureRmSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
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

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzureRmSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```






<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Parancsfájl 3: A végpont és a szabály létrehozása

Ez a parancsfájl egy virtuális hálózati alhálózat hoz létre. Ezt követően a parancsfájl hozzárendeli a **Microsoft.Sql** típusú végpont az alhálózathoz. Végül a parancsfájl az alhálózat hozzáadása az SQL Database-kiszolgálóhoz, így a szabályt hoz létre hozzáférési szabálygyűjtemény (ACL).

#### <a name="powershell-script-3-source-code"></a>PowerShell parancsfájl 3 forráskód

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Netowrk-Rule".';
```






<a name="a-script-40" />

## <a name="script-4-clean-up"></a>4. parancsfájl: Karbantartás

A végső parancsfájl törli az erőforrásokat, amelyek a korábbi parancsfájlok a bemutató készült. Azonban a parancsprogram kéri a jóváhagyás előtt törli a következő:

- Az Azure SQL Database-kiszolgálóhoz
- Azure-erőforráscsoportot

Parancsfájl 4 1 parancsfájl befejezése után bármikor futtathatja.

#### <a name="powershell-script-4-source-code"></a>PowerShell parancsfájl 4 forráskód

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzureRmVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzureRmVirtualNetwork `
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
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";
    
    Remove-AzureRmSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;
    
    Write-Host "Remove the Azure Resource Group.";
    
    Remove-AzureRmResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```






<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>1-4 parancsfájlok tényleges kimenete

A teszt futtatásakor kimenete egy rövidített formátumban a következő jelenik meg. A kimeneti hasznosak lehetnek, abban az esetben, ha nem szeretné, hogy ténylegesen most futtatni a PowerShell-parancsfájlok.

```
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Completed script 2, the "Prerequisites".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations      
----------------- -------       ---------      
Succeeded         Microsoft.Sql {westcentralus}
                                               
Verify that the rule is in the SQL DB ACL.
                                               
Completed script 3, the "Virtual-Network-Rule".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

Ez az a fő PowerShell-parancsfájl végén.





<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Ellenőrizze az alhálózat egy olyan végpont

Előfordulhat, hogy olyan alhálózatot, amely már hozzá van rendelve a **Microsoft.Sql** típusnév, ami azt jelenti, mert már egy virtuális végpontot. Használhatja a [Azure-portálon] [ http-azure-portal-link-ref-477t] a végpontot a virtuális hálózati szabály létrehozásához.

Vagy előfordulhat, nem tudja, hogy hogy rendelkezik-e az alhálózat a **Microsoft.Sql** típusnév. A műveletek a következő PowerShell-parancsfájl futtatásával is:

1. Annak megállapítása, hogy rendelkezik-e az alhálózat a **Microsoft.Sql** típusnév.
2. Szükség esetén rendelje hozzá a neve, ha nincs megadva.
    - A parancsprogram kéri, hogy *megerősítése*, mielőtt vonatkozik, a távol típus neve.

#### <a name="phases-of-the-script"></a>A parancsfájl fázisai

A PowerShell parancsfájl fázisai a következők:

1. JELENTKEZZEN be az Azure-fiókjával, PS munkamenetenként csak egyszer szükséges.  Rendelje hozzá a változókat.
2. Keresse meg a virtuális hálózat, majd az alhálózathoz.
3. Az alhálózat címkézett **Microsoft.Sql** kiszolgáló típusú végpont?
4. Írja be a nevet a virtuális szolgáltatásvégpont hozzáadása **Microsoft.Sql**, az alhálózaton.

> [!IMPORTANT]
> Ez a parancsfájl futtatása előtt módosítania kell a $változók, a parancsfájl elején rendelt értékeket.

#### <a name="direct-powershell-source-code"></a>Közvetlen PowerShell forráskód

A PowerShell-parancsfájl nem minden olyan összetevő frissítésére, kivéve, ha az Igen válasz, ha a rendszer jóváhagyást kér. A parancsfájl adhat hozzá a következő típusnév **Microsoft.Sql** az alhálózathoz. De a parancsfájl megpróbál hozzáadása csak akkor, ha az alhálózat nem rendelkezik a következő típusnév.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Login-AzureRmAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

#### <a name="actual-output"></a>Tényleges kimeneti

A következő kódblokk a tényleges visszajelzés (csak formai jellegű módosításokat) jeleníti meg.

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```




<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

