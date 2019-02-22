---
title: Virtuális gép létrehozása egy statikus nyilvános IP-cím – PowerShell |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális Gépet PowerShell-lel statikus nyilvános IP-cím.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 929378f0eea66d7f2ad7846e50bab7ab4014c010
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652866"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Hozzon létre egy virtuális gépet PowerShell-lel statikus nyilvános IP-cím

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Létrehozhat egy virtuális gépet egy statikus nyilvános IP-címmel. Nyilvános IP-cím lehetővé teszi, hogy egy virtuális géphez az internetről érkező kommunikációt. Rendeljen hozzá egy statikus nyilvános IP-címet, nem pedig a dinamikus címet, annak érdekében, hogy a cím soha nem módosul. Tudjon meg többet [statikus nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#allocation-method). Módosítsa a statikus, dinamikus egy meglévő virtuális géphez társított nyilvános IP-címet, vagy magánhálózati IP-címek használata esetén lásd: [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md). Nyilvános IP-címekre egy [névleges díj](https://azure.microsoft.com/pricing/details/ip-addresses), és van egy [korlát](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) előfizetésenként használható nyilvános IP-címek száma.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az alábbi lépéseket is elvégezheti, a helyi számítógépről, vagy az Azure Cloud Shell használatával. Szeretné használni a helyi számítógépen, ellenőrizze, hogy a [telepített Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Válassza ki az Azure Cloud Shell használatához **Kipróbálom** bármely a következő parancs mező jobb felső sarkában található. A Cloud Shellt az Azure-bA jelentkezik.

1. Ha a Cloud Shellt használja, ugorjon a 2. Nyisson meg egy parancssori munkamenetet, és jelentkezzen az Azure-bA `Connect-AzAccount`.
2. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot. A következő példában létrehozunk egy erőforráscsoportot az East US Azure-régióban:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. A virtuális gép létrehozása a [New-azvm parancsmag](/powershell/module/az.Compute/New-azVM) parancsot. A `-AllocationMethod "Static"` lehetőség egy statikus nyilvános IP-címet rendel a virtuális gépet. Az alábbi példa egy Windows Server virtuális gépet hoz létre egy statikus, az alapszintű Termékváltozat nyilvános IP-címet *myPublicIpAddress*. Amikor a rendszer kéri, adja meg egy felhasználónevet és jelszót a bejelentkezési hitelesítő adatait, a virtuális gép szolgál:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Ha a nyilvános IP-címének kell lennie a standard Termékváltozat, hogy [hozzon létre egy nyilvános IP-cím](virtual-network-public-ip-address.md#create-a-public-ip-address), [hozzon létre egy hálózati adaptert](virtual-network-network-interface.md#create-a-network-interface), [nyilvános IP-cím hozzárendelése a hálózati adapter](virtual-network-network-interface-addresses.md#add-ip-addresses), majd [hozzon létre egy virtuális gépet a hálózati adapter](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm), a lépést. Tudjon meg többet [nyilvános IP-cím termékváltozatok](virtual-network-ip-addresses-overview-arm.md#sku). Ha a rendszer felveszi a virtuális gép egy nyilvános Azure Load Balancer háttérkészlethez, az a virtuális gép nyilvános IP-cím Termékváltozatának meg kell egyeznie a terheléselosztó nyilvános IP-cím-Termékváltozat. További információkért lásd: [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Hozzárendelt nyilvános IP-címének megtekintése, és győződjön meg arról, hogy létrehoztuk a statikus címként [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure hozzárendelt nyilvános IP-címet a virtuális gépet hozott létre a régióban használt címek. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

> [!WARNING]
Ne módosítsa az IP-címbeállítások, a virtuális gép operációs rendszerén belül. Az operációs rendszer nem észleli az Azure nyilvános IP-címek. Bár a magánhálózati IP-cím beállításait az operációs rendszer is hozzáadhat, javasoljuk, hogy nem így, ha szükséges, és a csak olvasási után nem [magánhálózati IP-cím hozzáadása operációs rendszer](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhat [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) az Azure-ban
- További információ az összes [nyilvános IP-cím beállításai](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Tudjon meg többet [magánhálózati IP-címek](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és hozzárendelése egy [statikus magánhálózati IP-cím](virtual-network-network-interface-addresses.md#add-ip-addresses) Azure virtuális gépeken
- További információ a létrehozásával [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek
