---
title: 'Oktatóanyag: Felügyelt példány hozzáadása feladatátvételi csoporthoz'
description: Ismerje meg, hogyan konfigurálhat egy feladatátvételi csoportot az Azure SQL Database felügyelt példányához.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462649"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Oktatóanyag: SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz

SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz. Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> - Elsődleges felügyelt példány létrehozása
> - Másodlagos felügyelt példány létrehozása [feladatátvételi csoport](sql-database-auto-failover-group.md)részeként. 
> - Feladatátvétel tesztelése

  > [!NOTE]
  > - Az oktatóanyag során győződjön meg arról, hogy konfigurálja az erőforrásokat a [felügyelt példány feladatátvételi csoportjaibeállításának előfeltételeivel.](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets) 
  > - Felügyelt példány létrehozása jelentős időt vehet igénybe. Ennek eredményeképpen ez az oktatóanyag több órát is igénybe vehet. A kiépítési időkről további információt a [felügyelt példánykezelési műveletek című témakörben talál.](sql-database-managed-instance.md#managed-instance-management-operations) 
  > - Feladatátvevő csoportban részt vevő felügyelt példányok [expressroute-hoz](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) vagy két csatlakoztatott VPN-átjáróhoz van szükség. Ez az oktatóanyag a VPN-átjárók létrehozásának és csatlakoztatásának lépéseit ismerteti. Hagyja ki ezeket a lépéseket, ha már konfigurálta az ExpressRoute-ot. 


## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. [Hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) ha még nem rendelkezik ilyen.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Az oktatóanyag befejezéséhez győződjön meg arról, hogy a következő elemekkel rendelkezik:

- Azure-előfizetés. [Hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) ha még nem rendelkezik ilyen.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Erőforráscsoport és elsődleges felügyelt példány létrehozása
Ebben a lépésben hozza létre az erőforráscsoportot és az elsődleges felügyelt példányt a feladatátvételi csoport hoz létre az Azure Portalon vagy a PowerShell használatával. 


# <a name="portal"></a>[Portál](#tab/azure-portal) 

Hozza létre az erőforráscsoportot és az elsődleges felügyelt példányt az Azure Portalhasználatával. 

1. Válassza az **Azure SQL** az Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza a **+ Add lehetőséget** az SQL kiválasztása telepítési **lehetőség** lap megnyitásához. A különböző adatbázisokra vonatkozó további információkat az Adatbázisok csempén a Részletek megjelenítése lehetőséget választva tekintheti meg.
1. Válassza a **Létrehozás gombot** az **SQL felügyelt példányok** csempéjén. 

    ![Felügyelt példány kiválasztása](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Az **Azure SQL Database felügyelt példányának létrehozása** lapon az **Alapok lap**
    1. A **Projekt részletei**csoportban válassza ki az **Előfizetést** a legördülő menüből, majd válassza az Új erőforráscsoport **létrehozása lehetőséget.** Írja be az erőforráscsoport nevét, `myResourceGroup`például . 
    1. A **Felügyelt példány részletei csoportban**adja meg a felügyelt példány nevét, és azt a régiót, ahol telepíteni szeretné a felügyelt példányt. Hagyja a **Compute + storage-ot** az alapértelmezett értékeken. 
    1. A **Rendszergazdai fiók csoportban**adjon `azureuser`meg egy rendszergazdai bejelentkezést, például a , és egy összetett rendszergazdai jelszót. 

    ![Elsődleges hibajelző létrehozása](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Hagyja a többi beállítást az alapértelmezett értékeken, és válassza a **Véleményezés + létrehozás** lehetőséget a felügyelt példány beállításainak áttekintéséhez. 
1. Válassza a **Létrehozás** lehetőséget az elsődleges felügyelt példány létrehozásához. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre az erőforráscsoportot és az elsődleges felügyelt példányt a PowerShell használatával. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációhozzáadása egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Alhálózat leése egy virtuális hálózatban. | 
| [Új-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. | 
| [Új-Azroutetable](/powershell/module/az.network/new-azroutetable) | Útvonaltábla létrehozása. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Frissíti a virtuális hálózat alhálózati konfigurációját.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bejuttat egy hálózati biztonsági csoportot. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot frissít.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonal hozzáadása az útvonaltáblához. |
| [Set-Azroutetable](/powershell/module/az.network/set-azroutetable) | Útvonaltábla frissítése.  |
| [Új-Azsqlinstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy Azure SQL Database felügyelt példányt.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Másodlagos virtuális hálózat létrehozása
Ha az Azure Portalon a felügyelt példány létrehozásához, akkor külön kell létrehoznia a virtuális hálózatot, mert követelmény, hogy az elsődleges és másodlagos felügyelt példány alhálózata nem rendelkezik átfedési tartományokkal. Ha a PowerShell használatával konfigurálja a felügyelt példányt, ugorjon előre a 3. 

# <a name="portal"></a>[Portál](#tab/azure-portal) 
Az elsődleges virtuális hálózat alhálózati tartományának ellenőrzéséhez kövesse az alábbi lépéseket:
1. Az [Azure Portalon](https://portal.azure.com)keresse meg az erőforráscsoportot, és válassza ki az elsődleges példány virtuális hálózatát. 
1. Válassza **az Alhálózatok lehetőséget** a Beállítások **csoportban,** és jegyezze fel a Cím **tartományt.** A másodlagos felügyelt példány virtuális hálózatának alhálózati címtartománya nem fedheti át ezt. 


   ![Elsődleges alhálózat](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Virtuális hálózat létrehozásához kövesse az alábbi lépéseket:

1. Az [Azure portalon](https://portal.azure.com)válassza az **Erőforrás létrehozása** és a *virtuális hálózat*keresése lehetőséget. 
1. Válassza a Microsoft által közzétett **Virtuális hálózat** beállítást, majd a következő lapon a **Létrehozás** lehetőséget. 
1. Töltse ki a szükséges mezőket a másodlagos felügyelt példány virtuális hálózatának konfigurálásához, majd válassza a **Létrehozás gombot.** 

   Az alábbi táblázat a másodlagos virtuális hálózathoz szükséges értékeket mutatja be:

    | **Mező** | Érték |
    | --- | --- |
    | **Név** |  A másodlagos felügyelt példány által használandó virtuális hálózat `vnet-sql-mi-secondary`neve, például . |
    | **Címtér** | A virtuális hálózat címterülete, `10.128.0.0/16`például . | 
    | **Előfizetés** | Az az előfizetés, ahol az elsődleges felügyelt példány és erőforráscsoport található. |
    | **Régió** | Az a hely, ahol a másodlagos felügyelt példányt üzembe helyezi. |
    | **Alhálózat** | Az alhálózat neve. `default`alapértelmezés szerint az Ön számára biztosított. |
    | **Címtartomány**| Az alhálózat címtartománya. Ennek különböznie kell az elsődleges felügyelt példány virtuális hálózata által használt `10.128.0.0/24`alhálózati címtartománytól, például .  |
    | &nbsp; | &nbsp; |

    ![Másodlagos virtuális hálózati értékek](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ez a lépés csak akkor szükséges, ha az Azure Portal használatával telepíti a felügyelt példányt. Ugrás előre a 3. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Másodlagos felügyelt példány létrehozása
Ebben a lépésben egy másodlagos felügyelt példányt hoz létre az Azure Portalon, amely a két felügyelt példány közötti hálózatépítést is konfigurálja. 

A második felügyelt példánynak:
- Legyen üres. 
- Az elsődleges felügyelt példánytól eltérő alhálózati és IP-tartományt kell rendelkeznie. 

# <a name="portal"></a>[Portál](#tab/azure-portal) 

Hozza létre a másodlagos felügyelt példányt az Azure Portalhasználatával. 

1. Válassza az **Azure SQL** az Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza a **+ Add lehetőséget** az SQL kiválasztása telepítési **lehetőség** lap megnyitásához. A különböző adatbázisokra vonatkozó további információkat az Adatbázisok csempén a Részletek megjelenítése lehetőséget választva tekintheti meg.
1. Válassza a **Létrehozás gombot** az **SQL felügyelt példányok** csempéjén. 

    ![Felügyelt példány kiválasztása](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Az **Alapok** lapon az **Azure SQL Database felügyelt példány létrehozása** lap, töltse ki a másodlagos felügyelt példány konfigurálásához szükséges mezőket. 

   Az alábbi táblázat a másodlagos felügyelt példányhoz szükséges értékeket mutatja be:
 
    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, ahol az elsődleges felügyelt példány van. |
    | **Erőforráscsoport**| Az erőforráscsoport, ahol az elsődleges felügyelt példány van. |
    | **Felügyelt példány neve** | Az új másodlagos felügyelt példány neve, például`sql-mi-secondary`  | 
    | **Régió**| A másodlagos felügyelt példány helye.  |
    | **Felügyelt példány rendszergazdai bejelentkezési neve** | Az új másodlagos felügyelt példányhoz használni kívánt bejelentkezés, például `azureuser`. |
    | **Jelszó** | Egy összetett jelszó, amelyet a rendszergazdai bejelentkezés az új másodlagos felügyelt példány.  |
    | &nbsp; | &nbsp; |

1. A **Hálózat** lap Virtuális **hálózat**csoportjában válassza ki a másodlagos felügyelt példányhoz létrehozott virtuális hálózatot a legördülő menüből.

   ![Másodlagos hibajelző-hálózat](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. A **További beállítások** lap **Georeplikáció**csoportjában válassza az **Igen** beállítást a _feladatátvétel másodlagos részeként_való használatához. Válassza ki az elsődleges felügyelt példányt a legördülő menüből. 
    1. Győződjön meg arról, hogy a rendezés és az időzóna megegyezik az elsődleges felügyelt példány. Az ebben az oktatóanyagban létrehozott `SQL_Latin1_General_CP1_CI_AS` elsődleges felügyelt `(UTC) Coordinated Universal Time` példány a rendezés és az időzóna alapértelmezett példányát használta. 

   ![Másodlagos hibajelző-hálózat](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Válassza **a Véleményezés + létrehozás** lehetőséget a másodlagos felügyelt példány beállításainak áttekintéséhez. 
1. Válassza a **Létrehozás** lehetőséget a másodlagos felügyelt példány létrehozásához. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre a másodlagos felügyelt példányt a PowerShell használatával. 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációhozzáadása egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Alhálózat leése egy virtuális hálózatban. | 
| [Új-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. | 
| [Új-Azroutetable](/powershell/module/az.network/new-azroutetable) | Útvonaltábla létrehozása. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Frissíti a virtuális hálózat alhálózati konfigurációját.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bejuttat egy hálózati biztonsági csoportot. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot frissít.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonal hozzáadása az útvonaltáblához. |
| [Set-Azroutetable](/powershell/module/az.network/set-azroutetable) | Útvonaltábla frissítése.  |
| [Új-Azsqlinstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy Azure SQL Database felügyelt példányt.  |

---

## <a name="4---create-primary-gateway"></a>4 - Elsődleges átjáró létrehozása 
A feladatátvételi csoportban való részvételhez két felügyelt példánynak kell lennie az ExpressRoute vagy a két felügyelt példány virtuális hálózatai között a hálózati kommunikáció engedélyezéséhez konfigurált átjárónak. Ha úgy dönt, hogy két VPN-átjáró csatlakoztatása helyett [az ExpressRoute-ot](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) állítja be, ugorjon előre [a 7.](#7---create-a-failover-group)  

Ez a cikk a két VPN-átjáró létrehozásának és csatlakoztatásának lépéseit ismerteti, de előreugorhat a feladatátvételi csoport létrehozásához, ha helyette konfigurálta az ExpressRoute-ot. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre az átjárót az elsődleges felügyelt példány virtuális hálózatához az Azure Portal használatával. 


1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az erőforráscsoportot, és válassza ki az elsődleges felügyelt példány **virtuális hálózati** erőforrását. 
1. Válassza **az Alhálózatok lehetőséget a** Beállítások **csoportban,** majd új **átjáró-alhálózat**hozzáadásához válassza az alhálózatok at. Hagyja meg az alapértelmezett értékeket. 

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Az alhálózati átjáró létrehozása után válassza az **Erőforrás létrehozása** lehetőséget `Virtual network gateway` a bal oldali navigációs ablakban, majd írja be a keresőmezőbe. Válassza ki a **Microsoft**által közzétett **virtuális hálózati átjáró** erőforrást. 

   ![Új virtuális hálózati átjáró létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Töltse ki a szükséges mezőket az elsődleges felügyelt példány átjárókonfigurálásához. 

   Az alábbi táblázat az elsődleges felügyelt példány átjárójához szükséges értékeket mutatja be:
 
    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, ahol az elsődleges felügyelt példány van. |
    | **Név** | A virtuális hálózati átjáró neve, `primary-mi-gateway`például . | 
    | **Régió** | Az a régió, ahol a másodlagos felügyelt példány van. |
    | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
    | **VPN-típus** | **Útvonalalapú kiválasztása** |
    | **Sku**| Hagyja békén `VpnGw1`a. |
    | **Helyen**| Az a hely, ahol az elsődleges felügyelt példány és az elsődleges virtuális hálózat található.   |
    | **Virtuális hálózat**| Válassza ki a `vnet-sql-mi-primary`2. |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím**| Adja meg az IP-cím `primary-gateway-IP`nevét, például . |
    | &nbsp; | &nbsp; |

1. Hagyja a többi értéket alapértelmezettként, majd válassza a **Véleményezés + létrehozás** lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Az új virtuális hálózati átjáró létrehozásához válassza a **Létrehozás** lehetőséget. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre az átjárót az elsődleges felügyelt példány virtuális hálózatához a PowerShell használatával. 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációhozzáadása egy virtuális hálózathoz. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Alhálózat leése egy virtuális hálózatban. |
| [Új-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | IP-konfiguráció létrehozása virtuális hálózati átjáróhoz |
| [Új-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtuális hálózati átjáró létrehozása |


---


## <a name="5---create-secondary-gateway"></a>5 - Másodlagos átjáró létrehozása 
Ebben a lépésben hozza létre az átjárót a másodlagos felügyelt példány virtuális hálózatához az Azure Portal használatával, 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Az Azure Portal használatával ismételje meg az előző szakaszban leírt lépéseket a virtuális hálózati alhálózat és a másodlagos felügyelt példány átjárójának létrehozásához. Töltse ki a másodlagos felügyelt példány átjárójának konfigurálásához szükséges mezőket. 

   Az alábbi táblázat a másodlagos felügyelt példány átjárójához szükséges értékeket mutatja be:

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, ahol a másodlagos felügyelt példány van. |
   | **Név** | A virtuális hálózati átjáró neve, `secondary-mi-gateway`például . | 
   | **Régió** | Az a régió, ahol a másodlagos felügyelt példány van. |
   | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
   | **VPN-típus** | **Útvonalalapú kiválasztása** |
   | **Sku**| Hagyja békén `VpnGw1`a. |
   | **Helyen**| Az a hely, ahol a másodlagos felügyelt példány és a másodlagos virtuális hálózat található.   |
   | **Virtuális hálózat**| Válassza ki a `vnet-sql-mi-secondary`2. |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím**| Adja meg az IP-cím `secondary-gateway-IP`nevét, például . |
   | &nbsp; | &nbsp; |

   ![Másodlagos átjáróbeállítások](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre az átjárót a másodlagos felügyelt példány virtuális hálózatához a PowerShell használatával. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációhozzáadása egy virtuális hálózathoz. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Alhálózat leése egy virtuális hálózatban. |
| [Új-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | IP-konfiguráció létrehozása virtuális hálózati átjáróhoz |
| [Új-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtuális hálózati átjáró létrehozása |

---


## <a name="6---connect-the-gateways"></a>6 - Az átjárók csatlakoztatása
Ebben a lépésben hozzon létre egy kétirányú kapcsolatot a két virtuális hálózat két átjárója között. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Csatlakoztassa a két átjárók az Azure Portalhasználatával. 


1. Válassza **az Erőforrás létrehozása** az Azure [Portalon](https://portal.azure.com)lehetőséget.
1. Írja `connection` be a keresőmezőbe, majd nyomja meg az Enter billentyűt a kereséshez, amely a Microsoft által közzétett **Connection** erőforráshoz vezet.
1. A kapcsolat létrehozásához válassza a **Létrehozás** gombot. 
1. Az **Alapok** lapon jelölje ki az alábbi értékeket, majd kattintson az **OK gombra.** 
    1. Válassza `VNet-to-VNet` ki a **Kapcsolat típushoz.** 
    1. Válassza ki előfizetését a legördülő listából. 
    1. Válassza ki a felügyelt példány erőforráscsoportját a legördülő menüben. 
    1. Válassza ki az elsődleges felügyelt példány helyét a legördülő menüből 
1. A **Beállítások** lapon jelölje ki vagy írja be a következő értékeket, majd kattintson az **OK gombra:**
    1. Válassza ki az **első virtuális hálózati átjáró** `Primary-Gateway`elsődleges hálózati átjáróját, például a.  
    1. Válassza ki a második virtuális hálózati átjáró `Secondary-Gateway`másodlagos hálózati **átjáróját,** például a. 
    1. Jelölje be a **Kétirányú kapcsolat létrehozása jelölőnégyzetet.** 
    1. Hagyja meg az alapértelmezett elsődleges kapcsolat nevet, vagy nevezze át az Ön által választott értékre. 
    1. Megosztott **kulcs (PSK)** biztosítása a kapcsolathoz, például `mi1m2psk`. 

   ![Átjárókapcsolat létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Az **Összegzés** lapon tekintse át a kétirányú kapcsolat beállításait, majd a kapcsolat létrehozásához kattintson az **OK gombra.** 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Csatlakoztassa a két átjárót a PowerShell használatával. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmast használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |

---


## <a name="7---create-a-failover-group"></a>7 – Feladatátvételi csoport létrehozása
Ebben a lépésben hozza létre a feladatátvételi csoportot, és mindkét felügyelt példányt hozzáadja. 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot az Azure Portalhasználatával. 


1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Jelölje ki az első szakaszban létrehozott elsődleges `sql-mi-primary`felügyelt példányt, például a . 
1. A **Beállítások csoportban**keresse meg **a Példány feladatátvételi csoportjait,** és válassza a **Csoport hozzáadása lehetőséget** a **Példány feladatátvételi csoport** lapjának megnyitásához. 

   ![Feladatátvevő csoport hozzáadása](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. A **Példány feladatátvételi csoport** lapján írja be a `failovergrouptutorial` feladatátvételi csoport nevét, `sql-mi-secondary` például válassza ki a másodlagos felügyelt példányt, például a legördülő menüből. A Feladatátvételi csoport létrehozásához válassza a **Létrehozás** lehetőséget. 

   ![Feladatátvételi csoport létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Miután a feladatátvételi csoport telepítése befejeződött, a **feladatátvételi csoport** lapra kerül vissza. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Hozza létre a feladatátvételi csoportot a PowerShell használatával. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmast használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új Azure SQL Database felügyelt példány feladatátvételi csoportot.  |


---


## <a name="8---test-failover"></a>8 - Feladatátvétel tesztje
Ebben a lépésben sikertelen lesz a feladatátvételi csoport a másodlagos kiszolgálóra, majd az Azure Portal használatával. 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Feladatátvétel tesztelése az Azure Portalhasználatával. 


1. Keresse meg a _másodlagos_ felügyelt példányaz [Azure Portalon,](https://portal.azure.com) és válassza **a példány feladatátvételi csoportok** a beállítások alatt. 
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos. 
1. Válassza **a Feladatátvétel** lehetőséget, majd a leválasztott TDS-munkamenetekkel kapcsolatos figyelmeztetésen válassza az **Igen** lehetőséget. 

   ![Feladatátvétel a feladatátvételi csoport felett](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Tekintse át, hogy melyik manged példány az elsődleges, és melyik példány a másodlagos. Ha a feladatátvétel sikeres volt, a két példánynak szerepkört kellett váltania. 

   ![A felügyelt példányok szerepkört váltottak a feladatátvétel után](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Lépjen az új _másodlagos_ felügyelt példányra, és válassza a **Feladatátvétel** ismét az elsődleges példány elsődleges szerepkörbe való visszakerüléséhez. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Feladatátvétel tesztelése a PowerShell használatával. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Feladatátvételi csoport visszaállítása vissza az elsődleges kiszolgálóra:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Felügyelt példány feladatátvételi csoportok leése vagy listázása.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Egy felügyelt példány feladatátvételi csoportjának feladatátvételi végrehajtása. | 

---



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforrások törlése először törli a felügyelt példányt, majd a virtuális fürtöt, majd a fennmaradó erőforrásokat, végül pedig az erőforráscsoportot. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Nyissa meg az erőforráscsoportot az [Azure Portalon.](https://portal.azure.com) 
1. Jelölje ki a felügyelt példány(oka)t, majd válassza a **Törlés**gombot. Írja `yes` be a szövegmezőbe az erőforrás törlésének megerősítéséhez, majd válassza a **Törlés**lehetőséget. Ez a folyamat eltarthat egy ideig a háttérben, és amíg ez nem történik meg, nem fogja tudni törölni a *virtuális fürt vagy* bármely más függő erőforrások. Figyelje a törlést a Tevékenység lapon, és ellenőrizze, hogy a felügyelt példány törölve lett-e. 
1. A felügyelt példány törlése után törölje a *virtuális fürtöt* úgy, hogy kijelöli azt az erőforráscsoportban, majd a Törlés parancsot **választja.** Írja `yes` be a szövegmezőbe az erőforrás törlésének megerősítéséhez, majd válassza a **Törlés**lehetőséget. 
1. Törölje a fennmaradó erőforrásokat. Írja `yes` be a szövegmezőbe az erőforrás törlésének megerősítéséhez, majd válassza a **Törlés**lehetőséget. 
1. Az erőforráscsoport törléséhez válassza az **Erőforráscsoport törlése**lehetőséget, `myResourceGroup`írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Az erőforráscsoportot kétszer kell eltávolítania. Ha először távolítja el az erőforráscsoportot, akkor eltávolítja a felügyelt `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`példányt és a virtuális fürtöket, de a hibaüzenet sikertelen lesz. Futtassa az Remove-AzResourceGroup parancsot még egyszer a fennmaradó erőforrások és az erőforráscsoport eltávolításához.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Az oktatóanyag ezen része a következő PowerShell-parancsmast használja:

| Parancs | Megjegyzések |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása. |

---

## <a name="full-script"></a>Teljes szkript

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációhozzáadása egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Alhálózat leése egy virtuális hálózatban. | 
| [Új-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. | 
| [Új-Azroutetable](/powershell/module/az.network/new-azroutetable) | Útvonaltábla létrehozása. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Frissíti a virtuális hálózat alhálózati konfigurációját.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bejuttat egy hálózati biztonsági csoportot. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot frissít.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonal hozzáadása az útvonaltáblához. |
| [Set-Azroutetable](/powershell/module/az.network/set-azroutetable) | Útvonaltábla frissítése.  |
| [Új-Azsqlinstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy Azure SQL Database felügyelt példányt.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Az Azure SQL felügyelt adatbázispéldányával kapcsolatos információkat adja vissza. |
| [Új-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | IP-konfiguráció létrehozása virtuális hálózati átjáróhoz |
| [Új-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtuális hálózati átjáró létrehozása |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új Azure SQL Database felügyelt példány feladatátvételi csoportot.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Felügyelt példány feladatátvételi csoportok leése vagy listázása.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Egy felügyelt példány feladatátvételi csoportjának feladatátvételi végrehajtása. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása. | 

# <a name="portal"></a>[Portál](#tab/azure-portal) 

Nincsenek elérhető parancsfájlok az Azure Portalon.

---

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban két felügyelt példány között konfigurált egy feladatátvételi csoportot. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Elsődleges felügyelt példány létrehozása
> - Másodlagos felügyelt példány létrehozása [feladatátvételi csoport](sql-database-auto-failover-group.md)részeként. 
> - Feladatátvétel tesztelése

Lépjen tovább a felügyelt példányhoz való csatlakozás és az adatbázis felügyelt példányra való visszaállításának következő rövid útmutatója: 

> [!div class="nextstepaction"]
> [Csatlakozás a felügyelt példányhoz](sql-database-managed-instance-configure-vm.md)
> [Adatbázis visszaállítása felügyelt példányra](sql-database-managed-instance-get-started-restore.md)


