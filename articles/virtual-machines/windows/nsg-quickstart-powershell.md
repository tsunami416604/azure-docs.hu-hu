---
title: Portok megnyitása virtuális géphez az Azure PowerShell használatával
description: Megtudhatja, hogy miként nyithat meg egy portot/ hozhat létre végpontot a Windows virtuális géphez az Azure erőforrás-kezelő telepítési módés az Azure PowerShell használatával
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a0dcc53d84edb4dd697213106c02626df24acfd8
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869400"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Portok és végpontok megnyitása virtuális géphez az Azure-ban a PowerShell használatával
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Gyors parancsok
Hálózati biztonsági csoport- és ACL-szabályok létrehozásához [az Azure PowerShell legújabb verziójának telepítve](/powershell/azureps-cmdlets-docs)kell lennie. [Ezeket a lépéseket az Azure Portal használatával](nsg-quickstart-portal.md)is végrehajthatja.

Jelentkezzen be az Azure-fiókjába:

```powershell
Connect-AzAccount
```

A következő példákban cserélje le a paraméterneveket a saját értékeire. Példa paraméternevek közé tartozott *a myResourceGroup*, *a myNetworkSecurityGroup*és *a myVnet*.

Hozzon létre egy szabályt a [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével. A következő példa létrehoz egy *myNetworkSecurityGroupRule* nevű szabályt, amely engedélyezi a *tcp-forgalmat* a *80-as*porton:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Ezután hozza létre a hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup segítségével,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) és rendelje hozzá az imént létrehozott HTTP-szabályt az alábbiak szerint. A következő példa létrehoz egy *myNetworkSecurityGroup*nevű hálózati biztonsági csoportot:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Most rendelje hozzá a hálózati biztonsági csoportot egy alhálózathoz. A következő példa egy *myVnet* nevű meglévő virtuális hálózatot rendel a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) *$vnet* változóhoz:

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Társítsa a hálózati biztonsági csoportot az alhálózathoz a [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). A következő példa a *mySubnet* nevű alhálózatot társítja a hálózati biztonsági csoporthoz:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Végül frissítse a virtuális hálózatot a [Set-AzVirtualNetwork-tel](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) a módosítások érvénybe léptetéséhez:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportokról
A gyors parancsok itt lehetővé teszi, hogy a virtuális gépre áramló forgalommal való üzembe futást. A hálózati biztonsági csoportok számos nagyszerű szolgáltatást és részletességet biztosítanak az erőforrásokhoz való hozzáférés szabályozásához. A hálózati biztonsági csoport és az [ACL-szabályok létrehozásáról itt olvashat bővebben.](tutorial-virtual-network.md#secure-network-traffic)

A magas rendelkezésre állású webes alkalmazások esetén a virtuális gépeket egy Azure Load Balancer mögé kell helyeznie. A terheléselosztó forgalmat oszt ki a virtuális gépek, a hálózati biztonsági csoport, amely a forgalom szűrése. További információ: [Linux-alapú virtuális gépek terheléselosztása az Azure-ban egy magas rendelkezésre állású alkalmazás létrehozásához.](tutorial-load-balancer.md)

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű szabályt, amely engedélyezi a HTTP-forgalmat. A részletesebb környezetek létrehozásáról az alábbi cikkekben talál információt:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)
* [Mi az a hálózati biztonsági csoport?](../../virtual-network/security-overview.md)
* [Az Azure terheléselosztó áttekintése](../../load-balancer/load-balancer-overview.md)

