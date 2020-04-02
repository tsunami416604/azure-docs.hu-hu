---
title: Bástyaállomás létrehozása az Azure Powershell használatával | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure-bastion host
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520500"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Hozzon létre egy Azure-bastion host az Azure PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-bastion host PowerShell használatával. Miután kiépítette az Azure Bastion szolgáltatást a virtuális hálózatban, a zökkenőmentes RDP/SSH élmény az azonos virtuális hálózat összes virtuális gépe számára elérhető. Az Azure Bastion központi telepítése virtuális hálózatonként, nem előfizetésenként/fiókonként vagy virtuális gépenként.

Szükség esetén létrehozhat egy Azure-bastion host az [Azure Portal](bastion-create-host-portal.md)használatával.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion-gazdagép létrehozása

Ez a szakasz segít egy új Azure-bastion erőforrás létrehozásában az Azure PowerShell használatával.

1. Hozzon létre egy virtuális hálózatot és egy Azure-bástya-alhálózatot. Létre kell hoznia az Azure Bastion alhálózatot az **AzureBastionSubnet**névérték használatával. Ez az érték lehetővé teszi az Azure számára, hogy melyik alhálózatra telepítse a Bástya-erőforrásokat. Ez eltér az átjáró alhálózatátótól. Legalább /27 vagy nagyobb alhálózat (/27, /26 és így tovább) alhálózatot kell használnia. Hozza létre az **AzureBastionSubnet-et** útvonaltáblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ az **AzureBastionSubnet,** tekintse meg az [NSG-k használata](bastion-nsg.md) cikket.

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Hozzon létre egy nyilvános IP-címet az Azure Bastion számára. A nyilvános IP-cím az a nyilvános IP-cím, amelyen az RDP/SSH elérhető lesz (a 443-as porton keresztül). A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozott megerősített erőforrásnak.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Hozzon létre egy új Azure-bastion erőforrást a virtuális hálózat AzureBastionSubnet hálózatában. Körülbelül 5 percet vesz igénybe a Bástya erőforrás létrehozása és üzembe helyezése.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>További lépések

* További információkért olvassa el a [Bástya GYIK-et.](bastion-faq.md)

* Ha hálózati biztonsági csoportokat szeretne használni az Azure Bastion alhálózattal, olvassa el a [Munka az NSG-kkel című témakört.](bastion-nsg.md)
