---
title: Image z galerie megosztása a bérlők számára az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan oszthat meg Virtuálisgép-rendszerképek használatával megosztott kép katalógusok Azure bérlők között.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 1578ba840c6dca93feb68754863439811d7ef099
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158730"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Az Azure-bérlőre kiterjedő katalógus Virtuálisgép-rendszerképek megosztása

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-azure-cli"></a>Hozzon létre egy virtuális Gépet az Azure CLI használatával

Jelentkezzen be az egyszerű szolgáltatás bérlő 1 az appID, az Alkalmazáskulcs és az 1. bérlő azonosítója. Használhat `az account show --query "tenantId"` a bérlő beszerzése azonosítók szükség esetén.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Jelentkezzen be az egyszerű szolgáltatás bérlő 2 használata az appID, az Alkalmazáskulcs és a 2. bérlő azonosítója:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

A virtuális gép létrehozásához. Cserélje le a példában az adatokat a saját.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>További lépések

Ha problémákat tapasztal, [hibáinak elhárítása a közös kép katalógusok](troubleshooting-shared-images.md).