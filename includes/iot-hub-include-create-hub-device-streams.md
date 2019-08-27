---
title: fájl belefoglalása (eszköz-adatfolyamok előzetes verzió)
description: fájl belefoglalása (eszköz-adatfolyamok előzetes verzió)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a02c16a75bbdf4827ce6a6c5f50751caf9c5aab8
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445984"
---
Ez a szakasz azt ismerteti, hogyan hozható létre IoT hub a [Azure Portal](https://portal.azure.com)használatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza **az erőforrás létrehozása**lehetőséget, majd válassza a **eszközök internetes hálózata**lehetőséget.

1. A jobb oldali listában válassza az **IOT hub**elemet. Megnyílik az IoT hub létrehozásának első lapja.

   ![IoT hub létrehozása a Azure Portalban](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Töltse ki a mezőket:

   a. Az **előfizetés** legördülő listában válassza ki az IoT hub-hoz használni kívánt előfizetést.

   b. **Erőforráscsoport**esetén hajtsa végre az alábbi műveletek egyikét: 
      * Új erőforráscsoport létrehozásához válassza az **új létrehozása** lehetőséget, és adja meg a használni kívánt nevet. 
      * Meglévő erőforráscsoport használatához válassza a **meglévő használata** lehetőséget, majd a legördülő listából válassza ki az erőforráscsoportot. 
      
        További információ: [Azure Resource Manager erőforráscsoportok kezelése](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. A **régió** legördülő listában válassza ki azt a régiót, amelyben el szeretné helyezni a hubot. Válasszon egy olyan régiót, amely támogatja a IoT Hub Device Streams Preview-t, az **USA középső** régióját vagy az **USA középső – euap**.

   d. A **IoT hub neve** mezőbe írja be az IoT hub nevét. A névnek globálisan egyedinek kell lennie. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Az IoT hub létrehozásának folytatásához válassza a **tovább lehetőséget: Méret és skála**.

   ![Az új IoT hub méretének és méretezésének beállítása a Azure Portal használatával](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Ebben az ablaktáblában elfogadhatja az alapértelmezett beállításokat, és a lap alján válassza a **felülvizsgálat + létrehozás** elemet. Vegye figyelembe a következő lehetőségeket:

   * A **díjszabás és méretezési réteg** legördülő listában válassza ki az egyik standard szintet (**S1**, **S2**vagy **S3**) vagy **az F1 billentyűt: Ingyenes szintet**. Ezt a választást a flotta mérete és a központban várható nem streaming munkaterhelések (például telemetria-üzenetek) is megadhatja. Az ingyenes szintet például tesztelésre és kiértékelésre tervezték. Lehetővé teszi, hogy a 500-es eszközök a IoT hubhoz és naponta legfeljebb 8 000 üzenetet csatlakoztassanak. Az egyes Azure-előfizetések az ingyenes szinten hozhatnak létre egy IoT hubot. 

   * **IoT hub egységek száma**: Ez a választás a központban várható nem streaming számítási feladatoktől függ. Most kiválaszthatja az 1 lehetőséget.

   További információ a rétegek beállításairól: [válassza a megfelelő IoT hub-szintet](../articles/iot-hub/iot-hub-scaling.md).

1. A lehetőségek áttekintéséhez válassza a **felülvizsgálat + létrehozás** lapot. A megnyíló ablaktábla a következőhöz hasonló:

   ![Információk az új IoT hub létrehozásához](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Az új IoT hub létrehozásához válassza a **Létrehozás**lehetőséget. A folyamat eltarthat néhány percig.
