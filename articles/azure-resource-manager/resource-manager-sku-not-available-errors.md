---
title: "Az Azure Termékváltozat nem érhető el hibák |} Microsoft Docs"
description: "Ismerteti a Termékváltozat nem érhető el hiba történt a telepítés során."
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
ms.openlocfilehash: a6cccfa5097847429d3e402e3d522addc14b8c31
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
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

## <a name="solution"></a>Megoldás

A probléma megoldásához, meg kell határoznia termékváltozatok rendelkezésre álló régióban. PowerShell, a portál vagy a REST-művelet használatával elérhető termékváltozatok található.

### <a name="solution-1"></a>1 megoldás

Használja a [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) PowerShell parancsot. Hely szerint eredmények szűrésére. A parancs PowerShell legújabb verzióját kell rendelkeznie.

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

### <a name="solution-2"></a>Megoldás 2

Az Azure CLI használata a `az vm list-skus` parancsot. Ezután `grep` vagy egy hasonló segédprogram kimenete szűréséhez.

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

### <a name="solution-3"></a>Megoldás 3

Használja a [portal](https://portal.azure.com). Jelentkezzen be a portálra, és vegyen fel egy erőforrást a felületen. Az értékeket állíthat be, megjelenik az adott erőforrás elérhető termékváltozatok. Nem kell a központi telepítés befejezéséhez.

![elérhető termékváltozatok](./media/resource-manager-sku-not-available-errors/view-sku.png)

### <a name="solution-4"></a>Megoldás 4

A REST API-t használnia a virtuális gépekhez. A következő kérelem küldése:

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
