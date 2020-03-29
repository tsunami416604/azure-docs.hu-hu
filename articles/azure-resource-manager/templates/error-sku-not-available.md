---
title: A termékváltozat nem érhető el hibák
description: Ez a témakör ismerteti, hogyan háríthatja el a termékváltozat nem érhető el hiba, amikor erőforrásokat telepít az Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942728"
---
# <a name="resolve-errors-for-sku-not-available"></a>Nem elérhető termékváltozattal kapcsolatos hibák elhárítása

Ez a cikk a **SkuNotAvailable** hiba megoldását ismerteti. Ha nem talál megfelelő termékváltozatot az adott régióban/zónában, vagy egy alternatív régiót/zónát, amely megfelel az üzleti igényeinek, küldjön egy [Termékváltozat-kérelmet](https://aka.ms/skurestriction) az Azure-támogatásnak.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Erőforrás (általában virtuális gép) telepítésekor a következő hibaüzenet és hibaüzenet jelenik meg:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ok

Ez a hibaüzenet akkor jelenik meg, ha a kiválasztott erőforrás termékváltozat (például a virtuális gép mérete) nem érhető el a kiválasztott helyhez.

Ha egy Azure Spot virtuális gép vagy direkt méretezési csoport példánya, nincs kapacitás az Azure Spot ezen a helyen. További információt a [Hibaüzenetek felismerése](../../virtual-machines/error-codes-spot.md)című témakörben talál.

## <a name="solution-1---powershell"></a>1. megoldás – PowerShell

Annak megállapításához, hogy mely termékváltozatok érhetők el egy régióban/zónában, használja a [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) parancsot. Az eredmények szűrése hely szerint. Ehhez a parancshoz a PowerShell legújabb verziójával kell rendelkeznie.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Az eredmények tartalmazzák a hely termékváltozatainak listáját és az adott termékváltozatra vonatkozó korlátozásokat. Figyelje meg, hogy egy `NotAvailableForSubscription`termékváltozat a néven szerepelhet.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Néhány további minta:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

Az "fc" hozzáfűzése a végén további részleteket ad vissza.

## <a name="solution-2---azure-cli"></a>2. megoldás - Azure CLI

Annak meghatározásához, hogy mely sk-ek `az vm list-skus` érhetők el egy régióban, használja a parancsot. A `--location` paraméter segítségével szűrheti a kimenetet a használt helyre. A `--size` paraméter segítségével részleges méretű név alapján kereshet.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

A parancs a következő eredményeket adja vissza:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>3. megoldás – Azure-portál

Annak meghatározásához, hogy mely sk-ek érhetők el egy régióban, használja a [portált.](https://portal.azure.com) Jelentkezzen be a portálra, és adjon hozzá egy erőforrást a kapcsolaton keresztül. Az értékek beállításakor az adott erőforráshoz rendelkezésre álló sk-ek jelennek meg. Nem kell befejeznie a telepítést.

Indítsa el például a virtuális gép létrehozásának folyamatát. A többi elérhető méret megtekintéséhez válassza a **Méret módosítása**lehetőséget.

![Virtuális gép létrehozása](./media/error-sku-not-available/create-vm.png)

Szűrheti és görgetheti a rendelkezésre álló méreteket.

![Elérhető termékváltozatok](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>4. megoldás - REST

Annak meghatározásához, hogy mely skus érhető el egy régióban, használja az [Erőforrás-skus - Lista](/rest/api/compute/resourceskus/list) műveletet.

A rendelkezésre álló sk-eket és régiókat a következő formátumban adja vissza:

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

