---
title: Galériaképek megosztása az Azure-beli bérlők között
description: Megtudhatja, hogyan oszthatja meg a virtuálisgép-lemezképeket az Azure-bérlők között a megosztott képgalériák használatával.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034973"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Galéria virtuálisgép-lemezképeinek megosztása az Azure-bérlők között

A megosztott képgalériák lehetővé teszik a képek megosztását az RBAC használatával. Az RBAC segítségével megoszthatja a lemezképeket a bérlőn belül, és még a bérlőn kívüli személyek számára is. Erről az egyszerű megosztási lehetőségről a [Galéria megosztása című témakörben](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)talál további információt.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> A portál nem használhatja a virtuális gép üzembe helyezéséhez egy lemezkép egy másik azure-bérlő. Ha virtuális gép létrehozása a bérlők között megosztott lemezképből, az Azure CLI vagy a Powershell kell [használnia.](../windows/share-images-across-tenants.md)

## <a name="create-a-vm-using-azure-cli"></a>Virtuális gép létrehozása az Azure CLI használatával

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

Hozza létre a virtuális gép. Cserélje le a példában szereplő információkat a sajátjára.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>További lépések

Ha bármilyen problémába ütközik, [elháríthatja a megosztott képgalériákat.](troubleshooting-shared-images.md)