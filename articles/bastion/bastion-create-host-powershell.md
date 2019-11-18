---
title: Megerősített gazdagép létrehozása az Azure PowerShell használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Bastion-gazdagépet
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 6cae6d258da2ddf0c3bfaade65ae74f1201b67b7
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121073"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure-beli megerősített gazdagép létrehozása Azure PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Bastion-gazdagépet. Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózaton, a zökkenőmentes RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. Az üzembe helyezés virtuális hálózatonként értelmezendő, nem pedig előfizetések/fiókok vagy virtuális gépek alapján.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="createhost"></a>Megerősített gazdagép létrehozása

Ez a szakasz segítséget nyújt egy új Azure-beli megerősített erőforrás létrehozásában Azure PowerShell használatával.

1. Hozzon létre egy virtuális hálózatot és egy Azure Bastion-alhálózatot. Létre kell hoznia az Azure Bastion-alhálózatot a **AzureBastionSubnet**név értékkel. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Ez nem más, mint az átjáró alhálózata. Legalább egy/27 vagy nagyobb alhálózat (/27,/26 stb.) alhálózatot kell használnia. Hozza létre a **AzureBastionSubnet** útválasztási táblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ a **AzureBastionSubnet**, olvassa el a következőt: a [NSG](bastion-nsg.md)használata.

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName " myBastionRG " -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Hozzon létre egy nyilvános IP-címet az Azure Bastion számára. A nyilvános IP-cím a nyilvános IP-cím, amely az RDP/SSH elérésére használt megerősített erőforrás (az 443-as porton keresztül). A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Hozzon létre egy új Azure Bastion-erőforrást a virtuális hálózat AzureBastionSubnet. A megerősített erőforrás létrehozásához és üzembe helyezéséhez 5 perc szükséges.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName " myBastionRG " -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>További lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.
