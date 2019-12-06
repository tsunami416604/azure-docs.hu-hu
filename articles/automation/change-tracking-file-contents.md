---
title: A fájl tartalmának változásainak megtekintése Azure Automation
description: A Change Tracking file Content Change funkciójának használatával megtekintheti egy módosított fájl tartalmát.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ab88aa2dc604172f00d875353dabba61fd101af
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850584"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Change Tracking által nyomon követett fájl tartalmának megtekintése

A fájl tartalmának nyomon követése lehetővé teszi egy fájl tartalmának megtekintését a Change Tracking követésével követett változtatások előtt és után. Ennek elvégzéséhez a fájl tartalmát egy Storage-fiókba menti, miután minden módosítás bekövetkezik.

## <a name="requirements"></a>Követelmények

* A fájl tartalmának tárolásához a Resource Manager-alapú üzemi modellt használó standard Storage-fiók szükséges. A prémium és a klasszikus üzembe helyezési modell Storage-fiókjait nem szabad használni. További információ a Storage-fiókokról: [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-create-storage-account.md)

* A használt Storage-fióknak csak 1 Automation-fiókja lehet csatlakoztatva.

* [Change Tracking](automation-change-tracking.md) engedélyezve van az Automation-fiókjában.

## <a name="enable-file-content-tracking"></a>Fájl tartalmának követésének engedélyezése

1. A Azure Portal nyissa meg Automation-fiókját, majd válassza a **change Tracking (változások nyomon követése**) lehetőséget.
2. A felső menüben válassza a **beállítások szerkesztése**lehetőséget.
3. Válassza a **fájl tartalma** lehetőséget, majd kattintson a **hivatkozás**gombra. Ekkor megnyílik a **tartalom hozzáadása hely Change Tracking** ablaktáblán.

   ![engedélyezés](./media/change-tracking-file-contents/enable.png)

4. Válassza ki azt az előfizetést és a Storage-fiókot, amelyet a fájl tartalmának tárolására kíván használni. Ha engedélyezni szeretné a fájl tartalmának nyomon követését az összes meglévő követett fájlnál, válassza **a be** lehetőséget a **fájl tartalmának feltöltéséhez az összes beállításnál**. Ezt később is megváltoztathatja az egyes fájlok elérési útjára.

   ![Storage-fiók beállítása](./media/change-tracking-file-contents/storage-account.png)

5. Ha engedélyezve van, a Storage-fiók és a SAS URI-k láthatók. Az SAS URI-k 365 nap után lejárnak, és **az újbóli Létrehozás gombra kattintva** újra létrehozhatók.

   ![fiók kulcsainak listázása](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Fájl hozzáadása

A következő lépések végigvezetik a fájlok változás-követésének bekapcsolásán:

1. A Change Tracking **beállítások szerkesztése** lapján válasszaa **Windows-fájlok** vagy a **Linux-fájlok** fület, majd kattintson a **Hozzáadás** gombra.

1. Adja meg a fájl elérési útjának adatait, és válassza az **igaz** értéket a **fájl tartalmának feltöltése az összes beállításnál**. Ezzel a beállítással engedélyezhető a fájl tartalmának követése a fájl elérési útjához.

   ![Linux-fájl hozzáadása](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Egy követett fájl tartalmának megtekintése

1. Miután észlelte a fájl vagy az elérési út egy fájljának változását, az a portálon jelenik meg. A módosítások listájában válassza a fájl módosítása lehetőséget. Megjelenik a **változás részletei** ablaktábla.

   ![változások listázása](./media/change-tracking-file-contents/change-list.png)

1. A **change Details (részletek módosítása** ) lapon megtekintheti a fájlok előtt és után megjelenő szabványos adatokat, a bal felső sarokban kattintson a **fájl tartalmának módosítása** elemre a fájl tartalmának megtekintéséhez.

   ![Változás részletei](./media/change-tracking-file-contents/change-details.png)

1. Az új lapon a fájl tartalmait jelenítheti meg egymás melletti nézetben. A **beágyazott** nézetet is kiválaszthatja a módosítások beágyazott nézetének megtekintéséhez.

   ![fájl változásainak megtekintése](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Következő lépések

A megoldás használatával kapcsolatos további információkért tekintse meg az Change Tracking szóló oktatóanyagot:

> [!div class="nextstepaction"]
> [A környezet változásainak megoldása](automation-tutorial-troubleshoot-changes.md)

* A [naplóbeli keresések használata Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md) a részletes változások követésére szolgáló információk megtekintéséhez.

