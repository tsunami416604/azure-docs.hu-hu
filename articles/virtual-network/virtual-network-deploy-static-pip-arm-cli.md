---
title: Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása - Azure CLI | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egy statikus nyilvános IP-címmel rendelkező virtuális gép az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 4b8f91d7d9fc414ed0ae6387c25f71b1601e0b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043408"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása az Azure CLI használatával

Létrehozhat egy statikus nyilvános IP-címmel rendelkező virtuális gépet. A nyilvános IP-cím lehetővé teszi, hogy az internetről kommunikáljon egy virtuális géppel. Rendeljen statikus nyilvános IP-címet dinamikus cím helyett, hogy a cím soha ne változik. További információ a [statikus nyilvános IP-címekről.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Ha egy meglévő virtuális géphez rendelt nyilvános IP-címet dinamikusról statikusra szeretne módosítani, vagy magánhálózati IP-címekkel szeretne dolgozni, olvassa el az [IP-címek hozzáadása, módosítása vagy eltávolítása című témakört.](virtual-network-network-interface-addresses.md) A nyilvános IP-címek [névleges díja](https://azure.microsoft.com/pricing/details/ip-addresses)van, és az előfizetésenként használható nyilvános IP-címek száma [korlátozva](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A következő lépéseket a helyi számítógépről vagy az Azure Cloud Shell használatával hajthatja végre. A helyi számítógép használatához győződjön meg arról, hogy az [Azure CLI telepítve](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)van. Az Azure Cloud Shell használatához válassza a **Try It (Kipróbálás a** jobb felső sarokban) lehetőséget az alábbi parancsmező jobb felső sarkában. A Cloud Shell bejelentkezik az Azure-ba.

1. Ha a Cloud Shellt használja, ugorjon a 2. Nyisson meg egy parancsmunkamenetet, és jelentkezzen be az Azure-ba a segítségével. `az login`
2. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példa létrehoz egy erőforráscsoportot az USA keleti részén az Azure régióban:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. A `--public-ip-address-allocation=static` beállítás statikus nyilvános IP-címet rendel a virtuális géphez. A következő példa létrehoz egy Ubuntu virtuális gépet egy statikus, alapvető SKU nyilvános IP-címmel, *amelyet myPublicIpAddress-nek*neveznek:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Ha a nyilvános IP-címnek szabványos `--public-ip-sku Standard` termékváltozatnak kell lennie, adja hozzá az előző parancshoz. További információ a [nyilvános IP-cím-skus-okról.](virtual-network-ip-addresses-overview-arm.md#sku) Ha a virtuális gép hozzá lesz adva egy nyilvános Azure Load Balancer háttérkészletéhez, a virtuális gép nyilvános IP-címének termékváltozatának meg kell egyeznie a terheléselosztó nyilvános IP-címének termékváltozatával. További részletek az [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

4. Tekintse meg a hozzárendelt nyilvános IP-címet, és erősítse meg, hogy statikus, alapvető Termékváltozat-címként jött létre, [az hálózati nyilvános ip show-val:](/cli/azure/network/public-ip#az-network-public-ip-show)

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Az Azure nyilvános IP-címet rendelt a virtuális gépet létrehozó régióban használt címekről. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

> [!WARNING]
> Ne módosítsa az IP-cím beállításait a virtuális gép operációs rendszerén belül. Az operációs rendszer nem ismeri az Azure nyilvános IP-címeit. Bár hozzáadhat privát IP-címbeállításokat az operációs rendszerhez, azt javasoljuk, hogy csak akkor tegye meg, ha szükséges, és csak [a Privát IP-cím hozzáadása az operációs rendszerhez](virtual-network-network-interface-addresses.md#private)című elolvasása után.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

- További információ az Azure [nyilvános IP-címeiről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- További információ a [nyilvános IP-cím összes beállításáról](virtual-network-public-ip-address.md#create-a-public-ip-address)
- További információ a [magánhálózati IP-címekről](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és statikus [privát IP-cím](virtual-network-network-interface-addresses.md#add-ip-addresses) hozzárendeléséről egy Azure virtuális géphez
- További információ a [Linux és](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek létrehozásáról
