---
title: Hogyan címkét egy Azure Linux virtuális gép |} Microsoft Docs
description: További információk a címkézés egy Azure Linux virtuális gép létrehozása az Azure-ban a Resource Manager üzembe helyezési modellben.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: jeconnoc
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
ms.openlocfilehash: c26019bad63a904f8ebd1241d2b58923d786e181
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937890"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Hogyan Linux virtuális gépek címke az Azure-ban
Ez a cikk ismerteti a különböző módjai a Linux virtuális gépek címke az Azure-ban a Resource Manager üzembe helyezési modellel keresztül. Címke található a felhasználó által definiált kulcs/érték párok, amely lehet tenni közvetlenül egy erőforrás vagy egy erőforráscsoportot. Azure jelenleg legfeljebb 15 címkék erőforrás pedig erőforráscsoportban. Címkék erőforrás létrehozása idején helyezni vagy hozzáadni egy meglévő erőforrást. Vegye figyelembe, csak a Resource Manager üzembe helyezési modellel létrehozott erőforrások címkék használhatók.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Az Azure CLI-címkézés
Első lépésként a legújabb kell [Azure CLI 2.0](/cli/azure/install-azure-cli) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az-login).

Az összes tulajdonság egy adott virtuális gép, többek között a címkéket, ezzel a paranccsal tekintheti meg:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Egy új virtuális gép címke az Azure parancssori felületen keresztül hozzáadásához használja a `azure vm update` parancsot, és a tag paraméter **--beállítása**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Címkék eltávolításához használja a **--eltávolítása** paramétere a `azure vm update` parancsot.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Most, hogy az erőforrások az Azure CLI és a portál jelenleg alkalmazott címkék, vessen egy pillantást a használat részleteiről a címkék a számlázási portál megjelenítéséhez.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>További lépések
* Az Azure-erőforrások címkézés kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése] [ Azure Resource Manager Overview] és [címkék használata az Azure-erőforrások rendszerezéséhez] [ Using Tags to organize your Azure Resources].
* Hogyan címkék segíthetnek az Azure-erőforrások kezeléséhez, olvassa el [ismertetése a Azure számlázási] [ Understanding your Azure Bill] és [betekintést nyerhet a Microsoft Azure erőforrás-felhasználás] [Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
