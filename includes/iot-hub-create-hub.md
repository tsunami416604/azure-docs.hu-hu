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
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
1. Jelentkezzen be az [Azure Portalra][lnk-portal].
1. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.
   
    ![Képernyőfelvétel az Azure portál navigációs az IoT hubhoz][1]

1. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

   * **Előfizetés**: válassza ezt az IoT-központ létrehozásához használni kívánt előfizetést.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub üzemeltetéséhez, vagy használjon egy meglévőt. További információkért lásd: [erőforráscsoportok használata az Azure erőforrások kezeléséhez][lnk-resource-groups].

   * **A régióban**: válassza ki az Önhöz legközelebbi helyet.

   * **Név**: Adja meg az IoT Hub nevét. Ha a név nem érhető el, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Az IoT-központ alapjai ablak][2]

2. Válassza ki **tovább: méretétől** az IoT hub létrehozása a folytatáshoz. 

3. Válassza ki a **árazás és méretcsomag**. Ez a cikk, válassza ki a **F1 – szabad** rétegben, ha az előfizetés még mindig érhető el. További információkat a [díjszabással][lnk-pricing] kapcsolatos lapon olvashat.

   ![Az IoT-központ méretétől ablak][3]

4. Válassza ki **felülvizsgálati + létrehozása**.

1. Tekintse át a IoT hub adatait, majd kattintson az **létrehozása**. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md