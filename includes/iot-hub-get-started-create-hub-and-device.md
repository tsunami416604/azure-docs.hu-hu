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
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371371"
---
## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Most, hogy létrehozta az IoT-központ, keresse meg az eszközök és alkalmazások számára az IoT hub kapcsolódni a fontos információkról. 

Az IoT hub navigációs menü megnyitása **megosztott elérési házirendek**.
Válassza ki a **iothubowner** házirendet, majd másolja a **kapcsolati karakterlánc---elsődleges kulcs** az IoT hub. További információt [az IoT Hub-hozzáférés szabályozásával](../articles/iot-hub/iot-hub-devguide-security.md) kapcsolatos cikkben találhat.

   > [!NOTE] 
   > A jelen telepítés oktatóanyag nem kell a iothubowner kapcsolati karakterlánc. Azonban szükség lehet az egyes az oktatóprogramok vagy különböző IoT-forgatókönyvek esetében a telepítés befejezése után.

   ![Az IoT Hub kapcsolati karakterláncának beszerzése](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Eszköz regisztrálása az eszközéhez az IoT Hubon

1. Az IoT hub navigációs menü megnyitása **az IoT-eszközök**, majd kattintson a **Hozzáadás** eszköz regisztrálása az IoT hub.

   ![Az IoT-eszközök az IoT hub, az eszköz hozzáadásához](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Adjon meg egy **Eszközazonosító** az új eszköz. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Kattintson a **Save** (Mentés) gombra.
5. Az eszköz létrehozását követően nyissa meg az eszközt a listából a **az IoT-eszközök** ablaktáblán.
6. Másolás a **kapcsolati karakterlánc---elsődleges kulcs** későbbi használat céljából.

   ![Az eszköz kapcsolati karakterláncának beszerzése](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
