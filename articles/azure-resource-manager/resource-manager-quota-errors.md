---
title: Azure-kvóta hibái | Microsoft Docs
description: Ismerteti, Hogyan oldhatók fel az erőforrás-kvótával kapcsolatos hibák az erőforrások Azure Resource Manager használatával történő telepítésekor.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 201ddf69f9c28b5b3a4197f91768f749152094de
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390307"
---
# <a name="resolve-errors-for-resource-quotas"></a>Erőforrás-kvóták hibáinak elhárítása

Ez a cikk az erőforrások telepítése során felmerülő kvóta-hibákat ismerteti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Ha olyan sablont helyez üzembe, amely túllépi az Azure-kvótákat, a következőhöz hasonló telepítési hiba jelenik meg:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Vagy a következőket láthatja:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Ok

A kvóták erőforráscsoportonként, előfizetésenként, fiókonként és egyéb hatókörönként érvényesek. Lehetséges például, hogy az előfizetése úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Ha olyan virtuális gépet próbál üzembe helyezni, amely a megengedettnél több magot tartalmaz, akkor hibaüzenetet kap a kvóta túllépéséről.
A kvóta részletes ismertetését az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../azure-subscription-service-limits.md)című témakörben tekintheti meg.

## <a name="troubleshooting"></a>Hibakeresés

### <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az Azure CLI esetén a `az vm list-usage` paranccsal keresse meg a virtuálisgép-kvótákat.

```azurecli
az vm list-usage --location "South Central US"
```

Amely a következőket adja vissza:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

A PowerShell esetében használja a **Get-AzVMUsage** parancsot a virtuálisgép-kvóták megkereséséhez.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Amely a következőket adja vissza:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Megoldás

A kvóta növeléséhez lépjen a portálra, és küldjön egy támogatási problémát. A támogatási probléma esetén a kvóta növelését kell megadnia ahhoz a régióhoz, amelybe telepíteni kívánja.

> [!NOTE]
> Ne feledje, hogy az erőforráscsoportok esetében a kvóta minden egyes régióra érvényes, nem a teljes előfizetéshez. Ha 30 magot kell üzembe helyeznie az USA nyugati régiójában, az USA nyugati régiójában 30 Resource Manager-magot kell kérnie. Ha minden olyan régióban 30 magot kell üzembe helyeznie, amelyhez hozzáférése van, akkor minden régióban 30 Resource Manager-magot kell kérnie.
>
>

1. Válassza az **Előfizetések** lehetőséget.

   ![Előfizetések](./media/resource-manager-quota-errors/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés kiválasztása](./media/resource-manager-quota-errors/select-subscription.png)

3. **Használat + kvóták** kiválasztása

   ![Használat és kvóták kiválasztása](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

   ![Kérések növekedése](./media/resource-manager-quota-errors/request-increase.png)

5. Töltse ki az űrlapot ahhoz a kvótatípushoz, amelyet növelni szeretne.

   ![Űrlap kitöltése](./media/resource-manager-quota-errors/forms.png)