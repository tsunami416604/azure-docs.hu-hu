---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cc53f7ae6eb6254eaa05bf643ecfa0188650df95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77461861"
---
Ez a szakasz bemutatja, hogyan hozhat létre egy IoT-központot az [Azure Portal](https://portal.azure.com)használatával.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Az Azure kezdőlapján válassza a **+ Erőforrás létrehozása** gombot, majd írja be az *IoT Hub* kifejezést a **Piactér keresése** mezőben.

1. Válassza az **IoT Hub** elemet a keresési eredmények között, majd válassza a **Létrehozás gombot.**

1. Az **Alapok** lapon töltse ki a mezőket az alábbiak szerint:

   - **Előfizetés**: Válassza ki a hubhoz használandó előfizetést.

   - **Erőforráscsoport**: Jelöljön ki egy erőforráscsoportot, vagy hozzon létre egy újat. Új létrehozása, válassza **az Új létrehozása** és töltse ki a kívánt nevet. Meglévő erőforráscsoport használatához jelölje ki az adott erőforráscsoportot. További információt az [Azure Resource Manager erőforráscsoportok kezelése](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)című témakörben talál.

   - **Régió**: Válassza ki azt a régiót, amelyben a hubot el szeretné helyezni. Válassza ki az Önhöz legközelebb eső helyet. Egyes funkciók, például [az IoT Hub eszközstreamek](../articles/iot-hub/iot-hub-device-streams-overview.md), csak bizonyos régiókban érhetők el. Ezekhez a korlátozott funkciókhoz ki kell választania a támogatott régiók egyikét.

   - **IoT Hub-név:** Adja meg a hub nevét. A névnek globálisan egyedinek kell lennie. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Hub létrehozása az Azure Portalon](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Válassza a **Tovább: Méret és méretezés** a hub létrehozásának folytatásához.

   ![Új központ méretének és méretezésének beállítása az Azure Portal használatával](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Itt elfogadhatja az alapértelmezett beállításokat. Ha szükséges, az alábbi mezők bármelyikét módosíthatja: 

    - **Díjszabási és méretezési szint:** A kiválasztott szint. Több szint közül választhat, attól függően, hogy hány szolgáltatást szeretne, és naponta hány üzenetet küld a megoldáson keresztül. Az ingyenes szint tesztelésre és értékelésre szolgál. Lehetővé teszi 500 eszköz csatlakoztatását a hubhoz, és akár 8000 üzenetet naponta. Minden Azure-előfizetés létrehozhat egy IoT-központot az ingyenes csomagban. 

      Ha az IoT Hub-eszközstreamek rövid útmutatójában dolgozik, válassza ki az ingyenes szintet.

    - **IoT Hub-egységek:** Az egy egységenként naponta engedélyezett üzenetek száma a központ tarifacsomagjátatól függ. Ha például azt szeretné, hogy a hub 700 000 üzenet be- és be- és be- és be- és be- és be- és be- és be- és átszállását támogassa, két S1-szintegységet kell választania.
    A többi rétegbeállításokkal kapcsolatos további részletekért [olvassa el a megfelelő IoT Hub-szint kiválasztása című témakört.](../articles/iot-hub/iot-hub-scaling.md)

    - **Azure Security Center:** Kapcsolja be ezt, hogy egy további réteg fenyegetésvédelmi IoT és az eszközök. Ez a beállítás nem érhető el az ingyenes szint hubjaihoz. A funkcióról további információt az [Azure Security Center for IoT című](https://docs.microsoft.com/azure/asc-for-iot/)témakörben talál.

    - **Speciális beállítások** > **Eszközről felhőbe partíciók**: Ez a tulajdonság az eszközről a felhőbe irányuló üzeneteket az üzenetek egyidejű olvasóinak számával kapcsolja össze. A legtöbb hubnak csak négy partícióra van szüksége.

1.  Válassza a **Tovább: Címkék lehetőséget** a következő képernyőre való továbblépéshez.

    A címkék név-érték párok. Ugyanazt a címkét több erőforráshoz és erőforráscsoporthoz is hozzárendelheti az erőforrások kategorizálásához és a számlázás konszolidálásához. További információt az [Azure-erőforrások rendszerezéséhez címkék használata című](../articles/azure-resource-manager/management/tag-resources.md)témakörben talál.

    ![Címkék hozzárendelése a központhoz az Azure Portal használatával](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  Válassza a **Tovább: Ellenőrzés + létrehozás lehetőséget** a lehetőségek áttekintéséhez. A képernyőhöz hasonló, de a hub létrehozásakor kiválasztott értékekkel együtt jelenik meg. 

    ![Az új központ létrehozásához szükséges információk áttekintése](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  Az új központ létrehozásához válassza a **Létrehozás** gombot. A hub létrehozása néhány percet vesz igénybe.
