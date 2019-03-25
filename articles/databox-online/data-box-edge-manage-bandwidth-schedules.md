---
title: Az Azure Data Box Edge sávszélesség ütemezések kezelése |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure Data Box Edge sávszélesség ütemezések kezelése az Azure portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f88003e38a34eb3396b83158ff71e4739080cd9d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401409"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Az Azure Data Box Edge sávszélesség ütemezések kezelése az Azure portal használatával  

Ez a cikk ismerteti, hogyan kezelheti a felhasználókat az Azure Data Box Edge. A sávszélesség-ütemezéssel napi szinten szabályozható a hálózati sávszélesség felhasználása. Ezek az ütemezések az eszköz és a felhő közötti összes fel- és letöltési műveletre alkalmazhatók.

Hozzáadhatja, módosíthatja, vagy a sávszélesség ütemezések törlése a Data Box Edge az Azure Portalon keresztül.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Ütemezés hozzáadása
> * Ütemezés módosítása
> * Ütemezés törlése


## <a name="add-a-schedule"></a>Ütemezés hozzáadása

Kövesse az alábbi lépéseket egy ütemezés hozzáadása az Azure Portalon.

1. A Data Box Edge erőforráshoz, az Azure Portalon lépjen a **sávszélesség**.
2. A jobb oldali ablaktáblában válassza **+ Hozzáadás ütemezés**.

    ![Válassza ki a sávszélesség](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. Az **Ütemezés hozzáadása** területen: 

   1. Adja meg az ütemezés **Kezdő nap**, **Záró nap**, **Kezdés** és **Befejezés** paramétereinek értékeit.
   2. Ellenőrizze a **egész nap** beállítást, ha az ütemezés kell futtatni minden nap.
   3. A **Sávszélesség** az eszköz által felhasznált sávszélesség Mb/s-ban kifejezett értéke a felhőműveletek (fel- és letöltés egyaránt) esetében. Adja meg a 20 és a mező 1,000,000,007 közötti szám.
   4. Ha nem kívánja korlátozni a fel- és letöltéseket, válassza a **Korlátlan** sávszélesség-beállítást.
   5. Válassza a **Hozzáadás** lehetőséget.

      ![Ütemezés hozzáadása](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Ekkor létrejön egy ütemezés a megadott paraméterekkel. Az ütemezés ezután megjelenik a portálon is, a sávszélesség-ütemezések listájában.

    ![A sávszélesség ütemezések frissített listája](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Ütemezés szerkesztése

A sávszélesség-ütemezéseket az alábbi lépesek végrehajtásával szerkesztheti.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **sávszélesség**. 
2. Sávszélesség ütemezések listájában válassza ki, és válasszon ki egy ütemezést, amelyet módosítani szeretne.
    ![Válassza ki a sávszélesség-ütemezés](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Hajtsa végre és mentse a kívánt módosításokat.

    ![Felhasználó módosítása](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Az ütemezés módosítása után annak megfelelően frissül az ütemezések listája.

    ![Felhasználó módosítása](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Ütemezés törlése

A következő lépések a Data Box Edge-eszköz társított sávszélesség ütemezés törlése.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **sávszélesség**.  

2. A sávszélesség-ütemezések listájában válassza ki a törölni kívánt ütemezést. Az a **ütemezés szerkesztése**válassza **törlése**. Amikor a rendszer megerősítést kér, válassza ki a **Igen**.

   ![Felhasználó törlése](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Az ütemezés törlése után frissül az ütemezések listája.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [megosztások kezelése](data-box-edge-manage-shares.md).
