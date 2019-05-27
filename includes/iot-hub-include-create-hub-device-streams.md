---
title: adatfájl (eszköz Streamek előzetes verzió)
description: adatfájl (eszköz Streamek előzetes verzió)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: ede897054a6cbef254c06bd1d810b933ec09016a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158625"
---
Ez a szakasz ismerteti, hogyan hozhat létre egy IoT hub használata a [az Azure portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a +**erőforrás létrehozása**, majd válassza a **IOT-**.

3. Kattintson a **az Iot Hub** a jobb oldali listából. Az IoT hub létrehozása az első képernyőn láthatja.

   ![Képernyőfelvétel: a központ létrehozása az Azure Portalon](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Töltse ki a mezőket:

   **Előfizetés**: Válassza ki a az IoT hub használni kívánt előfizetést.

   **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt. Hozzon létre egy új, kattintson a **új létrehozása** , és töltse ki a használni kívánt nevét. Meglévő erőforráscsoport használatához kattintson **meglévő** és a legördülő listából válassza ki az erőforráscsoportot. További információkért lásd: [kezelése az Azure Resource Manager erőforráscsoportokat](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Régió**: Ez az a régió, amelyben a hub található szeretné. Válassza ki a régiót, amely támogatja az IoT Hub eszköz Streamek előzetes, USA középső RÉGIÓJA vagy a központi USA régiója – EUAP.

   **Az IoT Hub nevét**: Helyezze el az IoT hub nevét. A névnek globálisan egyedinek kell lennie. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Kattintson a **tovább: Mérete és méretezése** az IoT hub létrehozása a folytatáshoz.

   ![Képernyőfelvétel: a beállítás mérete és méretezése egy új IoT hub, az Azure portal használatával](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Ezen a képernyőn elvégezhető a szükséges az alapértelmezett beállításokat és egyszerűen kattintson **felülvizsgálat + létrehozása** alján.

   **Tarifacsomag és méret**: Győződjön meg arról, válassza ki a standard (S1, S2, S3) vagy az ingyenes (F1) szinten. Ez a választás az Ön és a streamelési nem tervezett munkaterhelési típusoktól az hub (pl. telemetriai üzeneteket) méretét is vezérlik. Például az ingyenes szintjének célja, tesztelés és értékelés céljából használják. Lehetővé teszi a 500 eszközt az IoT hubhoz, és legfeljebb napi 8000 üzenet továbbítását kell csatlakoztatni. Minden Azure-előfizetés hozhat létre egy IoT Hub ingyenes szintjéhez. 

   **IoT Hub-egységek**: Ez a választás attól függ, számítási feladatok várt az eseményközpont-streaming - most kiválaszthatja az 1.

   A többi szint lehetőség részleteiért lásd: [a megfelelő IoT Hub-csomag kiválasztása](../articles/iot-hub/iot-hub-scaling.md).

5. Kattintson a **tekintse át + létrehozása** , ellenőrizze a beállításokat. Hasonló ezen a képernyőn láthatja.

   ![Képernyőfelvétel az új IoT hub létrehozásához információk áttekintése](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

6. Kattintson a **létrehozás** az új IoT hub létrehozása. A hub létrehozása eltarthat néhány percig.
