---
title: Image z galerie megosztása a bérlők számára az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan oszthat meg Virtuálisgép-rendszerképek használatával megosztott kép katalógusok Azure bérlők között.
services: virtual-machine-scale-sets
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbaaac629fd013602eed75cc7dc357f13a62e3b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160125"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Az Azure-bérlőre kiterjedő katalógus Virtuálisgép-rendszerképek megosztása

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Hozzon létre egy méretezési csoportot az Azure CLI használatával

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

A méretezési csoport létrehozása. Cserélje le a példában az adatokat a saját.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>További lépések

Ha problémákat tapasztal, [hibáinak elhárítása a közös kép katalógusok](troubleshooting-shared-images.md).