---
title: Azure Stack Edge GPU a sávszélesség-ütemtervek kezelése | Microsoft Docs
description: Ismerteti, hogyan használható a Azure Portal a sávszélesség-ütemtervek kezeléséhez a Azure Stack Edge GPU-ban.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: b27c4fb9beb631b40617307cfc5952b7dcb45369
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147048"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge"></a>A Azure Portal használatával kezelheti a sávszélesség-ütemterveket a Azure Stack Edge-ben 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez a cikk bemutatja, hogyan kezelheti a sávszélesség-ütemterveket az Azure Stack Edge-ben. A sávszélesség-ütemezéssel napi szinten szabályozható a hálózati sávszélesség felhasználása. Ezek az ütemezések az eszköz és a felhő közötti összes fel- és letöltési műveletre alkalmazhatók.

A Azure Portal használatával hozzáadhat, módosíthat vagy törölhet Azure Stack Edge sávszélesség-menetrendjét.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Ütemezés hozzáadása
> * Ütemezés módosítása
> * Ütemezés törlése


## <a name="add-a-schedule"></a>Ütemezés hozzáadása

Az ütemterv hozzáadásához hajtsa végre az alábbi lépéseket a Azure Portalben.

1. A Azure Stack Edge-erőforrás Azure Portal válassza a **sávszélesség**lehetőséget.
2. A jobb oldali ablaktáblában válassza az **+ ütemterv hozzáadása**elemet.

    ![Sávszélesség kiválasztása](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. Az **Ütemezés hozzáadása** területen: 

   1. Adja meg az ütemezés **Kezdő nap**, **Záró nap**, **Kezdés** és **Befejezés** paramétereinek értékeit.
   2. Ellenőrizze az **egész nap** beállítást, ha az adott ütemtervnek egész nap kell futnia.
   3. A **Sávszélesség** az eszköz által felhasznált sávszélesség Mb/s-ban kifejezett értéke a felhőműveletek (fel- és letöltés egyaránt) esetében. Ehhez a mezőhöz 20 és 2 147 483 647 közötti számot adjon meg.
   4. Ha nem kívánja korlátozni a fel- és letöltéseket, válassza a **Korlátlan** sávszélesség-beállítást.
   5. Válassza a **Hozzáadás** lehetőséget.

      ![Ütemterv hozzáadása](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. Ekkor létrejön egy ütemezés a megadott paraméterekkel. Az ütemezés ezután megjelenik a portálon is, a sávszélesség-ütemezések listájában.

    ![A sávszélesség-ütemtervek frissített listája](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Ütemezés szerkesztése

A sávszélesség-ütemezéseket az alábbi lépesek végrehajtásával szerkesztheti.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza a **sávszélesség**lehetőséget. 
2. A sávszélesség-ütemtervek listájából válassza ki a módosítani kívánt ütemtervet.
    ![Sávszélesség-ütemterv kiválasztása](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. Hajtsa végre és mentse a kívánt módosításokat.

    ![Felhasználó módosítása](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. Az ütemezés módosítása után annak megfelelően frissül az ütemezések listája.

    ![Felhasználó módosítása](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Ütemezés törlése

Az Azure Stack Edge-eszközhöz társított sávszélesség-ütemterv törléséhez hajtsa végre a következő lépéseket.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza a **sávszélesség**lehetőséget.  

2. A sávszélesség-ütemezések listájában válassza ki a törölni kívánt ütemezést. Az **ütemterv szerkesztése**területen válassza a **Törlés**lehetőséget. Ha a rendszer megerősítést kér, válassza az **Igen**lehetőséget.

   ![Felhasználó törlése](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. Az ütemezés törlése után frissül az ütemezések listája.


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [kezelheti a megosztásokat](azure-stack-edge-j-series-manage-shares.md).
