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
ms.openlocfilehash: 935e2b9e861a889bef48c1d7ba119ab694cddfb3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094053"
---
## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban használhatja a [az Azure portal](https://portal.azure.com) hozhat létre egy új eszközidentitást az IoT hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. "Identitásjegyzék" című szakaszában talál további információt a [IoT Hub fejlesztői útmutatójának](../articles/iot-hub/iot-hub-devguide-identity-registry.md) használja a **IoT-eszközök** panel a portálon hozzon létre egy egyedi Eszközazonosítót, és az eszköz segítségével azonosíthatók a kulcs magát az IoT hubnak. Az eszközazonosítók megkülönböztetik a kis- és nagybetűket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. Válassza ki **összes erőforrás** , és keresse meg az IoT hub-erőforrást.

1. Amikor az IoT hub-erőforrás meg van nyitva, kattintson a **IoT-eszközök** eszközt, és kattintson a **Hozzáadás** tetején. 

    ![Eszközidentitás létrehozása a portálon](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. Adjon meg egy nevet az új eszköz, például: **myDeviceId**, és kattintson a **mentése**. Ez a művelet létrehoz egy új eszközidentitást az IoT hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Új eszköz felvétele](./media/iot-hub-get-started-create-device-identity-portal/add-device.png)

1. Az eszközlistában, kattintson az újonnan létrehozott eszköz és a példány a **kapcsolati karakterlánc---elsődleges kulcs** későbbi használat céljából.

    ![Eszköz kapcsolati karakterláncát](./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png)

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd: [IoT Hub fejlesztői útmutatójának](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
