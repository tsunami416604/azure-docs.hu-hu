---
title: Azure-beli virtuális gép címkézése a parancssori felület használatával
description: Ismerje meg, hogyan címkézheti a virtuális gépeket az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595027"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Virtuális gép címkézése a parancssori felület használatával

Ez a cikk bemutatja, hogyan címkézheti a virtuális gépet az Azure CLI használatával. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 50 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson. A virtuális gépeket az Azure [PowerShell](tag-powershell.md)használatával is címkézheti.


Megtekintheti egy adott virtuális gép összes tulajdonságát, beleértve a címkéket is, a használatával `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Ha új virtuálisgép-címkét szeretne hozzáadni az Azure CLI-n keresztül, a `azure vm update` parancsot a címke paraméterrel együtt használhatja `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

A címkék eltávolításához használhatja a `--remove` paramétert a `azure vm update` parancsban.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Most, hogy címkéket alkalmazottunk az erőforrásokhoz az Azure CLI-ben és a portálon, vessünk egy pillantást a használati adatokra, hogy megtekintsék a címkéket a számlázási portálon.


**Következő lépések**

- Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md) és [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).
- Ha szeretné megtekinteni, hogyan segíthetnek az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla megismerése](../cost-management-billing/understand/review-individual-bill.md) és [a Microsoft Azure erőforrás-felhasználás betekintését](../cost-management-billing/manage/usage-rate-card-overview.md)ismertető témakört.
