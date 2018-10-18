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
ms.openlocfilehash: ffd5da239f8e271a8c9b2aaf3f6d5fd9f885c79c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400116"
---
## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban használhatja a [az Azure portal](https://portal.azure.com) hozhat létre egy új eszközidentitást az IoT hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. "Identitásjegyzék" című szakaszában talál további információt a [IoT Hub fejlesztői útmutatójának](../articles/iot-hub/iot-hub-devguide-identity-registry.md) használja a **IoT-eszközök** panel a portálon hozzon létre egy egyedi Eszközazonosítót, és az eszköz segítségével azonosíthatók a kulcs magát az IoT hubnak. Az eszközazonosítók megkülönböztetik a kis- és nagybetűket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

2. Válassza ki **összes erőforrás** , és keresse meg az IoT hub-erőforrást.

3. Amikor az IoT hub-erőforrás meg van nyitva, kattintson a **IoT-eszközök** eszközt, és kattintson a **Hozzáadás** tetején. 

    ![Eszközidentitás létrehozása a portálon](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

4. Adjon meg egy nevet az új eszköz, például: **myDeviceId**, és kattintson a **mentése**. Ez a művelet létrehoz egy új eszközidentitást az IoT hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Új eszköz felvétele](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

5. Az eszközlistában, kattintson az újonnan létrehozott eszköz és a példány a **kapcsolati karakterlánc---elsődleges kulcs** későbbi használat céljából.

    ![Eszköz kapcsolati karakterláncát](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd: [IoT Hub fejlesztői útmutatójának](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
