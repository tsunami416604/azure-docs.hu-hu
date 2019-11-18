---
title: Klasszikus Azure-beli üzembe helyezési erőforrások áthelyezése
description: A Azure Resource Manager használatával helyezheti át a klasszikus üzembe helyezési erőforrásokat egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b97496e4abfdf248b9f5010417e9284c643a74ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150853"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Útmutató a klasszikus üzemi modell erőforrásainak üzembe helyezéséhez

A klasszikus modellen keresztül üzembe helyezett erőforrások áthelyezésének lépései attól függően változnak, hogy egy előfizetésen belül vagy egy új előfizetésben helyezi át az erőforrásokat.

## <a name="move-in-the-same-subscription"></a>Áthelyezés ugyanabba az előfizetésbe

Erőforrások erőforráscsoportok közötti áthelyezése ugyanazon az előfizetésen belül egy másik erőforráscsoportba, amikor a következő korlátozások vonatkoznak:

* Nem lehet áthelyezni a virtuális hálózatok (klasszikus).
* A felhőszolgáltatás virtuális gépek (klasszikus) kell áthelyezni.
* A felhőalapú szolgáltatás csak akkor helyezhető, ha az áthelyezés tartalmazza az összes virtuális gép.
* Csak egy felhőalapú szolgáltatás egyszerre áthelyezhető.
* Csak egy tárfiók (klasszikus) egyszerre áthelyezhető.
* A tárfiók (klasszikus) nem lehet áthelyezni a virtuális gép vagy felhőszolgáltatás műveletben.

Ha a klasszikus erőforrásokat egy új erőforráscsoporthoz szeretné áthelyezni ugyanabba az előfizetésbe, használja a [standard Move műveletet](../resource-group-move-resources.md) a portálon, Azure PowerShellon, az Azure CLI-n vagy a REST APIon keresztül. Resource Manager-erőforrások áthelyezése, ahogy használhatja ugyanazokat a műveleteket.

## <a name="move-across-subscriptions"></a>Áthelyezés az előfizetések között

Ha az erőforrások áthelyezése új előfizetésre, a következő korlátozások vonatkoznak:

* Az előfizetés összes klasszikus erőforrást műveletben kell áthelyezni.
* A cél-előfizetés nem tartalmazhat más klasszikus erőforrásokat.
* Az áthelyezés csak igényelni lehet klasszikus áthelyezését a külön REST API-n keresztül. A standard szintű Resource Manager áthelyezés parancsok nem működnek, ha a klasszikus erőforrások áthelyezése új előfizetésre.

Klasszikus erőforrások áthelyezése új előfizetést, használja a REST-műveletek, konkrétan a klasszikus erőforrások. A REST használatához hajtsa végre a következő lépéseket:

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

Ha problémája van a klasszikus erőforrások áthelyezésekor, forduljon az [ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../resource-group-move-resources.md).
