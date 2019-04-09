---
title: A portálról a meglévő hozzárendelések frissítése
description: További információ a mechanizmusa a portálról az Azure-tervek a meglévő hozzárendelések módosítása.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c75bd8c3831bad0c8217f16315843cbe3824fe4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276858"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Egy meglévő tervezet-hozzárendelés frissítése

A tervezet hozzárendelésekor a hozzárendelés lehet frissíteni. A meglévő hozzárendelések módosítása több okból is beleértve:

- Hozzáadása vagy eltávolítása [erőforrás zárolása](../concepts/resource-locking.md)
- Módosítsa az értéket a [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters)
- A hozzárendelés frissítsen egy újabb **közzétett** a tervezet verziója

## <a name="updating-assignments"></a>Hozzárendelések frissítése

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki **hozzárendelt tervezetek** a lap bal oldalán.

1. Tervezetek listájában bal kattintással a tervezet-hozzárendelést. Kattintson a **hozzárendelés frissítése** gombra, vagy kattintson a jobb gombbal a tervezet-hozzárendelést, és válassza ki **hozzárendelés frissítése**.

   ![Egy meglévő tervezet-hozzárendelés frissítése](../media/update-existing-assignments/update-assignment.png)

1. A **tervezet hozzárendelése** lap betöltése az eredeti hozzárendelés az összes értékeivel előre kitöltött lesz. Módosíthatja a **tervrajz-definíció verziója**, a **zárolási hozzárendelés** állapotot, és a tervezetdefiníciót a dinamikus paraméterek egyikét. Kattintson a **hozzárendelése** Amikor végzett a módosításokkal.

1. A frissített hozzárendelés részletei lapon tekintse meg az új állapot. Ebben a példában hozzáadtunk **zárolás** a hozzárendeléshez.

   ![Frissíti egy meglévő tervezet-hozzárendelést - zárolási mód változása](../media/update-existing-assignments/updated-assignment.png)

1. Részletes adatait más **hozzárendelési műveletek** használatával a listából. A tábla, **felügyelt erőforrások** frissítések által választott hozzárendelési műveletet.

   ![A tervezet-hozzárendelést a hozzárendelési műveletek](../media/update-existing-assignments/assignment-operations.png)

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

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).