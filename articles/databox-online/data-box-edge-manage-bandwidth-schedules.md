---
title: Az Azure Data Box Edge sávszélesség-ütemezéseket kezel | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az Azure Portal sávszélesség-ütemezések az Azure Data Box Edge használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756847"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Az Azure-portál használatával kezelheti a sávszélesség-ütemezéseket az Azure Data Box Edge-en  

Ez a cikk ismerteti, hogyan kezelheti a felhasználókat az Azure Data Box Edge. A sávszélesség-ütemezéssel napi szinten szabályozható a hálózati sávszélesség felhasználása. Ezek az ütemezések az eszköz és a felhő közötti összes fel- és letöltési műveletre alkalmazhatók.

Hozzáadhatja, módosíthatja vagy törölheti a Data Box Edge sávszélesség-ütemezését az Azure Portalon keresztül.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Ütemezés hozzáadása
> * Ütemezés módosítása
> * Ütemezés törlése


## <a name="add-a-schedule"></a>Ütemezés hozzáadása

Ütemezés hozzáadásához kövesse az alábbi lépéseket az Azure Portalon.

1. Az Azure Portalon a Data Box Edge erőforrás, nyissa meg a **Sávszélesség**.
2. A jobb oldali ablaktáblában válassza az **Ütemezés hozzáadása lehetőséget.**

    ![Sávszélesség kiválasztása](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. Az **Ütemezés hozzáadása** területen: 

   1. Adja meg az ütemezés **Kezdő nap**, **Záró nap**, **Kezdés** és **Befejezés** paramétereinek értékeit.
   2. Jelölje be a **Nap** beállítás, ha ez az ütemezés egész nap fut.
   3. A **Sávszélesség** az eszköz által felhasznált sávszélesség Mb/s-ban kifejezett értéke a felhőműveletek (fel- és letöltés egyaránt) esetében. Adja meg a mezőhöz 20 és 1 000 000 007 közötti számot.
   4. Ha nem kívánja korlátozni a fel- és letöltéseket, válassza a **Korlátlan** sávszélesség-beállítást.
   5. Válassza a **Hozzáadás** lehetőséget.

      ![Ütemezés hozzáadása](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Ekkor létrejön egy ütemezés a megadott paraméterekkel. Az ütemezés ezután megjelenik a portálon is, a sávszélesség-ütemezések listájában.

    ![A sávszélesség-ütemezések frissített listája](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Ütemezés szerkesztése

A sávszélesség-ütemezéseket az alábbi lépesek végrehajtásával szerkesztheti.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, és nyissa meg a **Sávszélesség .in**the Azure Portal, go to Data Box Edge resource and then go to Bandwidth . 
2. A sávszélesség-ütemezések listájából válassza ki és jelölje ki a módosítani kívánt ütemezést.
    ![Sávszélesség-ütemezés kiválasztása](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Hajtsa végre és mentse a kívánt módosításokat.

    ![Felhasználó módosítása](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Az ütemezés módosítása után annak megfelelően frissül az ütemezések listája.

    ![Felhasználó módosítása](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Ütemezés törlése

A Data Box Edge-eszközhöz társított sávszélesség-ütemezés törléséhez tegye a következő lépéseket.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, és nyissa meg a **Sávszélesség .in**the Azure Portal, go to Data Box Edge resource and then go to Bandwidth .  

2. A sávszélesség-ütemezések listájában válassza ki a törölni kívánt ütemezést. A **Szerkesztés ütemezésében**válassza a **Törlés**lehetőséget. Amikor megerősítést kér, válassza az **Igen**lehetőséget.

   ![Felhasználó törlése](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Az ütemezés törlése után frissül az ütemezések listája.


## <a name="next-steps"></a>További lépések

- További információ a [megosztások kezeléséről](data-box-edge-manage-shares.md).
