---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d373bc188a4d93c122cdf2cffbc4323aca16b4a4
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632800"
---
Ez a szakasz ismerteti, hogyan hozhat létre egy IoT hub használata a [az Azure portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza a +**erőforrás létrehozása**, majd válassza a **IOT-**.

3. Kattintson a **az Iot Hub** a jobb oldali listából. Az IoT hub létrehozása az első képernyőn láthatja.

   ![Eseményközpont létrehozásához az Azure Portalon](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

   Töltse ki a mezőket.

   **Előfizetés**: Válassza ki a az IoT hub használni kívánt előfizetést.

   **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt. Hozzon létre egy új, kattintson a **új létrehozása** , és töltse ki a használni kívánt nevét. Meglévő erőforráscsoport használatához kattintson **meglévő** és a legördülő listából válassza ki az erőforráscsoportot. További információkért lásd: [kezelése az Azure Resource Manager erőforráscsoportokat](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Régió**: Ez az a régió, amelyben a hub található szeretné. Válassza ki a legördülő listából Önhöz legközelebbi helyet.

   **Az IoT Hub nevét**: Helyezze el az IoT hub nevét. A névnek globálisan egyedinek kell lennie. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Kattintson a **tovább: Mérete és méretezése** az IoT hub létrehozása a folytatáshoz.

   ![Állítsa be a méretét és a méretezési csoport egy új IoT hub, az Azure portal használatával](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Ezen a képernyőn elvégezhető a szükséges az alapértelmezett beállításokat és egyszerűen kattintson **felülvizsgálat + létrehozása** alján. 

   **Tarifacsomag és méret**: Választhat több szinten funkciókat attól függően, hogy hány szeretne, és hány üzenetek küldése naponta a megoldáson keresztül. Az ingyenes szintjének célja, tesztelés és értékelés céljából használják. Lehetővé teszi a 500 eszközt az IoT hubhoz, és legfeljebb napi 8000 üzenet továbbítását kell csatlakoztatni. Minden Azure-előfizetés hozhat létre egy IoT Hub ingyenes szintjéhez. 

   **IoT Hub-egységek**: / Egység / nap engedélyezett üzenetek száma attól függ, hogy a hub tarifacsomagot. Például ha azt szeretné, hogy támogatja a bejövő forgalom 700 000 üzenetet az IoT hub, választhatja azt két S1 szintű egységre.

   A többi szint lehetőség részleteiért lásd: [a megfelelő IoT Hub-csomag kiválasztása](../articles/iot-hub/iot-hub-scaling.md).

   **Speciális / eszköz-felhő partíciók**: Ez a tulajdonság az eszköz a felhőbe irányuló üzeneteket az egyidejű olvasók az üzenetek vonatkozik. A legtöbb IoT-központok csak négy partíciót kell. 

5. Kattintson a **tekintse át + létrehozása** , ellenőrizze a beállításokat. Hasonló ezen a képernyőn láthatja.

   ![Tekintse át adatait az új IoT hub létrehozása](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

6. Kattintson a **létrehozás** az új IoT hub létrehozása. A hub létrehozása eltarthat néhány percig.
