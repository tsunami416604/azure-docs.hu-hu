---
title: Galériaképek megosztása az Azure-beli bérlők között
description: Megtudhatja, hogyan oszthatja meg a virtuálisgép-lemezképeket az Azure-bérlők között a megosztott képgalériák használatával.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276343"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Galéria virtuálisgép-lemezképeinek megosztása az Azure-bérlők között

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Méretezési csoport létrehozása az Azure CLI-vel

Jelentkezzen be a szolgáltatásnév a bérlő 1 az appID, az alkalmazáskulcs és az 1-es bérlő azonosítójával. Szükség esetén `az account show --query "tenantId"` lejuthat a bérlői azonosítók leéséhez.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Jelentkezzen be a bérlő 2 szolgáltatásnévbe az appazonosító, az alkalmazáskulcs és a 2-es bérlő azonosítója használatával:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Hozza létre a méretezési készletet. Cserélje le a példában szereplő információkat a sajátjára.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>További lépések

Ha bármilyen problémába ütközik, [elháríthatja a megosztott képgalériákat.](troubleshooting-shared-images.md)
