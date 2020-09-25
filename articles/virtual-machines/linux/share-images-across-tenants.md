---
title: Katalógusbeli rendszerképek megosztása a bérlők között
description: Ismerje meg, hogyan oszthatja meg a virtuálisgép-rendszerképeket az Azure-bérlők között a megosztott képtárak használatával Linux-példákkal.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: f6ffa23818a223ef1c0d46823955668ad96292d9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307211"
---
# <a name="share-gallery-vm-images-across-azure-tenants---linux-examples"></a>Katalógusbeli virtuálisgép-rendszerképek megosztása Azure-bérlők között – linuxos példák

A megosztott képtárak lehetővé teszik a képek megosztását a RBAC használatával. A RBAC segítségével megoszthatja a bérlőn belüli képeket, és akár a bérlőn kívüli személyeket is. További információ erről az egyszerű megosztási lehetőségről: a [gyűjtemény megosztása](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> A portálon nem lehet virtuális gépet üzembe helyezni egy másik Azure-bérlő lemezképéről. Ha a bérlők között megosztott rendszerképből szeretne virtuális gépet létrehozni, az Azure CLI-t vagy a [PowerShellt](../windows/share-images-across-tenants.md)kell használnia.

## <a name="create-a-vm-using-azure-cli"></a>Virtuális gép létrehozása az Azure CLI-vel

Jelentkezzen be az 1. bérlői szolgáltatásnév appID, az alkalmazás kulcsával és az 1. Bérlő AZONOSÍTÓjának használatával. `az account show --query "tenantId"`Ha szükséges, a segítségével kérheti le a bérlői azonosítókat.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Jelentkezzen be a 2. bérlői szolgáltatásnév appID, az alkalmazás kulcsával és a 2. Bérlő AZONOSÍTÓjának használatával:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Hozza létre a virtuális gépet. Cserélje le a példában szereplő információkat a saját adataira.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Következő lépések

Ha bármilyen problémába ütközik, a [megosztott képtárakat is elháríthatja](../troubleshooting-shared-images.md).
