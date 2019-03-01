---
title: Megismerheti az Azure IoT Hub üzenetküldés |} A Microsoft Docs
description: Fejlesztői útmutató – eszközről a felhőbe és a felhőből az eszközre irányuló, az IoT Hub üzenetküldési. Üzenet-formátumok és a támogatott kommunikációs protokollok kapcsolatos információkat tartalmaz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010871"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Az IoT Hub eszköz-felhő és a felhőből az eszközre irányuló üzenetek küldése

Az IoT Hub lehetővé teszi, hogy kétirányú kommunikációt, az eszközökkel. Használja az IoT Hub üzenetküldési és az eszközök közötti kommunikációt az üzenetek küldését az eszközök által vissza megoldásaiban befejezése, és a parancsok küldését az IoT megoldások háttér-eszközökre. Tudjon meg többet a [az IoT Hub üzenet formátuma](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Eszköz a felhőbe irányuló üzenetek küldése az IoT Hub

Az IoT Hub rendelkezik egy beépített szolgáltatás végpontjának telemetriai üzeneteket beolvasni az eszközök háttér-szolgáltatások által használható. Ez a végpont kompatibilis [az Event Hubs](https://docs.microsoft.com/azure/event-hubs/) és használhatja a szokásos IoT Hub SDK-k az [olvassa el a beépített végpontról](iot-hub-devguide-messages-read-builtin.md).

Az IoT Hub is támogatja a [egyéni végpontok](iot-hub-devguide-endpoints.md#custom-endpoints) , amely a felhasználók eszköz telemetriai adatokat és eseményeket küldhet az Azure-szolgáltatások használatával definiálható [üzenet-útválasztása](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Felhőből az eszközre irányuló üzenetek küldése az IoT hubról

Küldhet [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) üzenetek a megoldás a háttéralkalmazás az eszközökön.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub üzenetküldési funkció alapvető tulajdonságok akkor, a megbízhatóság és a tartós üzeneteket. Ezek a tulajdonságok engedélyezése nem állandó hálózati kapcsolat az eszközoldalon, és a felhő oldalon Eseményfeldolgozási kiugrások betöltése hibatűrést. Az IoT Hub valósít meg *legalább egyszer* kézbesítési garantálja az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>A megfelelő IoT Hub üzenetküldési-típus kiválasztása

A time series telemetriai adatokhoz és riasztásokhoz küldését az eszközalkalmazás eszköz – felhő üzeneteket, és a felhőből az eszközre irányuló üzenetek használata egyirányú értesítések az eszközalkalmazáshoz.

* Tekintse meg [eszközről a felhőbe való kommunikáció útmutatást](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) választhat eszköz – felhő üzeneteket, jelentett tulajdonságok, vagy a fájl feltöltése.

* Tekintse meg [felhőből az eszközre irányuló kommunikáció útmutatást](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) a felhőből az eszközre irányuló üzenetek, kívánt tulajdonságok vagy közvetlen metódusok közül választhat.

## <a name="next-steps"></a>További lépések

* További tudnivalók az IoT Hub [üzenet-útválasztása](iot-hub-devguide-messages-d2c.md).

* További tudnivalók az IoT Hub [felhőből az eszközre irányuló üzenetküldés](iot-hub-devguide-messages-c2d.md).