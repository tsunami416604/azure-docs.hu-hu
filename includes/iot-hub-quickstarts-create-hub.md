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
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008586"
---
Első lépésként az Azure Portal használatával hozzon létre IoT Hubot az előfizetésében. Az IoT Hub lehetővé teszi nagy mennyiségű telemetria betöltését számos eszközről a felhőbe. A Hub ezután engedélyt ad egy vagy több, a felhőben futó háttérszolgáltatásnak a telemetria olvasására és feldolgozására.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.

    ![Az IoT Hub telepítésének kiválasztása][1]

1. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

   * **Előfizetés**: Válassza ki az IoT Hub létrehozásához használni kívánt előfizetést.
   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub tárolásához, vagy használjon egy meglévőt. Ha az összes kapcsolódó erőforrást egy csoportban helyezi el (pl. **TestResources**), akkor mindet együtt kezelheti. Az erőforráscsoport törlése például az adott csoportban található összes erőforrást is törli. További információt az [Azure-erőforrások erőforráscsoportokkal való kezeléséről][lnk-resource-groups] szóló cikkben olvashat.
   * **Régió**: Válassza ki az eszközeihez legközelebbi helyet.
   * **Név**: Adja meg az IoT Hub egyedi nevét. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Alapvető IoT Hub-beállítások ablak][2]

2. Válassza a **Tovább: Méret és skálázás** elemet az IoT hub létrehozásának folytatásához. 

3. Válasszon **tarifacsomag és méretet**. Erre a cikkre vonatkozóan válassza az **F1 – Ingyenes** szintet, ha elérhető az előfizetésében. További információkat a [díjszabással][lnk-pricing] kapcsolatos lapon olvashat.

   ![Az IoT Hub Méret és skálázás ablaka][3]

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Tekintse át az IoT Hub adatait, majd kattintson a **Létrehozás** gombra. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md