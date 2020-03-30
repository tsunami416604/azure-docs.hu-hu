---
title: Alhálózati delegálás hozzáadása vagy eltávolítása Azure virtuális hálózatban
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan adhat hozzá vagy távolíthat el egy delegált alhálózatot egy azure-beli szolgáltatáshoz.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201866"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Alhálózati delegálás hozzáadása vagy eltávolítása

Az alhálózati delegálás explicit engedélyeket ad a szolgáltatásnak, hogy a szolgáltatás üzembe helyezésekor egyedi azonosító használatával hozzon létre szolgáltatásspecifikus erőforrásokat az alhálózatban. Ez a cikk ismerteti, hogyan adhat hozzá vagy távolíthat el egy Azure-szolgáltatás delegált alhálózatát.

## <a name="portal"></a>Portál

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban hozzon létre egy virtuális hálózatot és az alhálózatot, amelyet később egy Azure-szolgáltatásba delegál.

1. A képernyő bal felső részén válassza az **Erőforrás** > **létrehozása hálózati** > **virtuális hálózat**lehetőséget.
1. A **Virtuális hálózat létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be *a MyVirtualNetwork*. |
    | Címtér | Írja be *a 10.0.0.0/16*értéket . |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza **az Új létrehozása**lehetőséget, írja be a *myResourceGroup parancsot,* majd kattintson **az OK gombra.** |
    | Hely | Válassza **az EastUS**lehetőséget.|
    | Alhálózat - név | Adja meg *a mySubnet*. |
    | Alhálózat – címtartomány | Írja be *a 10.0.0.0/24 értéket*. |
    |||
1. Hagyja a többit alapértelmezettként, majd válassza a **Létrehozás gombot.**

### <a name="permissions"></a>Engedélyek

Ha nem ő hozza létre azt az alhálózatot, amelyet egy Azure-szolgáltatásnak delegálni szeretne, a következő engedélyre van szüksége: `Microsoft.Network/virtualNetworks/subnets/write`.

A beépített [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör is tartalmazza a szükséges engedélyeket.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Azure-szolgáltatáshoz

Ebben a szakaszban delegálja az előző szakaszban létrehozott alhálózatot egy Azure-szolgáltatásnak.

1. A portál keresősávján adja meg a *myVirtualNetwork*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork*lehetőséget.
3. Válassza **az Alhálózatok**lehetőséget a **BEÁLLÍTÁSOK csoportban,** majd a **mySubnet**lehetőséget.
4. A *mySubnet* lapon az **Alhálózati delegálási** lista listában válassza ki az **Alhálózat delegálása szolgáltatásra** (például **Microsoft.DBforPostgreSQL/serversv2)** csoportban felsorolt szolgáltatásokat.  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózati delegálás eltávolítása Azure-szolgáltatásból

1. A portál keresősávján adja meg a *myVirtualNetwork*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork*lehetőséget.
3. Válassza **az Alhálózatok**lehetőséget a **BEÁLLÍTÁSOK csoportban,** majd a **mySubnet**lehetőséget.
4. A *MySubnet (Alhálózati* **delegálási** lista) mySubnet lapján válassza a **Nincs** lehetőséget a **Delegált alhálózat szolgáltatásra**listában felsorolt szolgáltatások közül. 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy az Azure CLI-t helyileg telepíti és használja, ez a cikk az Azure CLI 2.0.28-as vagy újabb verzióját igényli. A telepített verzió megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a program `az --version` A telepítési vagy frissítési információkért tekintse meg az [Azure CLI telepítése](/cli/azure/install-azure-cli) című témakört.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy **myResourceGroup** nevű erőforráscsoportot az **eastus** helyen:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) paranccsal hozzon létre a **myResourceGroup** erőforráscsoportban egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Engedélyek

Ha nem ő hozza létre azt az alhálózatot, amelyet egy Azure-szolgáltatásnak delegálni szeretne, a következő engedélyre van szüksége: `Microsoft.Network/virtualNetworks/subnets/write`.

A beépített [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör is tartalmazza a szükséges engedélyeket.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Azure-szolgáltatáshoz

Ebben a szakaszban delegálja az előző szakaszban létrehozott alhálózatot egy Azure-szolgáltatásnak. 

A [hálózati virtuális hálózat alhálózati frissítésével](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) frissítse a **mySubnet** nevű alhálózatot egy Azure-szolgáltatásra delegálással.  Ebben a példában a **Microsoft.DBforPostgreSQL/serversv2** a példadelegáláshoz használatos:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

A delegálás alkalmazásának ellenőrzéséhez használja [az az hálózati virtuális hálózat alhálózatának show-ját.](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show) Ellenőrizze, hogy a szolgáltatás delegált az alhálózat alatt a tulajdonság **szolgáltatásNév**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózati delegálás eltávolítása Azure-szolgáltatásból

A [hálózati virtuálishálózat alhálózati frissítésének](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) használatával távolítsa el a delegálást a **mySubnet**nevű alhálózatból:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
A delegálás eltávolításának ellenőrzéséhez használja [az az hálózati virtuális hálózat alhálózatát.](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show) Ellenőrizze, hogy a szolgáltatás eltávolításra került-e az alhálózatból a **tulajdonságszolgáltatás alattName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
A kimenet a parancsból null zárójel:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group)segítségével. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy **myVnet** nevű virtuális hálózatot **egy mySubnet** nevű alhálózattal a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) használatával a **myResourceGroup** ban a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)használatával. A virtuális hálózat IP-címterülete **10.0.0.0/16**. A virtuális hálózaton belüli alhálózat **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Engedélyek

Ha nem ő hozza létre azt az alhálózatot, amelyet egy Azure-szolgáltatásnak delegálni szeretne, a következő engedélyre van szüksége: `Microsoft.Network/virtualNetworks/subnets/write`.

A beépített [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör is tartalmazza a szükséges engedélyeket.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Azure-szolgáltatáshoz

Ebben a szakaszban delegálja az előző szakaszban létrehozott alhálózatot egy Azure-szolgáltatásnak. 

[Az Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) segítségével frissítse a **mySubnet** nevű alhálózatot egy **azure-szolgáltatásra delegált delegálás** sal.  Ebben a példában a **Microsoft.DBforPostgreSQL/serversv2** a példadelegáláshoz használatos:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
A delegálás ellenőrzéséhez használja a [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) t:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózati delegálás eltávolítása Azure-szolgáltatásból

Az [Remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) segítségével távolítsa el a delegálást a **mySubnet**nevű alhálózatból:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
A [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) segítségével ellenőrizze a delegálás eltávolítását:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [kezelheti az alhálózatokat az Azure-ban.](virtual-network-manage-subnet.md)
