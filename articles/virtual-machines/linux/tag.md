---
title: Egy Azure-beli Linuxos virtuális gép címkézése |} A Microsoft Docs
description: További információ az Azure Resource Manager-alapú üzemi modellel létrehozott, egy Azure-beli Linuxos virtuális gép címkézése.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 290105b4e5e3ac3337b0be1b7d437601223bdf68
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708744"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gép címkézése Azure-ban
Ez a cikk ismerteti a különböző módjai a Linux rendszerű virtuális gép címkézése Azure-ban a Resource Manager-alapú üzemi modellel. A címkék olyan felhasználó által definiált kulcs/érték párok, amelyeket közvetlenül az erőforrás vagy erőforráscsoport helyezhető. Az Azure jelenleg támogatja az erőforrás és erőforráscsoport legfeljebb 15 címkék. Címkék erőforrás elhelyezve a létrehozásakor vagy hozzáadni egy meglévő erőforrást. Ne feledje, csak a Resource Manager üzemi modell használatával létrehozott erőforrások címkék támogatottak.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Az Azure CLI-vel címkézése

Első lépésként kell a legújabb [Azure CLI-vel](/cli/azure/install-azure-cli) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az-login).

Az összes tulajdonság egy adott virtuális gép, a címkéket, beleértve a következő paranccsal tekintheti meg:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Adjon hozzá egy új virtuális gép címke az Azure CLI-n keresztül, használhatja a `azure vm update` parancsot a címke paraméterrel együtt **--beállítása**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Címke eltávolításához használhatja a **--eltávolítása** paramétert a `azure vm update` parancsot.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Most, hogy az erőforrások az Azure CLI és a portálon azt alkalmazott címkék, vessünk egy pillantást a használat részleteiről a számlázási portálon címkéinek megtekintéséhez.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>További lépések
* Az Azure-erőforrások címkézése kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése][Azure Resource Manager Overview] and [Using Tags to organize your Azure Resources][Using Tags to organize your Azure Resources].
* Hogyan címkék segít Önnek az Azure-erőforrások kezeléséhez, olvassa el [az Azure-elszámolások ismertetése][Understanding your Azure Bill] and [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
