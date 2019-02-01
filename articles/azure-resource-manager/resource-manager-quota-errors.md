---
title: Az Azure-kvóta hibák |} A Microsoft Docs
description: Erőforrás-kvóta hibák megoldását ismerteti.
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
ms.openlocfilehash: 7938f2c47e4af8d8804191fbb9e55b379f9554ef
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488612"
---
# <a name="resolve-errors-for-resource-quotas"></a>Erőforráskvóták hibáinak megoldásához

Ez a cikk ismerteti a kvóta hibák erőforrások üzembe helyezésekor.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Jelenség

Ha telepít egy sablont, amely az Azure a kvóták túllépéséből erőforrást hoz létre, kap egy központi telepítési hiba, amely a következőhöz hasonló:

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

Egy erőforráscsoport, előfizetések, partnerek és más hatókörök kvótái vonatkoznak. Például az előfizetés konfigurálhatók korlátozni a régió magok számát. Ha megpróbálja telepíteni, mint a megengedett mennyiséget több maggal rendelkező virtuális gép, a kvóta túl lett lépve érvénytelenségét jelző hibaüzenet kap.
Teljes kvóta információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="azure-cli"></a>Azure CLI

Azure CLI esetén használja a `az vm list-usage` parancs használatával keresse meg a virtuális gép kvóták.

```azurecli
az vm list-usage --location "South Central US"
```

Amely értéket ad vissza:

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

PowerShell esetén használja a **Get-AzVMUsage** parancs használatával keresse meg a virtuális gép kvóták.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Amely értéket ad vissza:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Megoldás

Kérje egy kvótájának növelését, lépjen a portálra, és a egy támogatási a problémát. A támogatási problémát, a a régió, amelybe telepíteni kívánja a kvóta növelését.

> [!NOTE]
> Ne feledje, hogy az erőforráscsoportok, a kvóta minden egyes régió, a teljes előfizetés esetében nem. Ha kell telepíteni az USA nyugati adatközpontjába 30 magot, meg kell kérnie 30 Resource Manager-magok az USA nyugati RÉGIÓJA. Ha szeretne üzembe helyezése bármely, a régiók, amelyhez rendelkezik hozzáféréssel 30 magot, kérdezze meg 30 Resource Manager-magok minden régióban.
>
>

1. Válassza az **Előfizetések** lehetőséget.

   ![Előfizetések](./media/resource-manager-quota-errors/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés kiválasztása](./media/resource-manager-quota-errors/select-subscription.png)

3. Válassza ki **használat + kvóták**

   ![Válassza ki a használat és kvóták](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. A jobb felső sarokban, válassza ki **növelésére**.

   ![Növelés kérése](./media/resource-manager-quota-errors/request-increase.png)

5. Töltse ki az űrlapot ahhoz a kvótatípushoz, amelyet növelni szeretne.

   ![Űrlap kitöltése](./media/resource-manager-quota-errors/forms.png)