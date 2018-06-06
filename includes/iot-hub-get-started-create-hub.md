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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34371235"
---
## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Hozzon létre egy IoT Hubot, amelyhez csatlakozhat a szimulált eszközalkalmazás. Az alábbi lépések bemutatják, hogyan hajthatja végre ezt a feladatot az Azure Portal segítségével.

1. Jelentkezzen be az [Azure Portalra][lnk-portal].

1. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.
   
    ![Azure Portal – ugrósáv][1]

1. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

   * **Előfizetés**: Válassza ki az IoT Hub létrehozásához használni kívánt előfizetést.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub üzemeltetéséhez, vagy használjon egy meglévőt. További információt az [Azure-erőforrások erőforráscsoportokkal való kezeléséről][lnk-resource-groups] szóló cikkben olvashat.

   * **Régió**: Válassza ki az Önhöz legközelebbi helyet.

   * **Név**: Adja meg az IoT Hub nevét. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Alapvető IoT Hub-beállítások ablak][2]

2. Válassza a **Tovább: Méret és skálázás** elemet az IoT hub létrehozásának folytatásához. 

3. Válasszon **tarifacsomag és méretet**. Erre a cikkre vonatkozóan válassza az **F1 – Ingyenes** szintet, ha elérhető az előfizetésében. További információkat a [díjszabással][lnk-pricing] kapcsolatos lapon olvashat.

   ![Az IoT Hub Méret és skálázás ablaka][3]

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Tekintse át az IoT Hub adatait, majd kattintson a **Létrehozás** gombra. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Miután létrejött az új IoT Hub, kattintson a csempéjére az Azure Portalon a hozzá tartozó Tulajdonságok ablak megnyitásához. Most, hogy létrehozta az IoT Hubot, keresse meg az eszközök és alkalmazások az IoT Hubhoz való csatlakoztatásához használt fontos adatokat. Kattintson a **Megosztott elérési szabályzatok** elemre.
   
1. A **Megosztott elérési szabályzatok** alatt válassza ki az **iothubowner** elemet. Másolja le az IoT Hub **Kapcsolati sztring – elsődleges kulcs** elemét, mert később szüksége lesz rá. További információkért tekintse meg az IoT Hub fejlesztői útmutató [hozzáférés-vezérléssel][lnk-access-control] foglalkozó részét.
   
    ![Megosztott elérési házirendek][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
