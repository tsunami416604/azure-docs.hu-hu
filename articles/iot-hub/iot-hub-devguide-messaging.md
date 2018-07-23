---
title: Megismerheti az Azure IoT Hub üzenetküldés |} A Microsoft Docs
description: Fejlesztői útmutató – eszközről a felhőbe és a felhőből az eszközre irányuló, az IoT Hub üzenetküldési. Üzenet-formátumok és a támogatott kommunikációs protokollok kapcsolatos információkat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: b0667f820145f16c75a07ebe1849e20d2de36cc7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185509"
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Eszközről a felhőbe és a felhőből az eszközre irányuló, az IoT Hub üzenetküldés

Használja az IoT Hub üzenetküldés az eszközök által folytatott kommunikációhoz:

* Küldő [eszközről a felhőbe] [ lnk-d2c] a megoldás az eszközökről származó üzenetek háttéralkalmazáshoz.
* Küldő [felhőből az eszközre] [ lnk-c2d] üzenetek a megoldás a háttéralkalmazás az eszközökön.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub üzenetküldési funkció alapvető tulajdonságok akkor, a megbízhatóság és a tartós üzeneteket. Ezek a tulajdonságok engedélyezése nem állandó hálózati kapcsolat az eszközoldalon, és a felhő oldalon Eseményfeldolgozási kiugrások betöltése hibatűrést. Az IoT Hub valósít meg *legalább egyszer* kézbesítési garantálja az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek.

Megismerni a funkciókat az IoT Hub, tekintse meg a [az Azure IoT Hub szolgáltatás áttekintése][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Mikor érdemes használni az IoT Hub üzenetküldés

A time series telemetriai adatokhoz és riasztásokhoz küldését az eszközalkalmazás eszköz – felhő üzeneteket, és a felhőből az eszközre irányuló üzenetek használata egyirányú értesítések az eszközalkalmazáshoz.

* Tekintse meg [eszközről a felhőbe való kommunikáció útmutatást] [ lnk-d2c-guidance] Ha kétségei vannak az eszköz – felhő üzeneteket, jelentett tulajdonságok vagy a fájl feltöltése között.
* Tekintse meg [felhőből az eszközre irányuló kommunikáció útmutatást] [ lnk-c2d-guidance] Ha bizonytalan a felhőből az eszközre irányuló üzenetek, kívánt tulajdonságok vagy közvetlen metódusok használata között.

## <a name="next-steps"></a>További lépések

* További tudnivalók az IoT Hub [eszköz – felhő üzenetek][lnk-d2c].
* További tudnivalók az IoT Hub [felhőből az eszközre irányuló üzenetküldés][lnk-c2d].

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: about-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md