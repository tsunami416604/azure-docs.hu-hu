---
title: Az Azure Termékváltozat nem érhető el hibák |} Microsoft Docs
description: Ismerteti a Termékváltozat nem érhető el hiba történt a telepítés során.
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
ms.openlocfilehash: 490c912a6abd6570c9bc74de8b86a516a8e6f807
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358761"
---
# <a name="resolve-errors-for-sku-not-available"></a>Javítsa ki a hibákat a termékváltozat nem érhető el

Ez a cikk ismerteti, hogyan lehet feloldani a **SkuNotAvailable** hiba.

## <a name="symptom"></a>Jelenség

Egy erőforrás (általában egy virtuális gép) telepítésekor jelenhet meg a következő hiba kód és a hibaüzenet:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ok

Ha az erőforrás (például a Virtuálisgép-méretet) kiválasztott Termékváltozat nem érhető el a kiválasztott helyen a hibaüzenetet kap.

## <a name="solution-1---powershell"></a>1 - PowerShell megoldás

Meghatározza, melyik termékváltozatok érhetők el olyan régióhoz, használja a [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) parancsot. Hely szerint eredmények szűrésére. A parancs PowerShell legújabb verzióját kell rendelkeznie.

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations -icontains "southcentralus"}
```

Az eredmények tartalmazzák a hely és a termékváltozat korlátozások termékváltozatok listáját.

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

## <a name="solution-2---azure-cli"></a>2 – az Azure CLI megoldás

Meghatározza, melyik termékváltozatok érhetők el olyan régióhoz, használja a `az vm list-skus` parancsot. Ezután `grep` vagy egy hasonló segédprogram kimenete szűréséhez.

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

## <a name="solution-3---azure-portal"></a>Megoldás 3 - Azure-portálon

Meghatározza, melyik termékváltozatok érhetők el olyan régióhoz, használja a [portal](https://portal.azure.com). Jelentkezzen be a portálra, és vegyen fel egy erőforrást a felületen. Az értékeket állíthat be, megjelenik az adott erőforrás elérhető termékváltozatok. Nem kell a központi telepítés befejezéséhez.

![elérhető termékváltozatok](./media/resource-manager-sku-not-available-errors/view-sku.png)

## <a name="solution-4---rest"></a>4 - REST megoldás

Meghatározza, melyik termékváltozatok érhetők el olyan régióhoz, használja a REST API-t a virtuális gépek. A következő kérelem küldése:

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

Elérhető termékváltozatok és régiók visszaküldi a következő formátumban:

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

Ha nem a megfelelő SKU az adott régióban található, vagy egy másik régióban, amely megfelel az üzleti van szüksége, küldje el a [SKU kérelem](https://aka.ms/skurestriction) az Azure támogatási szolgálatához.
