---
title: A lemezmetrikák e-ei az Azure Portalon | Microsoft dokumentumok
description: Ismerje meg, hogy mely lemezmetrikák elavultak, és hogyan frissítheti a metrikariasztásokat az új metrikák használatához.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299802"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>A lemezmetrikák e-eiaz Azure Portalon

Az elavult lemezzel kapcsolatos metrikák hamarosan törlődnek az Azure Portalról. Minden elavult metrika új verziója áll rendelkezésre. Ez a cikk bemutatja, hogy mely metrikák újak, és hogyan frissítheti a metrikariasztásokat azok használatához.

## <a name="list-of-new-metrics"></a>Az új mutatók listája

Ez a táblázat leképezi az egyes elavult metrika a megfelelő új metrika. 

|Elavult mutató|Új (csere) mérőszám|
|----|----|
|Adatlemez –qd (elavult)|Adatlemez-várólista mélysége (előzetes verzió)|
|Adatlemez olvasási bájt/mp (elavult)|Adatlemez olvasási bájtjai/mp (előzetes verzió)|
|Adatlemez olvasási műveletei/mp (elavult)|Adatlemez olvasási műveletei/mp (előzetes verzió)|
|Adatlemez írási bájt/mp (elavult)|Adatlemez írási bájtjai/mp (előzetes verzió)|
|Adatlemez írási műveletei/mp(elavult)|Adatlemez írási műveletei/mp (előzetes verzió)|
|Operációs rendszer qd (elavult)|Az operációs rendszer várólistájának mélysége (előzetes verzió)|
|Operációs rendszer olvasási bájtjai/mp(elavult)|Operációs rendszer olvasási bájtjai/mp (előzetes verzió)|
|Operációs rendszer olvasási műveletei/mp-es (elavult)|Operációs rendszer olvasási műveletei/mp (előzetes verzió)|
|Operációs rendszer írási bájtjai/mp (elavult)|Operációs rendszer írási bájtjai/mp (előzetes verzió)|
|Operációs rendszer írási műveletei/mp-es (elavult)|Operációs rendszer írási műveletei/mp (előzetes verzió)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Mérőszámok áttelepítése a metrikariasztásokban

Frissítse a metrikariasztásokat az új mérőszámok használatához.

1. Az Azure Portalon keressen **riasztásokat.** Ezután a **Szolgáltatások** csoportban válassza a **Riasztások**lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. A **Riasztások** lapon válassza a **Riasztási szabályok kezelése** gombot. 

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Az **Erőforráscsoport** legördülő listában jelölje be a **Virtuális gépek** jelölőnégyzetet, és a **Jeltípus** legördülő listában jelölje be a **Metrikák jelölőnégyzetet.** 

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. A metrikák listájában azonosítsa a lemezekhez kapcsolódó feltételeket. Kattintson a szabály nevére. 

   A név hivatkozásként jelenik meg a tábla **Név** oszlopában.

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. A **Szabályok kezelése** lap **Feltételek** szakaszában kattintson a riasztás állapotára. 

   A feltétel hivatkozásként jelenik meg.  

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Megjelenik **a Jellogika konfigurálása** lap, és a feltétel beállításai az adott lap **Riasztáslogika** szakaszában jelennek meg.

6. Rögzítse ezeket a beállításokat, mert azok eltűnnek, amikor eltávolítja az elavult metrikát.

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Érdemes lehet ezeket a beállításokat képernyőképen vagy szöveges fájlban rögzíteni. 

7. Kattintson a **Vissza a jelhez választó** hivatkozásra.

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. A **Jellogika konfigurálása** lapon válassza ki a megfelelő helyettesítő metrikát (új metrika). A cikk korábbi részében megjelenő [táblával](#update-metrics) azonosá bislonizálja az új metrika nevét.

   > [!TIP] 
   > Kezdjen el gépelni a keresősávba a metrikanevek listájának szűkítéséhez. 

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Válassza a **Kész** gombot. 

   > [!div class="mx-imgBorder"]
   > ![Kép leírása](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. A **Mentés** gombra kattintva véglegesítheti a módosításokat. 

    > [!div class="mx-imgBorder"]
    > ![Kép leírása](./media/portal-disk-metrics-deprecation/save-new-metric.png)






