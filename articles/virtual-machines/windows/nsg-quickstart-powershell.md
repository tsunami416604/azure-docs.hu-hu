---
title: Nyissa meg a portokat a virtuális gépek Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan nyisson meg egy portot / hozzon létre egy végpontot, a Windows virtuális gépre az Azure resource manager üzembe helyezési mód és az Azure PowerShell használatával
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: f79db8cdec0aa48ae300aff4c58072fb6afdc932
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932782"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Hogyan nyithat meg portokat és végpontokat egy virtuális géphez a PowerShell használatával
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Gyors parancsok
Hozzon létre egy hálózati biztonsági csoportok és ACL-szabályok kell [telepített Azure PowerShell legújabb verzióját](/powershell/azureps-cmdlets-docs). Emellett [ezeket a lépéseket az Azure portal használatával](nsg-quickstart-portal.md).

Jelentkezzen be az Azure-fiókjával:

```powershell
Connect-AzureRmAccount
```

A következő példákban cserélje le a saját értékeire paraméterek nevei. Példa paraméterneveket foglalt *myResourceGroup*, *myNetworkSecurityGroup*, és *myVnet*.

Hozzon létre egy szabályt az [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). A következő példában létrehozunk egy nevű szabályt *myNetworkSecurityGroupRule* engedélyezéséhez *tcp* port forgalmát *80-as*:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig `
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

Ezután hozza létre a hálózati biztonsági csoport [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) és rendelje hozzá az újonnan létrehozott módon HTTP szabály. A következő példában létrehozunk egy hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Most tegyük a hálózati biztonsági csoport hozzárendelése egy alhálózathoz. Az alábbi példa hozzárendeli nevű meglévő virtuális hálózat *myVnet* változóként *$vnet* a [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Az alhálózatot, amelyen a hálózati biztonsági csoport társítása [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig). Az alábbi példa hozzárendeli a nevű alhálózat *mySubnet* a hálózati biztonsági csoporttal:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Végül frissítse a virtuális hálózaton [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) ahhoz, hogy a módosítások érvénybe léptetéséhez:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportok
A gyors parancsok lehetővé teszik elsajátíthatja a használatát az adatforgalom a virtuális géphez. Hálózati biztonsági csoportok számos nagyszerű funkciókkal és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További információ [létrehozása egy hálózati biztonsági csoportok és ACL-szabályok itt](tutorial-virtual-network.md#secure-network-traffic).

A magas rendelkezésre állású webes alkalmazásokhoz helyezze a virtuális gépek az Azure Load Balancer mögött. A terheléselosztó elosztja a forgalmat, amely biztosítja a forgalom szűrése hálózati biztonsági csoport rendelkező virtuális gépek között. További információkért lásd: [betöltés Linux rendszerű virtuális gépek terheléselosztása az Azure-ban magas rendelkezésre állású alkalmazások létrehozásához](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű HTTP-forgalmat engedélyező szabállyal. Részletesebb környezetek létrehozásáról a következő cikkekben talál:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az a hálózati biztonsági csoport?](../../virtual-network/security-overview.md)
* [A Terheléselosztók Azure Resource Manager áttekintése](../../load-balancer/load-balancer-arm.md)

