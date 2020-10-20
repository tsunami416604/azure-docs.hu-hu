---
title: fájl belefoglalása
description: fájl belefoglalása
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026476"
---
## <a name="transform-data-before-using-large-usage-files"></a>Adatok átalakítása a nagy méretű használati fájlok használata előtt

Néha a használati vagy egyeztetési fájl túl nagy, ezért nem nyitható meg. Vagy előfordulhat, hogy csak az információ egy részére van szüksége a probléma megoldásához. Előfordulhat például, hogy csak egy adott erőforrás adatait, vagy csak néhány szolgáltatás vagy erőforráscsoport felhasználását szeretné megtekinteni. A kimutatástáblák létrehozása előtt átalakíthatja az adatokat az összegzésükhöz.

1. Nyisson meg egy üres munkafüzetet az Excelben.
1. A felső menüben válassza az **Adatok** > **Szövegből/CSV-fájlból** lehetőséget, majd válassza ki a használati fájlt, és kattintson az **Importálás** elemre.
1. Az ablak alján kattintson az **Adatok átalakítása** elemre. Egy új ablakban megjelenik az adatok összegzése.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Összegzett adatokat bemutató példa" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Ha Microsoft Ügyfélszerződéssel rendelkezik, hagyja ki ezt a lépést, és folytassa a következővel, mert az MCA használati fájljaiban általában az első sorban szerepelnek az oszlopcímek. Készítse elő az adatokat egy tábla létrehozásával. Távolítsa el a felső sorokat, csak a címeket hagyja meg. Válassza a **Sorok eltávolítása** > **Legfelső sorok eltávolítása** lehetőséget.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Összegzett adatokat bemutató példa" :::
1. A Legelső sorok eltávolítása ablakban írja be a felülről eltávolítandó sorok számát. Ez EA esetében általában 1, CSP esetében általában 2. Válassza az **OK** lehetőséget.
1. Válassza az **Első sor használata fejlécként** lehetőséget.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Összegzett adatokat bemutató példa" :::
    
    A táblázatnézetben az oszlopcímek felül jelennek meg.
1. Következő lépésként adjon hozzá szűrőt. A szűréshez használja az egyes oszlopok címének jobb oldalán található választó nyilakat. Javasolt szűrők: előfizetés azonosítója, szolgáltatás neve (mérési kategória), példány azonosítója, erőforráscsoport. Ugyanabban a dokumentumban több szűrőt is használhat. Azt javasoljuk, hogy a dokumentum méretének csökkentése és a későbbi munka megkönnyítése érdekében az összes lehetséges szűrőt alkalmazza.
1. A szűrők alkalmazása után válassza a **Bezárás és betöltés** lehetőséget.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Összegzett adatokat bemutató példa" :::

A fájl betölt, és megjelenik a szűrt használati adatokat tartalmazó táblázat. Most létrehozhat egy új kimutatástáblát a használattal kapcsolatos hibák elhárításához.