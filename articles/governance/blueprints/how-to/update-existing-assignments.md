---
title: Meglévő hozzárendelés frissítése a portálról
description: Ismerje meg a meglévő tervezet-hozzárendelés frissítésének mechanizmusát az Azure Blueprints portálról.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381790"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Meglévő tervhozzárendelés frissítése

Tervrajz hozzárendelésekor a hozzárendelés frissíthető. Egy meglévő hozzárendelés frissítésének több oka is van, például:

- [Erőforrás-zárolás](../concepts/resource-locking.md) hozzáadása vagy eltávolítása
- Dinamikus [paraméterek](../concepts/parameters.md#dynamic-parameters) értékének módosítása
- A hozzárendelés frissítése a terv terv újabb **közzétett** verziójára

## <a name="updating-assignments"></a>Hozzárendelések frissítése

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali lapon válassza a **Hozzárendelt tervrajzok lehetőséget.**

1. A tervrajzok listájában kattintson a bal gombbal a társrajz-hozzárendelésre. Ezután kattintson a **Hozzárendelés frissítése** gombra vagy kattintson a jobb gombbal a társítási hozzárendelésre, és válassza a Hozzárendelés **frissítése parancsot**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Meglévő tervhozzárendelés frissítése" border="false":::

1. A **Blueprint hozzárendelése** lap előre kitöltve töltődik be az eredeti hozzárendelés összes értékével.
   Módosíthatja a **tervezet definíciós verziója,** a **Lock Hozzárendelés** állapot, és a dinamikus paraméterek, amelyek léteznek a tervezet definícióját. Ha végzett a módosításokkal, kattintson a **Hozzárendelés** gombra.

1. A frissített hozzárendelés részletei lapon tekintse meg az új állapotot. Ebben a példában hozzáadtuk a **zárolást** a hozzárendeléshez.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Meglévő tervezethozzárendelés frissítve – a zárolási mód megváltozott" border="false":::

1. Fedezze fel a többi **hozzárendelési művelet** részleteit a legördülő menühasználatával. A **Felügyelt erőforrások** táblája a kijelölt hozzárendelési művelet szerint frissül.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Tervhozzárendelési műveletek" border="false":::

## <a name="rules-for-updating-assignments"></a>A hozzárendelések frissítésének szabályai

A frissített hozzárendelések telepítése néhány fontos szabályt követ. Ezek a szabályok határozzák meg, hogy mi történik a már üzembe helyezett erőforrásokkal. A kért módosítás és a telepített vagy frissített műtermék-erőforrás típusa határozza meg, hogy mely műveletek et hajtanak végre.

- Szerepkör-hozzárendelések
  - Ha a szerepkör vagy a szerepkör-megbízott (felhasználó, csoport vagy alkalmazás) megváltozik, új szerepkör-hozzárendelés jön létre. A korábban telepített szerepkör-hozzárendelések a helyükön maradnak.
- Szabályzat-hozzárendelések
  - Ha a házirend-hozzárendelés paraméterei megváltoznak, a meglévő hozzárendelés frissül.
  - Ha a házirend-hozzárendelés definíciója megváltozik, új házirend-hozzárendelés jön létre.
    A korábban telepített házirend-hozzárendelések a helyükön maradnak.
  - Ha a házirend-hozzárendelés imågåttól távolodik a tervtervből, az üzembe helyezett házirend-hozzárendelések a helyükön maradnak.
- Azure Resource Manager-sablonok
  - A sablon feldolgozása az Erőforrás-kezelőn keresztül **PUT-ként történik.** Mivel az egyes erőforrástípusok eltérően kezelik ezt a műveletet, tekintse át az egyes beépített erőforrások dokumentációját, és határozza meg a művelet hatását a tervrajzok futtatásakor.

## <a name="possible-errors-on-updating-assignments"></a>A hozzárendelések frissítésével kapcsolatos lehetséges hibák

A hozzárendelések frissítésekor olyan módosításokat végezhet, amelyek végrehajtáskor megszakadnak. Egy példa egy erőforráscsoport helyének módosítása, miután már üzembe helyezte. Az Azure Resource [Manager](../../../azure-resource-manager/management/overview.md) által támogatott módosítások elhajthatók, de az Azure Resource Manager által hiba esetén végrehajtott módosítások a hozzárendelés hibáját is eredményezik.

Nincs korlátozva, hogy egy hozzárendelés hányszor frissíthető. Hiba esetén állapítsa meg a hibát, és frissítse a hozzárendelést.  Példa hibaesetekre:

- Hibás paraméter
- Már létező objektum
- Az Azure Resource Manager által nem támogatott módosítás

## <a name="next-steps"></a>További lépések

- További információ a [tervterv életciklusáról.](../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../concepts/resource-locking.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.