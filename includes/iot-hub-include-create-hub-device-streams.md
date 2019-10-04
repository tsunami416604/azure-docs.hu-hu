---
title: fájl belefoglalása (eszköz-adatfolyamok előzetes verzió)
description: fájl belefoglalása (eszköz-adatfolyamok előzetes verzió)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46f8e59713896cd94b96de62d982072119c32513
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050169"
---
Ez a szakasz azt ismerteti, hogyan hozható létre IoT hub a [Azure Portal](https://portal.azure.com)használatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza az **erőforrás létrehozása**lehetőséget, majd írja be a IoT hub **kifejezést a keresés a piactéren** mezőbe.

1. Válassza a **IoT hub** elemet a keresési eredmények közül, majd válassza a **Létrehozás**lehetőséget.

1. Az **alapvető beállítások** lapon végezze el a következő mezőket:

   - **Előfizetés**: Válassza ki a hubhoz használni kívánt előfizetést.

   - **Erőforráscsoport**: Válasszon egy erőforráscsoportot, vagy hozzon létre egy újat. Új létrehozásához válassza az **új létrehozása** elemet, és adja meg a használni kívánt nevet. Meglévő erőforráscsoport használatához válassza ki az erőforráscsoportot. További információ: [Azure Resource Manager erőforráscsoportok kezelése](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Régió**: Válassza ki azt a régiót, amelyben el szeretné helyezni a hubot. Válasszon egy olyan régiót, amely támogatja a IoT Hub Device Streams Preview-t, az **USA középső** régióját vagy az **USA középső – euap**.

   - **IoT hub neve**: Adja meg a hub nevét. A névnek globálisan egyedinek kell lennie. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   ![IoT hub létrehozása a Azure Portalban](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Válassza **a Next (tovább) lehetőséget: Méret és méretezés** a központ létrehozásához.

   ![Az új IoT hub méretének és méretezésének beállítása a Azure Portal használatával](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   A **méret és a skála**lehetőségnél fogadja el az alapértelmezett beállításokat, és válassza a lap alján található **Áttekintés + létrehozás** elemet. Vegye figyelembe a következő lehetőségeket:

   - **Díjszabási és skálázási szintek**: A kiválasztott szintet. Válassza ki az egyik standard szintet (**S1**, **S2**vagy **S3**) vagy **az F1: Ingyenes szintet**. Ezt a választást a flotta mérete és a központban várható nem streaming számítási feladatok, például a telemetria üzenetei is megadhatják. Az ingyenes szintet például tesztelésre és kiértékelésre tervezték. Lehetővé teszi, hogy a 500-es eszközök a IoT hubhoz és naponta legfeljebb 8 000 üzenetet csatlakoztassanak. Az egyes Azure-előfizetések az ingyenes szinten hozhatnak létre egy IoT hubot. 

   - **IoT hub egységek száma**: A napi egységenként engedélyezett üzenetek száma a központ díjszabási szintjétől függ. Ez a választás a központban várható nem streaming számítási feladatoktől függ. Most kiválaszthatja az 1 lehetőséget.

   -  > **Az eszközről a felhőbe irányuló**speciális beállítások: Ez a tulajdonság az eszközről a felhőbe irányuló üzeneteket az üzenetek egyidejű olvasóinak számához kapcsolja. A legtöbb hubhoz csak négy partíció szükséges.

   További információ a rétegek beállításairól: [válassza a megfelelő IoT hub-szintet](../articles/iot-hub/iot-hub-scaling.md).

1. A lehetőségek áttekintéséhez válassza a **felülvizsgálat + létrehozás**elemet. Az eredmények a következőhöz hasonlóak lesznek:

   ![Információk az új IoT hub létrehozásához](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Az új IoT hub létrehozásához válassza a **Létrehozás**lehetőséget. A folyamat eltarthat néhány percig.
