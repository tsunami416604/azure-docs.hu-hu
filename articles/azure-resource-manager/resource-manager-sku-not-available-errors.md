---
title: Azure SKU nem érhető el hibák | Microsoft Docs
description: Ismerteti, hogyan lehet elhárítani az SKU nem elérhető hibáját az erőforrások Azure Resource Manager használatával történő telepítésekor.
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
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: fca028412052a9a1520e1178f5d182a9987a9a85
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390222"
---
# <a name="resolve-errors-for-sku-not-available"></a>Az SKU-hoz nem elérhető hibák elhárítása

Ez a cikk a **SkuNotAvailable** -hiba elhárítását ismerteti. Ha nem talál megfelelő SKU-t az adott régióban vagy egy olyan alternatív régióban, amely megfelel az Ön üzleti igényeinek, küldjön be egy [SKU-kérést](https://aka.ms/skurestriction) az Azure támogatási szolgálatának.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Egy erőforrás (általában egy virtuális gép) telepítésekor a következő hibakód és hibaüzenet jelenik meg:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ok

Ez a hiba akkor jelenik meg, ha a kiválasztott erőforrás-SKU (például a virtuális gép mérete) nem érhető el a kiválasztott helyhez.

## <a name="solution-1---powershell"></a>1\. megoldás – PowerShell

Az egyes régiókban elérhető SKU-ket a [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) parancs használatával állapíthatja meg. Az eredmények kiszűrése hely szerint. Ehhez a parancshoz a PowerShell legújabb verzióját kell megadnia.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Az eredmények közé tartozik a helyhez tartozó SKU-lista és az adott SKU korlátozásai. Figyelje meg, hogy az SKU lehet `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>2\. megoldás – Azure CLI

A régiókban elérhető SKU-ket a `az vm list-skus` paranccsal állapíthatja meg. A `--location` paraméter használatával szűrheti a kimenetet a használt helyre. A `--size` paraméterrel megkeresheti a részleges méret nevét.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

A parancs a következőhöz hasonló eredményeket ad vissza:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>3\. megoldás – Azure Portal

Az adott régióban elérhető SKU-ket a [portálon](https://portal.azure.com)állapíthatja meg. Jelentkezzen be a portálra, és adjon hozzá egy erőforrást az interfészen keresztül. Az értékek megadásakor az adott erőforráshoz rendelkezésre álló SKU-ket láthatja. Nem kell végrehajtania az üzembe helyezést.

Megkezdheti például a virtuális gép létrehozásának folyamatát. Ha más elérhető méretet szeretne látni, válassza a **méret módosítása**lehetőséget.

![Virtuális gép létrehozása](./media/resource-manager-sku-not-available-errors/create-vm.png)

A rendelkezésre álló méretek szűrése és görgetése lehetséges.

![Rendelkezésre álló SKU-i](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>4\. megoldás – REST

A régiókban elérhető SKU-ket a [forrás SKU-List](/rest/api/compute/resourceskus/list) művelettel állapíthatja meg.

Az elérhető SKU-ket és régiókat a következő formátumban adja vissza:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

