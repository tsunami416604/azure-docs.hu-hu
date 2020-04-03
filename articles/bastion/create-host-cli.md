---
title: Bástyaállomás létrehozása az Azure CLI használatával | Az Azure-bástya
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és törölhet bástyaállomást
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: f98c965ad3b776f3688a716ba28b5367a00c9119
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619228"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Hozzon létre egy Azure Bastion host az Azure CLI használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Bastion host azure CLI használatával. Miután kiépítette az Azure Bastion szolgáltatást a virtuális hálózatban, a zökkenőmentes RDP/SSH élmény az azonos virtuális hálózat összes virtuális gépe számára elérhető. Az Azure Bastion központi telepítése virtuális hálózatonként, nem előfizetésenként/fiókonként vagy virtuális gépenként.

Szükség esetén létrehozhat egy Azure-bastion hostot az [Azure Portalon](bastion-create-host-portal.md), vagy az [Azure PowerShell](bastion-create-host-powershell.md)használatával.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion-gazdagép létrehozása

Ez a szakasz segít egy új Azure-bastion erőforrás létrehozásában az Azure CLI használatával.

1. Hozzon létre egy virtuális hálózatot és egy Azure-bástya-alhálózatot. Létre kell hoznia az Azure Bastion alhálózatot az **AzureBastionSubnet**névérték használatával. Ez az érték lehetővé teszi az Azure számára, hogy melyik alhálózatra telepítse a Bástya-erőforrásokat. Ez eltér az átjáró alhálózatátótól. Legalább /27 vagy nagyobb alhálózat (/27, /26 és így tovább) alhálózatot kell használnia. Hozza létre az **AzureBastionSubnet-et** útvonaltáblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ az **AzureBastionSubnet,** tekintse meg az [NSG-k használata](bastion-nsg.md) cikket.

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Hozzon létre egy nyilvános IP-címet az Azure Bastion számára. A nyilvános IP-cím az a nyilvános IP-cím, amelyen az RDP/SSH elérhető lesz (a 443-as porton keresztül). A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozott megerősített erőforrásnak.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Hozzon létre egy új Azure-bastion erőforrást a virtuális hálózat AzureBastionSubnet hálózatában. Körülbelül 5 percet vesz igénybe a Bástya erőforrás létrehozása és üzembe helyezése.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>További lépések

* További információkért olvassa el a [Bástya GYIK-et.](bastion-faq.md)

* Ha hálózati biztonsági csoportokat szeretne használni az Azure Bastion alhálózattal, olvassa el a [Munka az NSG-kkel című témakört.](bastion-nsg.md)
