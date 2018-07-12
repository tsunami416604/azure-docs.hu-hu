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
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724926"
---
## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Most, hogy létrehozta az IoT Hubot, keresse meg az eszközök és alkalmazások az IoT Hubhoz való csatlakoztatásához használt fontos adatokat. 

Az IoT hub navigációs menü megnyitása **megosztott elérési házirendek**.
Válassza ki a **iothubowner** házirendet, majd másolja a **kapcsolati karakterlánc---elsődleges kulcs** az IoT hub. További információt [az IoT Hub-hozzáférés szabályozásával](../articles/iot-hub/iot-hub-devguide-security.md) kapcsolatos cikkben találhat.

   > [!NOTE] 
   > Ennek a beállítási oktatóanyagnak az iothubowner kapcsolati karakterlánc nem kell. Azonban szükség lehet, néhány oktatóanyagok vagy különböző IoT-forgatókönyvek esetében a telepítés befejezése után.

   ![Az IoT Hub kapcsolati sztringjének beszerzése](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Eszköz regisztrálása az eszközéhez az IoT Hubon

1. Az IoT hub navigációs menü megnyitása **IoT-eszközök**, majd kattintson a **hozzáadása** az eszköz regisztrálása az IoT hubban.

   ![Eszköz felvétele az IoT hub IoT-eszközök](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Adjon meg egy **Eszközazonosító** az új eszköz. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Kattintson a **Save** (Mentés) gombra.
5. Az eszköz létrehozása után nyissa meg az eszköz a listából a **IoT-eszközök** ablaktáblán.
6. Másolás a **kapcsolati karakterlánc---elsődleges kulcs** későbbi használat céljából.

   ![Az eszköz kapcsolati sztringjének beszerzése](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
