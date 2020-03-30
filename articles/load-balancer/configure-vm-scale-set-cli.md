---
title: Virtuálisgép-méretezési készlet konfigurálása meglévő Azure Load Balancer -Azure CLI-vel
description: Ismerje meg, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer-rel.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349739"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Virtuálisgép-méretezési készlet konfigurálása egy meglévő Azure Load Balancer-rel az Azure CLI használatával

Ebben a cikkben megtudhatja, hogyan konfigurálhatja a virtuális gép méretezési készlet egy meglévő Azure Load Balancer. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Egy meglévő szabványos sku terheléselosztó az előfizetésben, ahol a virtuális gép méretezési csoport lesz telepítve.
- Egy Azure virtuális hálózat a virtuális gép méretezési készlet.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha úgy dönt, hogy a CLI helyileg használja, ez a cikk megköveteli, hogy az Azure CLI 2.0.28-as vagy újabb verziójának egy-egy verziója legyen telepítve. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Jelentkezzen be az Azure-ba.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Virtuálisgép-méretezési csoport üzembe helyezése meglévő terheléselosztóval

Cserélje le a zárójelben lévő értékeket a konfigurációban lévő erőforrások nevére.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Az alábbi példa egy virtuálisgép-méretezési készletet telepít a következőkkel:

- **MyVMSS** nevű virtuálisgép-méretezési készlet
- Az Azure Load Balancer nevű Azure load **balancer**
- **MyBackendPool** nevű terheléselosztó háttérkészlete
- Az Azure virtuális hálózat neve **myVnet**
- **MySubnet** nevű alhálózat
- **MyResourceGroup** nevű erőforráscsoport
- Ubuntu Server lemezkép a virtuális gép méretezési készletéhez

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> A méretezési készlet létrehozása után a háttérport nem módosítható a terheléselosztó állapotminta által használt terheléselosztási szabályhoz. A port módosításához távolítsa el az állapotminta az Azure virtuálisgép-méretezési készlet frissítésével, frissítse a portot, majd konfigurálja újra az állapotminta.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy virtuális gép méretezési készlet egy meglévő Azure Load Balancer.  A virtuálisgép-méretezési készletekről és a terheléselosztóról a következő témakörben olvashat bővebben:

- [Mi az az Azure Load Balancer?](load-balancer-overview.md)
- [Mik a virtuálisgép-méretezési csoportok?](../virtual-machine-scale-sets/overview.md)
                                