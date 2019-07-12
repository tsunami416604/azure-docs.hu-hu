---
title: A klasszikus Azure üzembe helyezési erőforrások áthelyezése új előfizetést vagy az erőforrás-csoport
description: Azure Resource Manager használatával a klasszikus üzembe helyezési erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723505"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Útmutató a klasszikus telepítési modell erőforrások áthelyezése

A lépések a klasszikus modellben telepített erőforrások áthelyezéséhez eltérőek Ha áthelyez egy előfizetésen belül vagy egy új előfizetést az erőforrások alapján.

## <a name="move-in-the-same-subscription"></a>Azonos előfizetésben található áthelyezése

Erőforrások erőforráscsoportok közötti áthelyezése ugyanazon az előfizetésen belül egy másik erőforráscsoportba, amikor a következő korlátozások vonatkoznak:

* Nem lehet áthelyezni a virtuális hálózatok (klasszikus).
* A felhőszolgáltatás virtuális gépek (klasszikus) kell áthelyezni.
* A felhőalapú szolgáltatás csak akkor helyezhető, ha az áthelyezés tartalmazza az összes virtuális gép.
* Csak egy felhőalapú szolgáltatás egyszerre áthelyezhető.
* Csak egy tárfiók (klasszikus) egyszerre áthelyezhető.
* A tárfiók (klasszikus) nem lehet áthelyezni a virtuális gép vagy felhőszolgáltatás műveletben.

Klasszikus erőforrások áthelyezése ugyanazon az előfizetésen belül egy új erőforráscsoportot, használja a [standard áthelyezési műveleteket](../resource-group-move-resources.md) a portal, Azure PowerShell-lel, az Azure CLI vagy REST API használatával. Resource Manager-erőforrások áthelyezése, ahogy használhatja ugyanazokat a műveleteket.

## <a name="move-across-subscriptions"></a>Áthelyezése előfizetések között

Ha az erőforrások áthelyezése új előfizetésre, a következő korlátozások vonatkoznak:

* Az előfizetés összes klasszikus erőforrást műveletben kell áthelyezni.
* A célként megadott előfizetés nem kell klasszikus erőforrásokat.
* Az áthelyezés csak igényelni lehet klasszikus áthelyezését a külön REST API-n keresztül. A standard szintű Resource Manager áthelyezés parancsok nem működnek, ha a klasszikus erőforrások áthelyezése új előfizetésre.

Klasszikus erőforrások áthelyezése új előfizetést, használja a REST-műveletek, konkrétan a klasszikus erőforrások. A REST használata, hajtsa végre a következő lépéseket:

1. Ellenőrizze, hogy ha a forrás-előfizetés részt vehetnek-e egy előfizetések közötti áthelyezés. Használja a következő műveletet:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     A kérelem törzsében a következők:

   ```json
   {
    "role": "source"
   }
   ```

     A választ az érvényesítés művelet a következő formátumban kell megadni:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Ellenőrizze, hogy ha a cél előfizetést részt vehetnek-e egy előfizetések közötti áthelyezés. Használja a következő műveletet:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     A kérelem törzsében a következők:

   ```json
   {
    "role": "target"
   }
   ```

     A válasz a forrás-előfizetés érvényesítése ugyanebben a formátumban van.
1. Ha mindkét előfizetés érvényesítési sikeresek, minden hagyományos erőforrás áthelyezése egy előfizetésből egy másik előfizetéshez a következő műveletet:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    A kérelem törzsében a következők:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A művelet több percig futtathatnak.

## <a name="next-steps"></a>További lépések

Ha a klasszikus erőforrások áthelyezése gondja van, lépjen kapcsolatba [támogatási](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../resource-group-move-resources.md).
