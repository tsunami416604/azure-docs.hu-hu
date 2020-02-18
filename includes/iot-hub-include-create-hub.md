---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/13/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 775e9fc737798a0e5517f9eb3314c71b14c5be07
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372398"
---
Ez a szakasz azt ismerteti, hogyan hozható létre IoT hub a [Azure Portal](https://portal.azure.com)használatával.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. Az Azure kezdőlapján kattintson a **+ erőforrás létrehozása** gombra, majd írja be a *IoT hub* **kifejezést a keresés a piactéren** mezőbe.

1. Válassza a **IoT hub** elemet a keresési eredmények közül, majd válassza a **Létrehozás**lehetőséget.

1. Az **alapvető beállítások** lapon végezze el a következő mezőket:

   - **Előfizetés**: válassza ki a hubhoz használni kívánt előfizetést.

   - **Erőforráscsoport**: válasszon ki egy erőforráscsoportot, vagy hozzon létre újat. Új létrehozásához válassza az **új létrehozása** elemet, és adja meg a használni kívánt nevet. Meglévő erőforráscsoport használatához válassza ki az erőforráscsoportot. További információ: [Azure Resource Manager erőforráscsoportok kezelése](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Régió**: válassza ki azt a régiót, amelyben el szeretné helyezni a hubot. Válassza ki az Önhöz legközelebb eső helyet.

   - **IoT hub neve**: adja meg a hub nevét. A névnek globálisan egyedinek kell lennie. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Hub létrehozása a Azure Portalban](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Válassza a **Next (tovább): méret és méretezés** lehetőséget a központ létrehozásának folytatásához.

   ![Egy új hub méretének és méretezésének beállítása a Azure Portal használatával](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    Ezen a képernyőn állíthatja be a következő értékeket:

    - **Díjszabás és méretezési szintek**: a kiválasztott csomag. Számos különböző szinten választhat, attól függően, hogy hány szolgáltatást szeretne, és hány üzenetet küld naponta a megoldáson keresztül. Az ingyenes szintet tesztelésre és kiértékelésre tervezték. Lehetővé teszi, hogy a 500-es eszközök naponta és legfeljebb 8 000 üzenetet csatlakoztassanak az elosztóhoz. Az egyes Azure-előfizetések az ingyenes szinten hozhatnak létre egy IoT hubot.

    - **IoT hub egységek**: a napi egységenként engedélyezett üzenetek száma a központ díjszabási szintjétől függ. Ha például azt szeretné, hogy a központ támogassa a 700 000-es bejövő üzeneteket, két S1 szintű egységet választ.
    További információ a többi lehetőségről: [a megfelelő IoT hub](../articles/iot-hub/iot-hub-scaling.md)-szinten való választás.

    - **Azure Security Center**: kapcsolja be ezt a beállítást, ha további veszélyforrások elleni védelmet szeretne hozzáadni a IoT és az eszközeihez. Ez a lehetőség az ingyenes szinten lévő hubok esetében nem érhető el. További információ erről a szolgáltatásról: [Azure Security Center a IoT](https://docs.microsoft.com/azure/asc-for-iot/).

    - Az **eszközről a felhőbe irányuló partíciók** > **Speciális beállítások** : Ez a tulajdonság az eszközről a felhőbe irányuló üzeneteket az üzenetek egyidejű olvasóinak számához kapcsolja. A legtöbb hubhoz csak négy partíció szükséges.

1. Ebben a cikkben fogadja el az alapértelmezett beállításokat, majd kattintson a **Next (tovább** ) gombra a következő képernyőre való áttéréshez.

    A címkék név/érték párok. Ugyanazt a címkét több erőforráshoz és erőforráscsoporthoz is hozzárendelheti az erőforrások kategorizálása és a számlázás konszolidálása céljából. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../articles/azure-resource-manager/management/tag-resources.md).

   ![Egy új hub méretének és méretezésének beállítása a Azure Portal használatával](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

    Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet a választási lehetőségek áttekintéséhez. Ehhez hasonló képernyő jelenik meg.

   ![Tekintse át az új központ létrehozásával kapcsolatos információkat](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1. Válassza a **Létrehozás** lehetőséget az új központ létrehozásához. A hub létrehozása eltarthat néhány percig.
