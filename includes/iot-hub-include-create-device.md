---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4a4ec51430242cc3e3d6a0b801f2b4be7858ab50
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516091"
---
<!-- put the ## header in the file that includes this file -->

Ebben a szakaszban az identitásjegyzékben egy eszközidentitást az IoT hub létrehozása. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. "Identitásjegyzék" című szakaszában talál további információt a [az IoT Hub fejlesztői útmutató](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. Az IoT hub navigációs menü megnyitása **IoT-eszközök**, majd kattintson a **Hozzáadás** új eszköz regisztrálása az IoT hub.

    ![Eszközidentitás létrehozása a portálon](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Adjon meg egy nevet az új eszköz, például: **myDeviceId**, és kattintson a **mentése**. Ez a művelet létrehoz egy új eszközidentitást az IoT hub.

   ![Új eszköz felvétele](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Az eszköz létrehozása után nyissa meg az eszköz a listából a **IoT-eszközök** ablaktáblán. Másolás a **kapcsolati karakterlánc---elsődleges kulcs** későbbi használat céljából.

    ![Eszköz kapcsolati karakterláncát](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd: [IoT Hub fejlesztői útmutatójának](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
