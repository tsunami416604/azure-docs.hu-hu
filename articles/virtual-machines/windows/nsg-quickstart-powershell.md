---
title: Nyissa meg a portokat a virtuális gép Azure PowerShell használatával |} Microsoft Docs
description: Megtudhatja, hogyan nyisson meg egy portot / hozzon létre egy végpontot, a Windows virtuális gépre az Azure resource manager rendszerbe állítási mód és az Azure PowerShell használatával
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: a7564c19f8318d62260d03b92f8115c8f3fa887a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Portok és egy virtuális géphez végpontok nyitni az Azure PowerShell használatával
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Gyors parancsok
Hozzon létre egy hálózati biztonsági csoport és a hozzáférés-vezérlési lista a szabályokat kell [telepített Azure PowerShell legújabb verziójának](/powershell/azureps-cmdlets-docs). Emellett [elvégzi ezeket a lépéseket az Azure portál használatával](nsg-quickstart-portal.md).

Jelentkezzen be az Azure-fiókjával:

```powershell
Connect-AzureRmAccount
```

A következő példákban cserélje le a saját értékeit paraméterek nevei. Példa paraméter nevekre *myResourceGroup*, *myNetworkSecurityGroup*, és *myVnet*.

Hozzon létre egy szabályt a [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Az alábbi példa létrehoz egy nevű szabályt *myNetworkSecurityGroupRule* engedélyezéséhez *tcp* porton forgalom *80*:

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

Ezután hozzon létre a hálózati biztonsági csoport [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) , és rendelje hozzá az újonnan létrehozott az alábbiak szerint a HTTP-szabály. Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Most tegyük a hálózati biztonsági csoport hozzárendelése egy alhálózatot. Az alábbi példa hozzárendel egy meglévő virtuális hálózatot nevű *myVnet* a változóhoz *$vnet* rendelkező [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

A hálózati biztonsági csoporthoz társítandó az alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig). Az alábbi példa társítja a nevű alhálózat *mySubnet* a hálózati biztonsági csoporthoz:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Végül frissítse a virtuális hálózaton található [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) ahhoz, hogy a módosítások életbe léptetéséhez:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportok
A gyors parancsok lehetővé teszik, amelyekből megismerheti a forgalom halad a virtuális Gépet. Hálózati biztonsági csoportok számos különleges szolgáltatásait és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További tudnivalók [itt szabályok létrehozása a hálózati biztonsági csoport és a hozzáférés-vezérlési lista](tutorial-virtual-network.md#secure-network-traffic).

Magas rendelkezésre állású webes alkalmazásokhoz helyezze a virtuális gépek az Azure terheléselosztó mögött. A load balancer osztja el a forgalmat a virtuális gépekhez, a hálózati biztonsági csoport, amely biztosítja a forgalomszűrést végez. További információkért lásd: [betöltése Linux virtuális gépek magas rendelkezésre állású alkalmazás létrehozása az Azure-ban egyenleg](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű szabályt, amely engedélyezi a HTTP-forgalmat. További részletes környezetek létrehozásáról a következő cikkekben találhat:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az, hogy a hálózati biztonsági csoport?](../../virtual-network/security-overview.md)
* [Terheléselosztók Azure Resource Manager áttekintése](../../load-balancer/load-balancer-arm.md)

