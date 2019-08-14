---
title: Azure Linux rendszerű virtuális gép címkézése | Microsoft Docs
description: Ismerje meg, hogyan címkézheti az Azure-ban létrehozott Azure Linux rendszerű virtuális gépeket a Resource Manager-alapú üzemi modell használatával.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67708744"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gép címkézése az Azure-ban
Ez a cikk az Azure-beli linuxos virtuális gépek Resource Manager-alapú üzemi modellel történő címkézésének különböző módszereit ismerteti. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 15 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson. Vegye figyelembe, hogy a csak a Resource Manager-alapú üzemi modellel létrehozott erőforrások esetében támogatott a címkék használata.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Címkézés az Azure CLI-vel

Első lépésként telepítenie kell a legújabb [Azure CLI](/cli/azure/install-azure-cli) -t, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index#az-login)használatával.

A következő parancs használatával megtekintheti az adott virtuális gép összes tulajdonságát, beleértve a címkéket is:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Ha új virtuálisgép-címkét szeretne hozzáadni az Azure CLI-n keresztül, `azure vm update` használja a parancsot a címke paraméterrel együtt – **set**:

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

## <a name="next-steps"></a>További lépések
* Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése][Azure Resource Manager Overview] és [címkék használata az Azure-erőforrások rendszerezéséhez][Using Tags to organize your Azure Resources].
* Ha szeretné megtekinteni, hogyan segíthetnek az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla megismerése][Understanding your Azure Bill] és [a Microsoft Azure erőforrás-felhasználás][Gain insights into your Microsoft Azure resource consumption]betekintését ismertető témakört.

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
