---
title: Fájltartalom-módosítások megtekintése az Azure Automation segítségével
description: A módosításkövetés fájltartalom-módosítási funkciójával megtekintheti a módosított fájl tartalmát.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 23c4f24e430d58895eb551c3e2cb62b5f0003ac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75418810"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>A változások követésével nyomon követett fájl tartalmának megtekintése

A fájltartalom-követés lehetővé teszi a fájl tartalmának megtekintését a módosításkövetés lelt követése előtt és után. Ehhez minden módosítás után a fájl tartalmát egy tárfiókba menti.

## <a name="requirements"></a>Követelmények

* A fájltartalom tárolásához szabványos tárfiók ra van szükség az Erőforrás-kezelő központi telepítési modelljét használó szabványos tárfiókhoz. Prémium szintű és klasszikus üzembe helyezési modell tárfiókok at nem kell használni. A tárfiókokról az [Azure storage-fiókok – további](../storage/common/storage-create-storage-account.md) információ

* A használt tárfiókhoz csak 1 Automation-fiók csatlakoztatható.

* [A változáskövetés](automation-change-tracking.md) engedélyezve van az Automatizálási fiókban.

## <a name="enable-file-content-tracking"></a>Fájltartalom-követés engedélyezése

1. Az Azure Portalon nyissa meg az Automation-fiókot, és válassza **a Követés módosítása**lehetőséget.
2. A felső menüben válassza a **Beállítások szerkesztése parancsot.**
3. Válassza **a Fájltartalom lehetőséget,** és kattintson **a Hivatkozás gombra.** Ezzel megnyitja a **Tartalomhely hozzáadása a változáskövetéshez** ablaktáblát.

   ![Engedélyezi](./media/change-tracking-file-contents/enable.png)

4. Válassza ki azt az előfizetési és tárfiókot, amelynek a fájl tartalmát tárolni szeretné. Ha engedélyezni szeretné a fájltartalom-követést az összes meglévő nyomon követett fájlesetében, válassza **a Be** lehetőséget **a Fájltartalom feltöltéséhez az összes beállításhoz**. Ezt követően minden fájlelérési út esetében módosíthatja ezt.

   ![tárfiók beállítása](./media/change-tracking-file-contents/storage-account.png)

5. Ha engedélyezve van, a tárfiók és a SAS Uris jelennek meg. A SAS Uris 365 nap után lejár, és újra létrehozható a **Regenerate** gombra kattintva.

   ![fiókkulcsok listázása](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Fájl hozzáadása

A következő lépések végigvezetik a fájl módosítás-nyomon követésének bekapcsolásán:

1. A **Változások követés** **szerkesztése** lapon válassza a **Windows-fájlok** vagy **a Linux-fájlok** lapot, és kattintson a **Hozzáadás** gombra.

1. Töltse ki a fájl elérési útjának adatait, és válassza a **Fájltartalom feltöltése**csoport **Igaz csoportban** az összes beállításhoz lehetőséget. Ez a beállítás csak az adott fájlelérési úton engedélyezi a fájltartalom-követést.

   ![linux fájl hozzáadása](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Nyomon követett fájl tartalmának megtekintése

1. Ha a rendszer változást észlelt a fájlhoz vagy az elérési úthoz, az megjelenik a portálon. Jelölje ki a fájlmódosítást a módosítások listájából. Megjelenik a **Részletek módosítása** ablaktábla.

   ![lista módosításai](./media/change-tracking-file-contents/change-list.png)

1. A **Részletek módosítása** lapon a fájladatok előtti és utáni szabvány látható, a bal felső sarokban kattintson a **Fájltartalom-módosítások megtekintése** gombra a fájl tartalmának megtekintéséhez.

   ![részletek módosítása](./media/change-tracking-file-contents/change-details.png)

1. Az új lapon a fájl tartalma egymás mellett látható. A **Szövegközi** lehetőséget választva a módosítások szövegközi nézetét is megtekintheti.

   ![fájlmódosítások megtekintése](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>További lépések

Látogasson el a változáskövetés ről szóló oktatóanyagba, ha többet szeretne megtudni a megoldás használatáról:

> [!div class="nextstepaction"]
> [A környezet változásainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)

* Az [Azure Monitor naplóiban végzett naplókeresések](../log-analytics/log-analytics-log-searches.md) használatával részletes változáskövetési adatokat tekinthet meg.

