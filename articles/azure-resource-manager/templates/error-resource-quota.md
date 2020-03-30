---
title: Kvótahibák
description: Ez a témakör ismerteti, hogyan oldhatja meg az erőforráskvóta-hibákat az Erőforrások Azure Resource Manager rel történő üzembe helyezésekor.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 410b086b39d63d03491d390364f4aec6300fc7c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273786"
---
# <a name="resolve-errors-for-resource-quotas"></a>Erőforráskvótákkal kapcsolatos hibák elhárítása

Ez a cikk az erőforrások telepítésekor előforduló kvótahibákat ismerteti.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Ha olyan sablont telepít, amely az Azure-kvótákat meghaladó erőforrásokat hoz létre, a következő központi telepítési hibát kap:

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
A teljes kvótainformációt az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások című témakörben talál.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI, `az vm list-usage` használja a parancsot a virtuális gép kvóták keresése.

```azurecli
az vm list-usage --location "South Central US"
```

Melyik adja vissza:

```output
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

Melyik adja vissza:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Megoldás

Kvótanövelés igényléséhez nyissa meg a portált, és nyújtson be támogatási problémát. A támogatási probléma, kérje a kvóta növelését a régió, amelyre telepíteni kívánt.

> [!NOTE]
> Ne feledje, hogy az erőforráscsoportok esetében a kvóta az egyes régiókra vonatkozik, nem a teljes előfizetésre. Ha 30 mag telepítését kell telepítenie az USA nyugati részén, 30 Erőforrás-kezelő magját kell kérnie az USA nyugati részén. Ha 30 mag üzembe helyezéséhez kell rendelkeznie bármelyik olyan régióban, amelyhez hozzáféréssel rendelkezik, kérjen 30 Resource Manager-magot az összes régióban.
>
>

1. Válassza az **Előfizetések** lehetőséget.

   ![Előfizetések](./media/error-resource-quota/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés kiválasztása](./media/error-resource-quota/select-subscription.png)

3. **Felhasználás + kvóták** kiválasztása

   ![Használat és kvóták kiválasztása](./media/error-resource-quota/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **Kérelem növelése lehetőséget.**

   ![Emelés kérése](./media/error-resource-quota/request-increase.png)

5. Töltse ki az űrlapot ahhoz a kvótatípushoz, amelyet növelni szeretne.

   ![Űrlap kitöltése](./media/error-resource-quota/forms.png)