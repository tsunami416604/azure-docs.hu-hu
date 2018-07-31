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
ms.openlocfilehash: 9f7fee71fb2b80be93d978569791dbb57f137949
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346834"
---
## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Most, hogy létrehozta az IoT Hubot, keresse meg az eszközök és alkalmazások az IoT Hubhoz való csatlakoztatásához használt fontos adatokat. 

Az IoT hub navigációs menü megnyitása **megosztott elérési házirendek**. Válassza ki a **iothubowner** házirendet, majd másolja a **kapcsolati karakterlánc---elsődleges kulcs** az IoT hub. További információt [az IoT Hub-hozzáférés szabályozásával](../articles/iot-hub/iot-hub-devguide-security.md) kapcsolatos cikkben találhat.

   > [!NOTE] 
   > Ennek a beállítási oktatóanyagnak az iothubowner kapcsolati karakterlánc nem kell. Azonban szükség lehet, néhány oktatóanyagok vagy különböző IoT-forgatókönyvek esetében a telepítés befejezése után.

   ![Az IoT Hub kapcsolati sztringjének beszerzése](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-your-device-in-the-iot-hub"></a>Az eszköz regisztrálása az IoT hubban

1. Az IoT hub navigációs menü megnyitása **IoT-eszközök**, majd kattintson a **hozzáadása** az eszköz regisztrálása az IoT hubban.

   ![Eszköz felvétele az IoT hub IoT-eszközök](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Adjon meg egy **Eszközazonosító** az új eszköz. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

3. Kattintson a **Save** (Mentés) gombra.

4. Az eszköz létrehozása után nyissa meg az eszköz a listából a **IoT-eszközök** ablaktáblán.

5. Másolás a **kapcsolati karakterlánc---elsődleges kulcs** későbbi használat céljából.

   ![Az eszköz kapcsolati sztringjének beszerzése](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
