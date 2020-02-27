---
title: Azure Linux rendszerű virtuális gép címkézése
description: Ismerje meg, hogyan címkézheti az Azure-ban létrehozott Azure Linux rendszerű virtuális gépeket a Resource Manager-alapú üzemi modell használatával.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 3dd46f0c1c1e36e0373311ce6dcbf5d2f155dacc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616272"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gép címkézése az Azure-ban
Ez a cikk az Azure-beli linuxos virtuális gépek Resource Manager-alapú üzemi modellel történő címkézésének különböző módszereit ismerteti. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 50 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson. Vegye figyelembe, hogy a csak a Resource Manager-alapú üzemi modellel létrehozott erőforrások esetében támogatott a címkék használata.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Címkézés az Azure CLI-vel

Első lépésként telepítenie kell a legújabb [Azure CLI](/cli/azure/install-azure-cli) -t, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index#az-login)használatával.

A következő parancs használatával megtekintheti az adott virtuális gép összes tulajdonságát, beleértve a címkéket is:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Ha új virtuálisgép-címkét szeretne hozzáadni az Azure CLI-n keresztül, használja a `azure vm update` parancsot a címke paraméterrel együtt – **set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

A címkék eltávolításához használhatja a **--Remove** paramétert a `azure vm update` parancsban.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Most, hogy címkéket alkalmazottunk az erőforrásokhoz az Azure CLI-ben és a portálon, vessünk egy pillantást a használati adatokra, hogy megtekintsék a címkéket a számlázási portálon.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Következő lépések
* Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése][Azure Resource Manager Overview] és [címkék használata az Azure-erőforrások rendszerezéséhez][Using Tags to organize your Azure Resources].
* Ha szeretné megtekinteni, hogyan segíthetnek az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla megismerése][Understanding your Azure Bill] és [a Microsoft Azure erőforrás-felhasználás betekintését][Gain insights into your Microsoft Azure resource consumption]ismertető témakört.

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
