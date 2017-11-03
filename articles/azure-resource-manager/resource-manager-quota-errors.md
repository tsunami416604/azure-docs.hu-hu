---
title: "Az Azure kvóta hibák |} Microsoft Docs"
description: "Ismerteti, hogyan erőforrás qouta hibák."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 6a8024a12c4a79e92e37df0a56b6e6bd0cb6a8d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1 megoldás

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

### <a name="solution-2"></a>Megoldás 2

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

### <a name="solution-3"></a>Megoldás 3

A kvótakorlát növelnie kell, ha-portálon végezhető, és a problémának a megoldásához a régió, ahol számára telepíteni kívánja a kvóta emelése fájlt.

> [!NOTE]
> Ne feledje, hogy az erőforráscsoportok, a kvóta a minden egyes régió tartozik, nem az egész előfizetésre. Ha kell telepítenie az USA nyugati régiója 30 mag, hogy kérjen 30 erőforrás-kezelő magok az USA nyugati régiója. Ha sem a régiók, amelyhez hozzáfér 30 magok telepíteni kell, kérdezze meg a 30 erőforrás-kezelő magok minden régióban.
>
>