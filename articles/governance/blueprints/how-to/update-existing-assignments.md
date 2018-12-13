---
title: A meglévő hozzárendelések frissítése
description: További információ az Azure-tervek a meglévő hozzárendelések módosítása a mechanizmust.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ada0ccc1286677300c005da4fa0e92be94a3ca8
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314829"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Egy meglévő tervezet-hozzárendelés frissítése

A tervezet hozzárendelésekor a hozzárendelés lehet frissíteni. A meglévő hozzárendelések módosítása több okból is beleértve:

- Hozzáadása vagy eltávolítása [erőforrás zárolása](../concepts/resource-locking.md)
- Módosítsa az értéket a [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters)
- A hozzárendelés frissítsen egy újabb **közzétett** a tervezet verziója

## <a name="updating-assignments"></a>Hozzárendelések frissítése

1. Kattintson a **Minden szolgáltatás** elemre, és keresse meg, majd válassza ki a **Szabályzat** elemet a bal oldali panelen. Kattintson a **Szabályzat** oldal **Tervek** elemére.

1. Válassza a **Hozzárendelt tervek** lehetőséget a lap bal oldalán.

1. Tervezetek listájában bal kattintással a tervezet-hozzárendelést. Kattintson a **frissítési hozzárendelés** gombra, vagy kattintson a jobb gombbal a tervezet-hozzárendelést, és válassza ki **frissítési hozzárendelés**.

   ![Hozzárendelés frissítése](../media/update-existing-assignments/update-assignment.png)

1. A **tervezet hozzárendelése** lap betöltése az eredeti hozzárendelés az összes értékeivel előre kitöltött lesz. Módosíthatja a **tervrajz-definíció verziója**, a **zárolási hozzárendelés** állapotot, és a tervezetdefiníciót a dinamikus paraméterek egyikét. Kattintson a **hozzárendelése** Amikor végzett a módosításokkal.

1. A frissített hozzárendelés részletei lapon tekintse meg az új állapot. Ebben a példában hozzáadtunk **zárolás** a hozzárendeléshez.

   ![Frissített hozzárendelés - zárolva van](../media/update-existing-assignments/updated-assignment.png)

1. Részletes adatait más **hozzárendelési műveletek** használatával a listából. A tábla, **felügyelt erőforrások** frissítések által választott hozzárendelési műveletet.

   ![Hozzárendelési műveletek](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Szabályok hozzárendeléseinek frissítése folyamatban van

A központi telepítés a frissített hozzárendelések néhány fontos szabályokat követi. Ezek a szabályok határozzák meg, mi történik, a már telepített erőforrásokhoz. A kért változtatás és a telepített vagy a frissített összetevő erőforrás típusa, határozza meg, hogy mely műveleteket hajtja végre.

- Szerepkör-hozzárendelések
  - Ha a szerepkör vagy a szerepkör assignee (felhasználó, csoport vagy alkalmazás) megváltozik, akkor egy új szerepkör-hozzárendelés jön létre. Szerepkör-hozzárendelések korábban már telepítette a helyükön maradnak.
- Szabályzat-hozzárendelések
  - Ha módosítják a szabályzat-hozzárendelés paramétereit, a meglévő hozzárendelést frissül.
  - Ha a szabályzat-hozzárendelés definíciója megváltozott, létrejön egy új szabályzat-hozzárendelés. Szabályzat-hozzárendelések korábban már telepítette a helyükön maradnak.
  - Ha a házirend-hozzárendelési összetevője a tervezet eltávolítják, telepített szabályzat-hozzárendelések a helyükön maradnak.
- Azure Resource Manager-sablonok
  - A sablon Resource Managerrel, dolgoz fel egy **PUT**. Egyes erőforrástípusok másképp kezeli ezt a műveletet, mert tekintse át az egyes belefoglalt erőforrást annak megállapításához, mikor fusson a tervek szerint ez a művelet hatása a dokumentációban.

## <a name="possible-errors-on-updating-assignments"></a>Hozzárendeléseinek frissítése folyamatban van a lehetséges hibák

Hozzárendelések frissítése esetén lehetséges szünet, ha a végrehajtott módosításokat. Példa már telepítése után megváltoztatja az erőforráscsoport helyét. Minden olyan változás által támogatott [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) arról is, de bármelyik módosíthatja, hogy lenne az Azure Resource Manager hibát eredményez is eredményez a hozzárendelés sikertelen.

Nincs korlátozva a hozzárendelés egy hány alkalommal frissíthetők. Hiba esetén határozza meg a hiba, és győződjön meg arról, egy másik frissítés, a hozzárendeléshez.  Példaforgatókönyvek hiba:

- Hibás paraméter
- Egy már meglévő objektum
- Nem támogatott az Azure Resource Manager által módosítása

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md)
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md)
