---
title: Az Azure Classic telepítési erőforrásainak áthelyezése
description: Az Azure Resource Manager használatával klasszikus üzembe helyezési erőforrásokat helyezhet át egy új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485285"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Útmutató áthelyezése a klasszikus telepítési modell erőforrásaihoz

A klasszikus modellen keresztül üzembe helyezett erőforrások áthelyezésének lépései eltérnek attól függően, hogy egy előfizetésen belül vagy egy új előfizetésen belül helyezi-e át az erőforrásokat.

## <a name="move-in-the-same-subscription"></a>Áthelyezés ugyanabban az előfizetésben

Amikor erőforrásokat helyez át egy erőforráscsoportból egy másik erőforráscsoportba ugyanazon az előfizetésen belül, a következő korlátozások érvényesek:

* A virtuális hálózatok (klasszikus) nem helyezhetők át.
* A virtuális gépeket (klasszikus) át kell helyezni a felhőszolgáltatással.
* A felhőszolgáltatás csak akkor helyezhető át, ha az áthelyezés az összes virtuális gépet tartalmazza.
* Egyszerre csak egy felhőszolgáltatás helyezhető át.
* Egyszerre csak egy tárfiók (klasszikus) helyezhető át.
* Tárfiók (klasszikus) nem helyezhető át ugyanabban a műveletben egy virtuális gép vagy egy felhőszolgáltatás.

Klasszikus erőforrások áthelyezése egy új erőforráscsoport ugyanazon az előfizetésen belül, használja a [szokásos áthelyezési műveleteket](../move-resource-group-and-subscription.md) a portálon keresztül, az Azure PowerShell, az Azure CLI vagy a REST API-t. Ugyanazokat a műveleteket használja, mint az Erőforrás-kezelő erőforrásainak áthelyezéséhez.

## <a name="move-across-subscriptions"></a>Áthelyezés az előfizetések között

Amikor erőforrásokat helyez át egy új előfizetésbe, a következő korlátozások érvényesek:

* Az előfizetés összes klasszikus erőforrását ugyanabban a műveletben kell áthelyezni.
* A cél-előfizetés nem rendelkezhet más klasszikus erőforrásokkal.
* Az áthelyezés csak egy külön REST API-n keresztül kérhető a klasszikus áthelyezések esetén. A szabványos Erőforrás-kezelő áthelyezési parancsok nem működnek, amikor klasszikus erőforrásokat helyez át egy új előfizetésbe.

Klasszikus erőforrások áthelyezése egy új előfizetés, használja a KLASSZIKUS erőforrásokra jellemző REST-műveleteket. A REST használatához tegye a következő lépéseket:

1. Ellenőrizze, hogy a forrás-előfizetés részt vehet-e egy előfizetésközi áthelyezésben. Használja a következő műveletet:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     A kérelem törzsében a következők et kell megadni:

   ```json
   {
    "role": "source"
   }
   ```

     Az érvényesítési műveletre a következő formátumú a válasz:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Ellenőrizze, hogy a cél-előfizetés részt vehet-e egy előfizetésközi áthelyezésben. Használja a következő műveletet:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     A kérelem törzsében a következők et kell megadni:

   ```json
   {
    "role": "target"
   }
   ```

     A válasz formátuma megegyezik a forrás-előfizetés érvényesítésével.
1. Ha mindkét előfizetés megfelel az ellenőrzésen, az összes klasszikus erőforrásáthelyezése az egyik előfizetésből egy másik előfizetésbe a következő művelettel:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    A kérelem törzsében a következők et kell megadni:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A művelet több percig is futhat.

## <a name="next-steps"></a>További lépések

Ha nem sikerül klasszikus erőforrásokat áthelyezni, forduljon [az ügyfélszolgálathoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

Az erőforrások áthelyezésére vonatkozó parancsokról az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](../move-resource-group-and-subscription.md)témakörben található.
