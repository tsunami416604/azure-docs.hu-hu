---
title: Az Azure kvóta hibák |} Microsoft Docs
description: Ismerteti, hogyan erőforrás qouta hibák.
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
ms.openlocfilehash: 6d9048ae531abedb89b70989ce1c962357c514cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357044"
---
# <a name="resolve-errors-for-resource-quotas"></a>Javítsa a hibákat a erőforráskvótákkal

Ez a cikk ismerteti a kvóta hibák jelentkezhetnek, ha erőforrásokat üzembe helyezi.

## <a name="symptom"></a>Jelenség

Ha telepít egy sablont, amely hoz létre, amelyek mérete meghaladja az Azure-kvótákról erőforrásokat, kap egy központi telepítési hiba, amely a következőhöz hasonló:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Vagy jelenhetnek meg:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Ok

Kvóták egy erőforráscsoport, előfizetések, fiókok és egyéb hatókörök érvényesek. Például az előfizetés beállítható az a régió magok száma korlátozza. Ha megkísérli az engedélyezett értéknél több maggal rendelkező virtuális gép telepítése, hibaüzenet kap, a kvóta túl lett lépve.
Teljes kvóta információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="azure-cli"></a>Azure CLI

Az Azure parancssori felület, használja a `az vm list-usage` található virtuális gép kvóták parancsot.

```azurecli
az vm list-usage --location "South Central US"
```

Amely adja vissza:

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

A PowerShell környezethez, használja a **Get-AzureRmVMUsage** található virtuális gép kvóták parancsot.

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

Amely adja vissza:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Megoldás

Kérje meg a kvótájának növelését, nyissa meg a portál és a a problémának a megoldásához. A támogatási probléma kér a régió, ahol számára telepíteni kívánja a kvótájának növelését.

> [!NOTE]
> Ne feledje, hogy az erőforráscsoportok, a kvóta a minden egyes régió tartozik, nem az egész előfizetésre. Ha kell telepítenie az USA nyugati régiója 30 mag, hogy kérjen 30 erőforrás-kezelő magok az USA nyugati régiója. Ha sem a régiók, amelyhez hozzáfér 30 magok telepíteni kell, kérdezze meg a 30 erőforrás-kezelő magok minden régióban.
>
>

1. Válassza ki **előfizetések**.

   ![Előfizetések](./media/resource-manager-quota-errors/subscriptions.png)

2. Válassza ki az előfizetést, amelyet egy nagyobb kvótát.

   ![Előfizetés kiválasztása](./media/resource-manager-quota-errors/select-subscription.png)

3. Válassza ki **használati + kvóták**

   ![Válassza ki a használati és kvóták](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. A jobb felső sarokban válassza **megnövelésére**.

   ![Növelés kérése](./media/resource-manager-quota-errors/request-increase.png)

5. Töltse ki az űrlapok kvóta növelnie kell a típushoz.

   ![Adja meg a képernyő](./media/resource-manager-quota-errors/forms.png)