---
title: Virtuális gép létrehozása egy statikus nyilvános IP-cím – Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális gép statikus nyilvános IP-címet az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 974c984cda2dd2198d09fb0dd004e640727b8c48
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982647"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Hozzon létre egy virtuális gépet egy statikus nyilvános IP-címet az Azure CLI használatával

Létrehozhat egy virtuális gépet egy statikus nyilvános IP-címmel. Nyilvános IP-cím lehetővé teszi, hogy egy virtuális géphez az internetről érkező kommunikációt. Rendeljen hozzá egy statikus nyilvános IP-címet, nem pedig a dinamikus címet, annak érdekében, hogy a cím soha nem módosul. Tudjon meg többet [statikus nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#allocation-method). Módosítsa a statikus, dinamikus egy meglévő virtuális géphez társított nyilvános IP-címet, vagy magánhálózati IP-címek használata esetén lásd: [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md). Nyilvános IP-címekre egy [névleges díj](https://azure.microsoft.com/pricing/details/ip-addresses), és van egy [korlát](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) előfizetésenként használható nyilvános IP-címek száma.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az alábbi lépéseket is elvégezheti, a helyi számítógépről, vagy az Azure Cloud Shell használatával. Szeretné használni a helyi számítógépen, ellenőrizze, hogy a [telepített Azure CLI-](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Válassza ki az Azure Cloud Shell használatához **Kipróbálom** bármely a következő parancs mező jobb felső sarkában található. A Cloud Shellt az Azure-bA jelentkezik.

1. Ha a Cloud Shellt használja, ugorjon a 2. Nyisson meg egy parancssori munkamenetet, és jelentkezzen az Azure-bA `az login`.
2. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy erőforráscsoportot az East US Azure-régióban:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. A `--public-ip-address-allocation=static` lehetőség egy statikus nyilvános IP-címet rendel a virtuális gépet. Az alábbi példában egy Ubuntu virtuális gépet hoz létre egy statikus, az alapszintű Termékváltozat nyilvános IP-címet *myPublicIpAddress*:

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

   Ha a nyilvános IP-címének kell lennie a standard Termékváltozat, vegye fel `--public-ip-sku Standard` az előző parancs. Tudjon meg többet [nyilvános IP-cím termékváltozatok](virtual-network-ip-addresses-overview-arm.md#sku). Ha a rendszer felveszi a virtuális gép egy nyilvános Azure Load Balancer háttérkészlethez, az a virtuális gép nyilvános IP-cím Termékváltozatának meg kell egyeznie a terheléselosztó nyilvános IP-cím-Termékváltozat. További információkért lásd: [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Megtekintheti a hozzárendelt nyilvános IP-címet, és győződjön meg arról, hogy készült-címként statikus, az alapszintű Termékváltozat, a [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure hozzárendelt nyilvános IP-címet a virtuális gépet hozott létre a régióban használt címek. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

> [!WARNING]
Ne módosítsa az IP-címbeállítások, a virtuális gép operációs rendszerén belül. Az operációs rendszer nem észleli az Azure nyilvános IP-címek. Bár a magánhálózati IP-cím beállításait az operációs rendszer is hozzáadhat, javasoljuk, hogy nem így, ha szükséges, és a csak olvasási után nem [magánhálózati IP-cím hozzáadása operációs rendszer](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) az Azure-ban
- További információ az összes [nyilvános IP-cím beállításai](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Tudjon meg többet [magánhálózati IP-címek](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és hozzárendelése egy [statikus magánhálózati IP-cím](virtual-network-network-interface-addresses.md#add-ip-addresses) Azure virtuális gépeken
- További információ a létrehozásával [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek
