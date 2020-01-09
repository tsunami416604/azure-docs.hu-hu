---
title: Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása – PowerShell | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre statikus nyilvános IP-címmel rendelkező virtuális gépet a PowerShell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 6e278172bc1c3ec4e81860c3f6d6a4b644731e2c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647781"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Létrehozhat egy statikus nyilvános IP-címmel rendelkező virtuális gépet is. A nyilvános IP-cím lehetővé teszi, hogy az internetről kommunikáljon egy virtuális géppel. Statikus nyilvános IP-címet rendeljen hozzá, nem pedig dinamikus címet, hogy a cím ne legyen módosítva. További információ a [statikus nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#allocation-method). Ha egy meglévő virtuális géphez hozzárendelt nyilvános IP-címet szeretne módosítani dinamikusról statikusra, vagy magánhálózati IP-címekkel szeretne dolgozni, tekintse meg az [IP-címek hozzáadása, módosítása vagy eltávolítása](virtual-network-network-interface-addresses.md)című témakört. A nyilvános IP-címek [névleges díjszabással](https://azure.microsoft.com/pricing/details/ip-addresses)rendelkeznek, és az előfizetések által használható nyilvános IP-címek száma [korlátozva](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A következő lépéseket a helyi számítógépről vagy a Azure Cloud Shell használatával végezheti el. A helyi számítógép használatához győződjön meg arról, hogy telepítve van a [Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). A Azure Cloud Shell használatához válassza a **kipróbálás** elemet az alábbi parancsok bármelyikének jobb felső sarkában. A Cloud Shell aláírja az Azure-t.

1. Ha a Cloud Shell használja, ugorjon a 2. lépésre. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-ba `Connect-AzAccount`.
2. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az USA keleti régiója Azure-régióban:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Hozzon létre egy virtuális gépet a [New-AzVM](/powershell/module/az.Compute/New-azVM) paranccsal. Az `-AllocationMethod "Static"` lehetőség statikus nyilvános IP-címet rendel hozzá a virtuális géphez. Az alábbi példa egy *myPublicIpAddress*nevű, statikus, alapszintű SKU nyilvános IP-címmel rendelkező Windows Server rendszerű virtuális gépet hoz létre. Ha a rendszer kéri, adjon meg egy felhasználónevet és egy jelszót a virtuális gép bejelentkezési hitelesítő adataiként való használathoz:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Ha a nyilvános IP-címnek standard SKU-nak kell lennie, [létre kell hoznia egy nyilvános IP-címet](virtual-network-public-ip-address.md#create-a-public-ip-address), [létre kell hoznia egy hálózati adaptert](virtual-network-network-interface.md#create-a-network-interface), [hozzá kell rendelnie a nyilvános IP-címet a hálózati adapterhez](virtual-network-network-interface-addresses.md#add-ip-addresses), majd [létre kell hoznia egy virtuális gépet a hálózati adapterrel](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm), külön lépésben. További információ a [nyilvános IP-cím SKU-](virtual-network-ip-addresses-overview-arm.md#sku)ról. Ha a virtuális gépet egy nyilvános Azure Load Balancer háttér-készletéhez adja hozzá, akkor a virtuális gép nyilvános IP-címének SKU-jának meg kell egyeznie a terheléselosztó nyilvános IP-címének SKU-jának. Részletekért lásd: [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Tekintse meg a hozzárendelt nyilvános IP-címet, és győződjön meg róla, hogy statikus címként lett létrehozva a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Az Azure hozzárendelt egy nyilvános IP-címet a virtuális gépet a ben létrehozó régióban használt címekről. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

> [!WARNING]
> Ne módosítsa az IP-cím beállításait a virtuális gép operációs rendszerén belül. Az operációs rendszer nem ismeri az Azure nyilvános IP-címeit. Bár a magánhálózati IP-címek beállításait az operációs rendszerhez is hozzáadhatja, azt javasoljuk, hogy csak akkor hajtsa végre ezt, ha szükséges, és nem, amíg az olvasó nem [ad hozzá privát IP-címet az operációs rendszerhez](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

- További információ az Azure [-beli nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- További információ az összes [nyilvános IP-cím beállításairól](virtual-network-public-ip-address.md#create-a-public-ip-address)
- További információ a [magánhálózati IP-címekről](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és a [statikus magánhálózati IP-](virtual-network-network-interface-addresses.md#add-ip-addresses) címek egy Azure-beli virtuális géphez való hozzárendeléséről
- További információ a [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [Windows rendszerű](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek létrehozásáról
