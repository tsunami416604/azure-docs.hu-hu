---
title: Az Azure IoT Hub üzenetküldése | Microsoft dokumentumok
description: Fejlesztői útmutató – eszközről felhőbe és felhőből az eszközre irányuló üzenetküldés az IoT Hubbal. Az üzenetformátumokról és a támogatott kommunikációs protokollokról tartalmaz információkat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60626247"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Eszközről felhőbe és felhőbe irányuló üzenetek küldése az IoT Hubbal

Az IoT Hub lehetővé teszi a kétirányú kommunikációt az eszközökkel. Az IoT Hub-üzenetküldés segítségével kommunikálhat az eszközökkel azáltal, hogy üzeneteket küld az eszközökről a megoldások háttér-kezelőszámára, és parancsokat küld az IoT-megoldások háttér-kezelőjéből az eszközökre. További információ az [IoT Hub üzenetformátumáról.](iot-hub-devguide-messages-construct.md)

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Eszközről felhőbe irányuló üzenetek küldése az IoT Hubba

Az IoT Hub rendelkezik egy beépített szolgáltatás-végpont, amely a háttér-szolgáltatások segítségével telemetriai üzeneteket az eszközökről olvasni. Ez a végpont kompatibilis az [Event Hubs-szal,](https://docs.microsoft.com/azure/event-hubs/) és szabványos IoT Hub SDK-k használatával [olvashat erről a beépített végpontról.](iot-hub-devguide-messages-read-builtin.md)

Az IoT Hub támogatja [azokat az egyéni végpontokat](iot-hub-devguide-endpoints.md#custom-endpoints) is, amelyeket a felhasználók meghatározhatnak az eszköz telemetriai adatainak és eseményeinek az Azure-szolgáltatásokba [üzenettovábbítás](iot-hub-devguide-messages-d2c.md)használatával történő küldéséhez.

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Felhőből eszközre irányuló üzenetek küldése az IoT Hubról

Felhőből [az eszközre](iot-hub-devguide-messages-c2d.md) irányuló üzeneteket küldhet a megoldás háttértartalékából az eszközökre.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub üzenetkezelési funkcióinak alapvető tulajdonságai az üzenetek megbízhatósága és tartóssága. Ezek a tulajdonságok lehetővé teszik az eszköz oldalán lévő időszakos kapcsolat rugalmasságát, és a felhőbeli oldalon lévő eseményfeldolgozási csúcsok betöltését. Az IoT Hub *legalább egyszer* megvalósítja a kézbesítési garanciákat mind az eszközről a felhőbe, mind a felhőből az eszközre irányuló üzenetküldéshez.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Az IoT Hub-üzenetek megfelelő típusának kiválasztása

Az eszközről a felhőbe irányuló üzeneteksegítségével idősorozat-telemetriai adatokat és riasztásokat küldhet az eszközalkalmazásból, valamint felhőből az eszközre irányuló üzeneteket az eszközalkalmazásba.

* Tekintse meg [az eszközről a felhőbe irányuló kommunikációra vonatkozó útmutatást](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) az eszközről a felhőbe irányuló üzenetek, a jelentett tulajdonságok vagy a fájlfeltöltés között.

* Tekintse meg a felhőből az [eszközre irányuló kommunikációra vonatkozó útmutatást](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) a felhőből az eszközre irányuló üzenetek, a kívánt tulajdonságok vagy a közvetlen módszerek közül.

## <a name="next-steps"></a>További lépések

* További információ az IoT Hub [üzenet-útválasztásáról.](iot-hub-devguide-messages-d2c.md)

* További információ az IoT Hub [felhőből az eszközökre történő üzenetküldéséről.](iot-hub-devguide-messages-c2d.md)