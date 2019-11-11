---
title: Katalógusbeli rendszerképek megosztása az Azure-beli bérlők között | Microsoft Docs
description: Megtudhatja, hogyan oszthat meg virtuálisgép-lemezképeket az Azure-bérlők között megosztott képtárakkal.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: b63bc1089b113edaac637df0a7e55c39f3a11f1a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904976"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Katalógusbeli virtuálisgép-rendszerképek megosztása Azure-bérlők között

A megosztott képtárak lehetővé teszik a képek megosztását a RBAC használatával. A RBAC segítségével megoszthatja a bérlőn belüli képeket, és akár a bérlőn kívüli személyeket is. További információ erről az egyszerű megosztási lehetőségről: a [gyűjtemény megosztása](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> A portálon nem lehet virtuális gépet üzembe helyezni egy másik Azure-bérlő lemezképéről. Ha a bérlők között megosztott rendszerképből szeretne virtuális gépet létrehozni, az Azure CLI-t vagy a [PowerShellt](../windows/share-images-across-tenants.md)kell használnia.

## <a name="create-a-vm-using-azure-cli"></a>Virtuális gép létrehozása az Azure CLI-vel

Jelentkezzen be az 1. bérlői szolgáltatásnév appID, az alkalmazás kulcsával és az 1. Bérlő AZONOSÍTÓjának használatával. Ha szükséges, a `az account show --query "tenantId"` segítségével kérheti le a bérlői azonosítókat.

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

Ha bármilyen problémába ütközik, a [megosztott képtárakat is elháríthatja](troubleshooting-shared-images.md).