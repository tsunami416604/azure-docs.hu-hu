---
title: Az Azure IoT Hub üzenetkezelés ismertetése | Microsoft Docs
description: Fejlesztői útmutató – eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldés IoT Hub használatával. Az üzenetek formátumával és a támogatott kommunikációs protokollokkal kapcsolatos információkat tartalmazza.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "60626247"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek küldése IoT Hub

IoT Hub lehetővé teszi a kétirányú kommunikációt az eszközökkel. IoT Hub üzenetkezeléssel kommunikálhat az eszközökkel, ha üzeneteket küld az eszközökről a megoldási háttérbe, és parancsokat küld a IoT-megoldásairól a saját eszközeire. További információ a [IoT hub üzenet formátumáról](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Eszközről a felhőbe irányuló üzenetek küldése IoT Hub

IoT Hub rendelkezik egy beépített szolgáltatás-végponttal, amelyet a háttér-szolgáltatások használhatnak a telemetria üzenetek olvasásához az eszközökről. Ez a végpont kompatibilis a [Event Hubskel](https://docs.microsoft.com/azure/event-hubs/) , és a szabványos IoT hub SDK-k használatával a [beépített végpontból is elolvasható](iot-hub-devguide-messages-read-builtin.md).

A IoT Hub támogatja az [Egyéni végpontokat](iot-hub-devguide-endpoints.md#custom-endpoints) is, amelyeket a felhasználók az eszközök telemetria és eseményeinek az Azure-szolgáltatásokhoz való küldésére használhatnak az [üzenet-útválasztás](iot-hub-devguide-messages-d2c.md)használatával.

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>A felhőből az eszközre irányuló üzenetek küldése IoT Hub

A megoldás háttérbeli üzeneteiről az eszközökre küldheti a [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) irányuló üzeneteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A IoT Hub üzenetkezelési funkcióinak alapvető tulajdonságai az üzenetek megbízhatósága és tartóssága. Ezek a tulajdonságok lehetővé teszik a rugalmasságot az eszköz oldalának időnkénti kapcsolatára, valamint a Felhőbeli események feldolgozására vonatkozó tüskék betöltését. IoT Hub *legalább egyszer* implementálja az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldéshez szükséges garanciákat.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>A megfelelő típusú IoT Hub üzenetkezelés kiválasztása

Az eszközről a felhőbe irányuló üzenetek használatával idősorozat-telemetria és-riasztásokat küldhet az eszköz alkalmazásaiból, valamint a felhőből az eszközre irányuló üzeneteket az eszköz alkalmazására vonatkozó egyirányú értesítésekhez.

* Tekintse meg az [eszközről a felhőbe irányuló kommunikációs útmutatót](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) az eszközről a felhőbe irányuló üzenetek, a jelentett tulajdonságok vagy a fájlfeltöltés közötti választáshoz.

* Tekintse át a [felhőből az eszközre irányuló kommunikációs útmutatót](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) a felhőből az eszközre irányuló üzenetek, a kívánt tulajdonságok vagy a közvetlen metódusok közötti választáshoz.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg IoT Hub [üzenet-útválasztással](iot-hub-devguide-messages-d2c.md).

* További tudnivalók a [felhőből az eszközökre IoT hub üzenetkezelésről](iot-hub-devguide-messages-c2d.md).