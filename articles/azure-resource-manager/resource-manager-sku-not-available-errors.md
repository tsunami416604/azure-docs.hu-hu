---
title: Az Azure-Termékváltozat nem érhető el hibák |} A Microsoft Docs
description: Ismerteti, hogyan háríthatók el a Termékváltozat nem érhető el hiba üzembe helyezés során.
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
ms.openlocfilehash: 1dd0532452c3558e53f0236998953d2055ed328c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489690"
---
# <a name="resolve-errors-for-sku-not-available"></a>Javítsa ki a hibákat a termékváltozat nem érhető el

Ez a cikk bemutatja, hogyan oldja meg a **SkuNotAvailable** hiba. Ha Ön nem található megfelelő Termékváltozat az adott régióban, vagy egy másik régióba, amely megfelel az üzleti van szüksége, adjon be egy [Termékváltozat kérelem](https://aka.ms/skurestriction) Azure támogatási szolgálatának.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Jelenség

Egy erőforrás (általában egy virtuális gép) üzembe helyezése során a következő hibakóddal és a hibaüzenet:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ok

Ezt a hibaüzenetet kapja, amikor az erőforrás (például a virtuális gép mérete) kiválasztott Termékváltozat nem érhető el a kiválasztott helyen.

## <a name="solution-1---powershell"></a>Megoldás 1 – PowerShell

Meghatározza, melyik SKU-k egy régióban érhető el, használja a [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) parancsot. Hely szerint az eredmények szűréséhez. Ezzel a paranccsal a PowerShell legújabb verzióját kell rendelkeznie.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Az eredmények tartalmazzák a termékváltozatok listája a hely és a megadott korlátozások nélkül, az adott Termékváltozatot. Figyelje meg, hogy a Termékváltozat is szerepelhet, mint `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>2 – Azure CLI megoldás

Meghatározza, melyik SKU-k egy régióban érhető el, használja a `az vm list-skus` parancsot. Használja a `--location` paraméter szűrése a kimeneti helyet használ. Használja a `--size` paraméter egy részleges mérete nevet a kereséshez.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

A parancs hasonló eredményeket ad vissza:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>3 – Azure portal megoldás

Meghatározza, melyik SKU-k egy régióban érhető el, használja a [portál](https://portal.azure.com). Jelentkezzen be a portálra, és adjon hozzá egy erőforrást a felületen. Az értékeket állít be, mivel láthatja az adott erőforráshoz rendelkezésre álló termékváltozatok. Nem kell a telepítés befejezéséhez.

Például indítsa el a virtuális gép létrehozásának folyamatán. Egyéb elérhető méretének megtekintéséhez válasszon **méretének módosítása**.

![Virtuális gép létrehozása](./media/resource-manager-sku-not-available-errors/create-vm.png)

Szűrés, és görgessen az elérhető méretek keresztül.

![A rendelkezésre álló termékváltozatok](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Megoldás 4 – REST

Meghatározza, melyik SKU-k egy régióban érhető el, használja a [erőforrás SKU - lista](/rest/api/compute/resourceskus/list) műveletet.

Rendelkezésre álló termékváltozatok és régiók, visszaadja a következő formátumban:

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

