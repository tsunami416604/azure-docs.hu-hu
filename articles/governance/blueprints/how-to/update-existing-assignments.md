---
title: Egy meglévő Azure tervezet-hozzárendelés frissítése
description: További információ az Azure-tervek a meglévő hozzárendelések módosítása a mechanizmust.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956200"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Egy meglévő tervezet-hozzárendelés frissítése

A tervezet hozzárendelésekor a hozzárendelés lehet frissíteni. A meglévő hozzárendelések módosítása több okból is beleértve:

- Hozzáadása vagy eltávolítása [erőforrás zárolása](../concepts/resource-locking.md)
- Módosítsa az értéket a [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters)
- A hozzárendelés frissítsen egy újabb **közzétett** a tervezet verziója

## <a name="updating-assignments"></a>Hozzárendelések frissítése

1. Indítsa el az Azure-tervek szolgáltatás az Azure Portalon kattintson a **minden szolgáltatás** keresése és kiválasztása **házirend** a bal oldali panelen. Az a **házirend** lapon **tervezetek**.

1. Válassza ki **hozzárendelt tervezetek** a lap bal oldalán.

1. Tervezetek listájában kattintson a bal gombbal a tervezet-hozzárendelést, és kattintson a **frissítési hozzárendelés** gombra, vagy kattintson a jobb gombbal a tervezet-hozzárendelést, és válassza ki **frissítési hozzárendelés**.

   ![Frissítési hozzárendelés](../media/update-existing-assignments/update-assignment.png)

1. A **tervezet hozzárendelése** lap betöltése az eredeti hozzárendelés az összes értékeivel előre kitöltött lesz. Módosíthatja a **tervrajz-definíció verziója**, a **zárolási hozzárendelés** állapotot, és a tervezetdefiníciót a dinamikus paraméterek egyikét. Kattintson a **hozzárendelése** Amikor végzett a módosításokkal.

1. A frissített hozzárendelés részletei lapon tekintse meg az új állapot. Ebben a példában hozzáadtunk **zárolás** a hozzárendeléshez.

   ![Frissített hozzárendelés - zárolva van](../media/update-existing-assignments/updated-assignment.png)

1. Részletes adatait más **hozzárendelési műveletek** használatával a listából. A tábla, **felügyelt erőforrások** frissítések által választott hozzárendelési műveletet.

   ![Hozzárendelési műveletek](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Szabályok hozzárendeléseinek frissítése folyamatban van

A központi telepítés a frissített hozzárendelések néhány fontos szabályokat követi. Ezek a szabályok határozzák meg, mi történik, a kért változtatás és összetevő erőforrás folyamatban típusától függően egy meglévő erőforrásnak telepíteni vagy frissíteni.

- Szerepkör-hozzárendelések
  - Ha a szerepkör vagy a szerepkör assignee (felhasználó, csoport vagy alkalmazás) megváltozik, akkor egy új szerepkör-hozzárendelés jön létre. A korábban telepített szerepkör-hozzárendelést a helyén marad.
- Szabályzat-hozzárendelések
  - Ha módosítják a szabályzat-hozzárendelés paramétereit, a meglévő hozzárendelést frissül.
  - Ha módosítják a szabályzat-hozzárendelés definíciója, létrejön egy új szabályzat-hozzárendelést. A korábban telepített szabályzat-hozzárendelés a helyén marad.
  - Ha a házirend-hozzárendelési összetevője a tervezet eltávolítják, a korábban telepített szabályzat-hozzárendelés a helyén marad.
- Azure Resource Manager-sablonok
  - A sablon Resource Managerrel, dolgoz fel egy **PUT**. Szerint egyes erőforrástípusok ez másképp kezeli, tekintse át az egyes belefoglalt erőforrást annak megállapításához, mikor fusson a tervek szerint ez a művelet hatása a dokumentációban.

## <a name="possible-errors-on-updating-assignments"></a>Hozzárendeléseinek frissítése folyamatban van a lehetséges hibák

Hozzárendelések frissítése esetén lehetséges szünet, ha a végrehajtott módosításokat. Ilyen például a már telepítése után megváltoztatja az erőforráscsoport helyét. Minden olyan változás által támogatott [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) arról is, de bármelyik módosíthatja, hogy lenne az Azure Resource Manager hibát eredményez is eredményez a hozzárendelés sikertelen.

Nincs korlátozva a hozzárendelés egy hány alkalommal frissíthetők. Így ha hiba lép fel, vagy egy hibás paramétert, már meglévő objektum vagy egy módosítása nem engedélyezett az Azure Resource Manager miatt határozza meg a hiba, és győződjön meg arról, egy másik frissítés, a hozzárendeléshez.

## <a name="next-steps"></a>További lépések

- További információ a [tervezetet életciklus](../concepts/lifecycle.md)
- Megtudhatja, hogyan használja [statikus és dinamikus paraméterek](../concepts/parameters.md)
- Ismerje meg, szabhatja testre a [tervezetet alkalmazás-előkészítés sorrend](../concepts/sequencing-order.md)
- Ismerje meg, győződjön meg arról, hogyan használhatja az [tervezetet erőforrás zárolása](../concepts/resource-locking.md)
- A tervrajz hozzárendelésének során felmerülő problémák megoldása [általános hibaelhárítási](../troubleshoot/general.md)
