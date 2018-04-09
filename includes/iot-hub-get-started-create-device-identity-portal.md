---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban használhatja a [Azure-portálon] [ lnk-azure-portal] egy eszközidentitás az IoT hub a identitás rendszerleíró adatbázis létrehozásához. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd az [IoT Hub fejlesztői útmutatójának][lnk-devguide-identity] „Identitásjegyzék” című szakaszát. Használja a **az IoT-eszközök** a portál egy eszköz egyedi azonosítót vagy kulcsot a következőhöz azonosítja magát az IoT-központ az eszköz panel. Az eszközazonosítók megkülönböztetik a kis- és nagybetűket.

1. Győződjön meg arról, hogy be vannak jelentkezve a [Azure-portálon][lnk-azure-portal].

1. Az ugrósávon kattintson **összes erőforrás** és az IoT hub erőforrás található.

    ![Keresse meg az Iot hub][img-find-iothub]

1. Az IoT hub erőforrás már meg van nyitva, kattintson a **az IoT-eszközök** eszközzel, és kattintson a **Hozzáadás** tetején. Adjon meg a nevet az új eszköz, például: **myDeviceId**, és kattintson a **mentése**.

    ![Az eszközazonosító létrehozása a portálon][img-create-device]

   Ez a művelet létrehoz egy új, az IoT hub eszközidentitás.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Az a **az IoT-eszközök**tartozó eszközök listája, kattintson az újonnan létrehozott eszközre, és jegyezze fel az a **kapcsolati karakterlánc---elsődleges kulcs**.

    ![Eszköz kapcsolati karakterlánc][img-connection-string]

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd az [Azure IoT Hub fejlesztői útmutatóját][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

