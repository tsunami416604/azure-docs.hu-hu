---
title: Modell- és tanulási beállítások kezelése - Személyre szabás
description: A gép megtanult modell és a tanulási beállítások exportálhatók biztonsági mentéshez a saját forrásvezérlő rendszerében.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624296"
---
# <a name="how-to-manage-model-and-learning-settings"></a>A modell- és tanulási beállítások kezelése

A gép megtanult modell és a tanulási beállítások exportálhatók biztonsági mentéshez a saját forrásvezérlő rendszerében.

## <a name="export-the-personalizer-model"></a>A Personalizer modell exportálása

Az Erőforrás-kezelés a **modell és a tanulási beállítások**szakaszban tekintse át a modell létrehozását és az utolsó frissített dátumot, és exportálja az aktuális modellt. Az Azure Portalon vagy a Personalizer API-k használatával exportálhat egy modellfájlt archiválási célokra.

![Jelenlegi Personalizer modell exportálása](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Adatok törlése a tanulási ciklushoz

1. Az Azure Portalon a Personalizer erőforráshoz a **Modell és a tanulási beállítások** lapon válassza az Adatok törlése **lehetőséget.**
1. Az összes adat törléséhez és a tanulási ciklus eredeti állapotba állításához jelölje be mind a 3 jelölőnégyzetet.

    ![Az Azure Portalon törölje az adatokat a Personalizer erőforrásból.](./media/settings/clear-data-from-personalizer-resource.png)

    |Érték|Cél|
    |--|--|
    |Naplózott személyre szabási és jutalomadatok.|Ezeket a naplózási adatokat offline kiértékelések használják. Törölje az adatokat, ha alaphelyzetbe állítja az erőforrást.|
    |Állítsa alaphelyzetbe a Personalizer modellt.|Ez a modell minden átképzésnél megváltozik. Ez a betanítási gyakoriság a **konfigurációs** oldalon a **feltöltési modell gyakoriságában** van megadva. |
    |Állítsa be a tanulási szabályzatot alapértelmezettre.|Ha egy offline értékelés részeként módosította a tanulási szabályzatot, ez visszaáll az eredeti tanulási szabályzatra.|

1. A **törlési** folyamat megkezdéséhez jelölje be a Kijelölt adatok törlése jelölőnégyzetet. Az állapot az Azure-értesítésekben, a jobb felső navigációs sávon jelent meg.

## <a name="import-a-new-learning-policy"></a>Új tanulási szabályzat importálása

A [tanulási házirend-beállítások](concept-active-learning.md#understand-learning-policy-settings) határozzák meg a modell betanításának _hiperparamétereit._ Végezzen [offline értékelést](how-to-offline-evaluation.md) az új tanulási szabályzat megkereséséhez.

1. Nyissa meg az [Azure Portalon](https://portal.azure.com), és válassza ki a Personalizer erőforrást.
1. Válassza a **Modell és a tanulási beállítások lehetőséget** az **Erőforrás-kezelés** szakaszban.
1. A **Tanulási beállítások importálása esetén** jelölje ki a fent megadott JSON formátummal létrehozott fájlt, majd kattintson a **Feltöltés gombra.**

    Várja meg az értesítést, hogy a tanulási szabályzat feltöltése sikeresen megtörtént.

## <a name="export-a-learning-policy"></a>Tanulási szabályzat exportálása

1. Nyissa meg az [Azure Portalon](https://portal.azure.com), és válassza ki a Personalizer erőforrást.
1. Válassza a **Modell és a tanulási beállítások lehetőséget** az **Erőforrás-kezelés** szakaszban.
1. A **Tanulási beállítások importálása** lapon válassza a **Tanulási beállítások exportálása** gombot. Ezzel menti `json` a fájlt a helyi számítógépre.

## <a name="next-steps"></a>További lépések

[További információ a tanulási szabályzat kezeléséről](how-to-manage-model.md)
