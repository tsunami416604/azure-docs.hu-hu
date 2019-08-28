---
title: Portok megnyitása virtuális géphez Azure PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan nyithat meg egy portot/hozzon létre egy végpontot a Windows rendszerű virtuális géphez az Azure Resource Manager-alapú üzembe helyezési móddal és Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: cd5aab6934e2f9692411e09046722cd59ad5e6a8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089106"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Portok és végpontok megnyitása az Azure-beli virtuális gépeken a PowerShell használatával
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Gyors parancsok
A hálózati biztonsági csoport és az ACL-szabályok létrehozásához [a Azure PowerShell telepítésének legújabb verziójára](/powershell/azureps-cmdlets-docs)van szükség. [Ezeket a lépéseket a Azure Portal használatával](nsg-quickstart-portal.md)is végrehajthatja.

Jelentkezzen be az Azure-fiókjába:

```powershell
Connect-AzAccount
```

Az alábbi példákban cserélje le a paraméterek nevét a saját értékeire. Példa a paraméterek neveire: *myResourceGroup*, *myNetworkSecurityGroup*és *myVnet*.

Hozzon létre egy szabályt a [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). A következő példa egy *myNetworkSecurityGroupRule* nevű szabályt hoz létre a *80*-es porton a *TCP* -forgalom engedélyezéséhez:

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

Ezután hozza létre a hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) , és rendelje hozzá az IMÉNT létrehozott http-szabályt az alábbiak szerint. A következő példa egy *myNetworkSecurityGroup*nevű hálózati biztonsági csoportot hoz létre:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Most rendeljen hálózati biztonsági csoportot egy alhálózathoz. Az alábbi példa egy *myVnet* nevű meglévő virtuális hálózatot rendel hozzá a *$vnet* változóhoz a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)használatával:

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Társítsa a hálózati biztonsági csoportot az alhálózatához a [set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). A következő példa a *mySubnet* nevű alhálózatot a hálózati biztonsági csoporttal társítja:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Végül frissítse a virtuális hálózatot a [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) , hogy a módosítások életbe lépnek:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportokról
Az itt található gyors parancsok lehetővé teszik a virtuális gép felé irányuló forgalom működésének megkezdését. A hálózati biztonsági csoportok számos nagyszerű szolgáltatást és részletességet biztosítanak az erőforrásokhoz való hozzáférés szabályozásához. [A hálózati biztonsági csoport és az ACL-szabályok létrehozásával kapcsolatban itt](tutorial-virtual-network.md#secure-network-traffic)talál további információt.

A magasan elérhető webalkalmazások esetében a virtuális gépeket egy Azure Load Balancer mögé kell helyeznie. A terheléselosztó elosztja a forgalmat a virtuális gépekre egy olyan hálózati biztonsági csoporttal, amely forgalmi szűrést biztosít. További információkért lásd: a [Linux rendszerű virtuális gépek terheléselosztása az Azure-ban egy olyan, magasan elérhető alkalmazás létrehozásához](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a példában egy egyszerű szabályt hozott létre a HTTP-forgalom engedélyezéséhez. A részletesebb környezetek létrehozásáról a következő cikkekben talál információt:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az a hálózati biztonsági csoport?](../../virtual-network/security-overview.md)
* [A terheléselosztó Azure Resource Manager áttekintése](../../load-balancer/load-balancer-arm.md)

