---
title: Katalógusbeli rendszerképek megosztása az Azure-beli bérlők között
description: Megtudhatja, hogyan oszthat meg virtuálisgép-lemezképeket az Azure-bérlők között megosztott képtárakkal.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 6560bf452f04ae5d88168b8d3fad300feb90b16f
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86220524"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Katalógusbeli virtuálisgép-rendszerképek megosztása Azure-bérlők között

A megosztott képtárak lehetővé teszik a képek megosztását a RBAC használatával. A RBAC segítségével megoszthatja a bérlőn belüli képeket, és akár a bérlőn kívüli személyeket is. További információ erről az egyszerű megosztási lehetőségről: a [gyűjtemény megosztása](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

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

## <a name="next-steps"></a>További lépések

Ha bármilyen problémába ütközik, a [megosztott képtárakat is elháríthatja](troubleshooting-shared-images.md).