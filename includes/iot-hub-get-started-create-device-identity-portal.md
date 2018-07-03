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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "34371201"
---
## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban használhatja a [az Azure portal] [ lnk-azure-portal] hozhat létre egy új eszközidentitást az IoT hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd az [IoT Hub fejlesztői útmutatójának][lnk-devguide-identity] „Identitásjegyzék” című szakaszát. Használja a **IoT-eszközök** panel a portálon hozzon létre egy egyedi Eszközazonosítót, és az eszköz azonosítani tudja magát az IoT hub kulcsára. Az eszközazonosítók megkülönböztetik a kis- és nagybetűket.

1. Jelentkezzen be az [Azure Portalra][lnk-azure-portal].

1. Válassza ki **összes erőforrás** , és keresse meg az IoT hub-erőforrást.

1. Amikor az IoT hub-erőforrás meg van nyitva, kattintson a **IoT-eszközök** eszközt, és kattintson a **Hozzáadás** tetején. 

    ![Eszközidentitás létrehozása a portálon][img-add-device]

1. Adjon meg egy nevet az új eszköz, például: **myDeviceId**, és kattintson a **mentése**. Ez a művelet létrehoz egy új eszközidentitást az IoT hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Új eszköz felvétele][img-create-device]

1. Az eszközlistában, kattintson az újonnan létrehozott eszköz és a példány a **kapcsolati karakterlánc---elsődleges kulcs** későbbi használat céljából.

    ![Eszköz kapcsolati karakterláncát][img-connection-string]

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd az [Azure IoT Hub fejlesztői útmutatóját][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

