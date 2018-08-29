---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111211"
---
## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Hozzon létre egy IoT Hubot, amelyhez csatlakozhat a szimulált eszközalkalmazás. Az alábbi lépések bemutatják, hogyan hajthatja végre ezt a feladatot az Azure Portal segítségével.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.
   
    ![Azure Portal – ugrósáv](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

   * **Előfizetés**: Válassza ki az IoT Hub létrehozásához használni kívánt előfizetést.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub üzemeltetéséhez, vagy használjon egy meglévőt. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről](../articles/azure-resource-manager/resource-group-portal.md) szóló cikkben olvashat.

   * **Régió**: Válassza ki az Önhöz legközelebbi helyet.

   * **Név**: Adja meg az IoT Hub nevét. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Alapvető IoT Hub-beállítások ablak](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. Válassza a **Tovább: Méret és skálázás** elemet az IoT hub létrehozásának folytatásához. 

5. Válasszon **tarifacsomag és méretet**. Erre a cikkre vonatkozóan válassza az **F1 – Ingyenes** szintet, ha elérhető az előfizetésében. További információkat a [díjszabással](https://azure.microsoft.com/pricing/details/iot-hub/) kapcsolatos lapon olvashat.

   ![Az IoT Hub Méret és skálázás ablaka](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget.

7. Tekintse át az IoT Hub adatait, majd kattintson a **Létrehozás** gombra. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

8. Miután létrejött az új IoT Hub, kattintson a csempéjére az Azure Portalon a hozzá tartozó Tulajdonságok ablak megnyitásához. Most, hogy létrehozta az IoT Hubot, keresse meg az eszközök és alkalmazások az IoT Hubhoz való csatlakoztatásához használt fontos adatokat. Kattintson a **Megosztott elérési szabályzatok** elemre.
   
9. A **Megosztott elérési szabályzatok** alatt válassza ki az **iothubowner** elemet. Másolja le az IoT Hub **Kapcsolati sztring – elsődleges kulcs** elemét, mert később szüksége lesz rá. További információkért tekintse meg az IoT Hub fejlesztői útmutató [hozzáférés-vezérléssel](../articles/iot-hub/iot-hub-devguide-security.md) foglalkozó részét.
   
    ![Megosztott elérési házirendek](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)