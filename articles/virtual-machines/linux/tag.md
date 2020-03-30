---
title: Azure Linux-virtuális gépek címkézése
description: Ismerje meg az Azure-ban létrehozott Azure-beli virtuális gépek címkézését a Resource Manager telepítési modell használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616272"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Linuxos virtuális gépek címkézése az Azure-ban
Ez a cikk ismerteti a Linux virtuális gépek címkézésének különböző módjait az Azure-ban a Resource Manager üzembe helyezési modellen keresztül. A címkék a felhasználó által definiált kulcs-/értékpárok, amelyek közvetlenül egy erőforrásra vagy erőforráscsoportra helyezhetők. Az Azure jelenleg erőforrás- és erőforráscsoportonként legfeljebb 50 címkét támogat. A címkék elhelyezhetők egy erőforráson a létrehozás időpontjában, vagy hozzáadhatók egy meglévő erőforráshoz. Kérjük, vegye figyelembe, címkék csak az Erőforrás-kezelő telepítési modell en létrehozott erőforrások támogatottak.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Címkézés az Azure CLI-vel

Először is szüksége van a legújabb [Azure CLI](/cli/azure/install-azure-cli) telepítve van, és bejelentkezett egy Azure-fiók [használatával az bejelentkezési.](/cli/azure/reference-index#az-login)

Az adott virtuális gép összes tulajdonságát megtekintheti, beleértve a címkéket is, ezzel a paranccsal:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Ha új virtuálisgép-címkét szeretne hozzáadni az Azure `azure vm update` CLI-n keresztül, használhatja a parancsot a **--set**címke paraméterrel együtt:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

A címkék eltávolításához használhatja a **--remove** paramétert a `azure vm update` parancsban.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Most, hogy az Azure CLI és a portál az erőforrásokra alkalmazott címkéket, vessünk egy pillantást a használati adatokat a címkék megtekintéséhez a számlázási portálon.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>További lépések
* Ha többet szeretne megtudni az Azure-erőforrások címkézéséről, olvassa el az [Azure Resource Manager áttekintése][Azure Resource Manager Overview] és [címkék használata az Azure-erőforrások rendszerezéséhez című témakört.][Using Tags to organize your Azure Resources]
* Ha meg szeretné tekinteni, hogy a címkék hogyan segíthetnek az Azure-erőforrások használatának kezelésében, [olvassa el Az Azure-számla ismertetése][Understanding your Azure Bill] és [a Microsoft Azure-erőforrás-felhasználás elemzéseinek megismerése című témakört.][Gain insights into your Microsoft Azure resource consumption]

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
