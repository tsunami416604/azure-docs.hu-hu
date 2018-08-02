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
ms.openlocfilehash: fce9e42e24be7f8678292a5d98a683ca4e579cd2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39357060"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.
   
    ![Képernyőkép az IoT Hub az Azure Portalon történő megnyitásáról](./media/iot-hub-create-hub/create-iot-hub1.png)

3. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

   * **Előfizetés**: Válassza ki az IoT Hub létrehozásához használni kívánt előfizetést.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub üzemeltetéséhez, vagy használjon egy meglévőt. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről](../articles/azure-resource-manager/resource-group-portal.md) szóló cikkben olvashat.

   * **Régió**: Válassza ki az Önhöz legközelebbi helyet.

   * **Név**: Adja meg az IoT Hub nevét. Ha a megadott név elérhető, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Alapvető IoT Hub-beállítások ablak](./media/iot-hub-create-hub/create-iot-hub2.png)

4. Válassza a **Tovább: Méret és skálázás** elemet az IoT hub létrehozásának folytatásához. 

5. Válasszon **tarifacsomag és méretet**. Erre a cikkre vonatkozóan válassza az **F1 – Ingyenes** szintet, ha elérhető az előfizetésében. További információkat a [díjszabással](https://azure.microsoft.com/pricing/details/iot-hub/) kapcsolatos lapon olvashat.

   ![Az IoT Hub Méret és skálázás ablaka](./media/iot-hub-create-hub/create-iot-hub3.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget.

7. Tekintse át az IoT Hub adatait, majd kattintson a **Létrehozás** gombra. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.