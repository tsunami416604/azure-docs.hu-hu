---
title: Az Azure Resource Manager erőforráscsoport törlése
description: Ismerteti, hogyan Azure Resource Manager orders-e az erőforrások törlését Ha töröl egy erőforráscsoportot.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: tomfitz
ms.openlocfilehash: 8b0711cab07584aa84ab437a2a4efb5aab92f3d1
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319357"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Az Azure Resource Manager erőforrás-csoport törlése

Ez a cikk bemutatja, hogyan Azure Resource Manager orders-e az erőforrások törlését egy erőforráscsoport törlésekor.

## <a name="determine-order-of-deletion"></a>Törlés sorrendjének meghatározása

Ha töröl egy erőforráscsoportot, a Resource Manager ahhoz, hogy törölje az erőforrást a határozza meg. Használja a következő sorrendben:

1. Az összes gyermek (beágyazott) erőforrások törlődnek.

2. Erőforrások, amelyek egyéb erőforrások kezelése a következő törlődnek. Erőforrás rendelkezhet a `managedBy` tulajdonsága azt jelzik, hogy egy másik erőforrás kezeli azt. Ha ez a tulajdonság értéke, az erőforrást, amely kezeli a többi erőforrás törlődik, mielőtt a többi erőforrást.

3. A további erőforrások az előző két kategóriába után törlődnek.

## <a name="resource-deletion"></a>Erőforrás törlése

A sorrend határozza meg, miután a Resource Manager problémák minden erőforrás egy törlési művelet. A folytatás előtt befejezéséhez függőségek vár.

A szinkron műveletek esetében a sikeres válasz várt kódok a következők:

* 200
* 204
* 404

Az aszinkron műveletek a várt a sikeres válasz 202. Erőforrás-kezelő nyomon követi a location fejlécet, vagy az azure-aszinkron művelet fejlécére az aszinkron törlési művelet állapotának megállapítása.
  
### <a name="errors"></a>Hibák

Egy törlési művelet hibát ad vissza, ha a Resource Manager újrapróbálkozik a törlési hívásban. Az újrapróbálkozások az 5xx, 429-es és 408 állapotkódok fordulhat elő. Alapértelmezés szerint az adott időszakban újra beállítás 15 perc.

## <a name="after-deletion"></a>Törlés után

Erőforrás-kezelő kiad egy GET hívást végrehajtó erőforrásokra helyezett próbált meg törölni. A válasz a GET-hívás várhatóan 404-es. Ha a Resource Manager lekérdezi a 404-es, úgy ítéli meg, a törlés sikeresen befejeződött. Resource Manager az erőforrás eltávolítja a gyorsítótárból.

Azonban az erőforráson a GET-hívás 200-as vagy a 201-es adja vissza, ha a Resource Manager az erőforrást újra létrehozza.

### <a name="errors"></a>Hibák

A GET-művelet hibát ad vissza, ha a Resource Manager újrapróbálkozik a GET esetében a következő hibakóddal:

* 100-nál kisebb
* 408
* 429
* Nagyobb, mint 500

Más hibakódok erőforrás-kezelő nem az erőforrás törlését.

## <a name="next-steps"></a>További lépések

* Erőforrás-kezelő kapcsolatos fogalmak megismeréséhez tekintse meg [Azure Resource Manager áttekintése](resource-group-overview.md).
* A műveletek esetében egy erőforrás-szolgáltató megtekintése: [Azure REST API](/rest/api/).
