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
ms.openlocfilehash: 93c71fa8b0c39cc16d2a8e24472e8d68717a6c32
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733249"
---
Ez a szakasz ismerteti, hogyan hozhat létre az IoT hub használatával a [az Azure portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **erőforrás létrehozása**, majd válassza ki **IOT-** .

1. Válassza a lista jobb **az Iot Hub**. Az IoT hub létrehozásához az első lap nyílik meg.

   ![Az IoT hub létrehozása az Azure Portalon](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Töltse ki a mezőket:

   a. Az a **előfizetés** legördülő listára, válassza ki a az IoT hub használni kívánt előfizetést.

   b. A **erőforráscsoport**, tegye a következők egyikét: 
      * Hozzon létre egy új erőforráscsoportot, válassza a **új létrehozása** , és adja meg a használni kívánt nevét. 
      * Használjon egy meglévő erőforráscsoportot, válassza a **meglévő** és majd a legördülő listában válassza ki az erőforráscsoportot. 
      
        További információkért lásd: [kezelése az Azure Resource Manager erőforráscsoportokat](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. Az a **régió** legördülő listára, válassza ki a régiót, amelyben a hub található szeretné. Válassza ki a régiót az IoT Hub eszköz Streamek előzetes támogató **USA középső RÉGIÓJA** vagy **központi USA régiója – EUAP**.

   d. Az a **IoT Hub nevére** mezőbe írja be az IoT hub nevét. A névnek globálisan egyedinek kell lennie. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Az IoT hub létrehozása a folytatáshoz válasszon ki **tovább: Mérete és méretezése**.

   ![Méret és a egy új IoT hubot, az Azure portal használatával nagy számú beállítása](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Ezen a panelen fogadja el az alapértelmezett beállításokat és válassza ki **felülvizsgálat + létrehozása** alján. Vegye figyelembe a következő beállításokat:

   * Az a **díjszabással** legördülő listában válasszon a standard csomagokhoz (**S1**, **S2**, vagy **S3**) vagy **F1: Ingyenes szint**. Ez a választás vezérlik a flotta és az eseményközpont (például telemetriai üzeneteket) várt nem streamelési számítási feladatokat is. Például az ingyenes szintjének célja, tesztelés és értékelés céljából használják. Lehetővé teszi a 500 eszközt az IoT hubhoz, és legfeljebb napi 8000 üzenet továbbítását kell csatlakoztatni. Minden egyes Azure-előfizetést hozhat létre egy IoT hubot az ingyenes szint. 

   * A **száma az IoT Hub-egységek**: Ez a választás a hub várhatóan nem streamelési munkaterheléstől függ. Most kiválaszthatja a 1.

   Réteg lehetőségekkel kapcsolatos további információkért lásd: [válassza ki a megfelelő IoT hub-csomag](../articles/iot-hub/iot-hub-scaling.md).

1. Ellenőrizze a beállításokat, jelölje be a **tekintse át + létrehozása** fülre. A megnyíló panelen az alábbihoz hasonlít:

   ![Információk az új IoT hub létrehozásához](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Az új IoT hub létrehozásához válassza **létrehozás**. A folyamat pár percet vesz igénybe.
