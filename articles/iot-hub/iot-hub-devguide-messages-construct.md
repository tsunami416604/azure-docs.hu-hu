---
title: Megismerheti az Azure IoT Hub üzenet formátuma |} A Microsoft Docs
description: Fejlesztői útmutató – descibes formátuma és az IoT Hub-üzenetek várt tartalom.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 7c08848698f07d64bbbff429682c18525659f7bf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286517"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-üzenetek létrehozása és olvasása

Zökkenőmentes együttműködés támogatására protokollok között, az IoT Hub egy közös üzenetformátum minden eszköz által használt protokollra határozza meg. Az üzenet formátuma is szolgál [eszközről a felhőbe] [ lnk-d2c] és [felhőből az eszközre] [ lnk-c2d] üzeneteket. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Egy [az IoT Hub üzenet] [ lnk-messaging] áll:

* Egy előre meghatározott készletét *Rendszertulajdonságok* alábbi.
* Egy *alkalmazástulajdonságok*. Egy szótárt az alkalmazás meghatározó karakterlánc-tulajdonságok és a hozzáférés, anélkül, hogy az üzenet törzse deszerializálni. Az IoT Hub soha nem módosítja ezeket a tulajdonságokat.
* Egy nem átlátszó bináris törzse.

Nevét és értékeit csak ASCII alfanumerikus karaktereket tartalmazhat, emellett ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` amikor Ön:  

* A HTTPS protokoll használatával eszköz a felhőbe irányuló üzenetek küldéséhez.
* Üzenetküldés a felhőből az eszközre.

Kódolhat és dekódolhat különböző protokollok használatával küldött üzenetek kapcsolatos további információkért lásd: [Azure IoT SDK-k][lnk-sdks].

Az alábbi táblázat az IoT Hub-üzenetek a rendszer tulajdonságai készletét.

| Tulajdonság | Leírás | Az felhasználó állítható be? |
| --- | --- | --- |
| MessageId |Egy felhasználó állítható kérés-válasz mintákat keressen az üzenet azonosítója. Formátum: A kis-és nagybetűket (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karakterekből álló karakterlánc + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Igen |
| Sorozat száma |Minden egyes felhőből az eszközre irányuló üzenetek IoT Hub által hozzárendelt szám (soronként eszköz egyedi). | A nem C2D üzenetek; Ellenkező esetben igen. |
| Művelet |A megadott cél [felhőből az eszközre] [ lnk-c2d] üzeneteket. | A nem C2D üzenetek; Ellenkező esetben igen. |
| ExpiryTimeUtc |Dátum és idő, az üzenetek lejáratkor. | Igen |
| EnqueuedTime |Dátum és idő a [felhőből az eszközre] [ lnk-c2d] által az IoT Hub-üzenet érkezett. | A nem C2D üzenetek; Ellenkező esetben igen. |
| CorrelationId |A válaszüzenetben általában az üzenetazonosító, a kérelem, a kérés-válasz mintákat tartalmazó karakterlánc típusú tulajdonság. | Igen |
| Felhasználói azonosító |Adja meg a forrás, az üzenetek használt azonosító. Az IoT Hub által előállított üzeneteket, ha van-e állítva `{iot hub name}`. | Nem |
| Nyomon követés |Egy visszajelzés üzenet generátort. Ez a tulajdonság használják a felhőből az eszközre irányuló üzenetek létrehozni az üzenet a használatalapú eredményeként visszajelzés üzeneteket az IoT Hub kérése az eszköz. A lehetséges értékek: **nincs** (alapértelmezett): Nincs visszajelzés üzenet jön létre, **pozitív**: visszajelzés üzenetet kap, ha az üzenet fejeződött be, **negatív**: kap egy visszajelzési üzenetek anélkül, hogy az eszköz végzi üzenet lejárt (vagy kézbesítések maximális száma elérte a) Ha vagy **teljes**: pozitív és negatív. További információkért lásd: [visszajelzés üzenet][lnk-feedback]. | Igen |
| ConnectionDeviceId |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított azonosító. Tartalmazza a **deviceId** az eszköz, amely elküldte az üzenetet. | A nem D2C-messages; Ellenkező esetben igen. |
| ConnectionDeviceGenerationId |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított azonosító. Tartalmazza a **generationId** (megfelelően [identitás eszköztulajdonságok][lnk-device-properties]) az eszköz, amely elküldte az üzenetet. | A nem D2C-messages; Ellenkező esetben igen. |
| ConnectionAuthMethod |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított hitelesítési módszert. Ez a tulajdonság az üzenet elküldésekor az eszköz hitelesítésére használt hitelesítési módszert kapcsolatos információt tartalmazza. További információkért lásd: [eszköz – felhő hamisításszűrés][lnk-antispoofing]. | A nem D2C-messages; Ellenkező esetben igen. |
| CreationTimeUtc | Létrehozásának dátuma és időpontja az üzenet az eszközön. Egy eszköz explicit módon be kell ezt az értéket. | Igen |

## <a name="message-size"></a>Üzenet mérete

Az IoT Hub méri üzenet mérete a protokoll-független módon csak a tényleges tartalom a mérlegeli. A mérete (bájt) a következő összegeként számítható ki:

* A törzs mérete (bájt).
* A mérete (bájt) összes értékét a üzenet tulajdonságai.
* A mérete (bájt) az összes felhasználó nevét és értékeit.

Nevét és értékeit pedig csak ASCII-karaktereket, így az a karakterlánc hosszának értéke a mérete (bájt).

## <a name="next-steps"></a>További lépések

Az IoT Hub üzenetek méretkorlátjának kapcsolatos információkért lásd: [IoT Hub kvótái és szabályozása][lnk-quotas].

Ismerje meg, hogyan hozhat létre, és az IoT Hub, az üzenetek olvasásakor a különböző programozási nyelvek, tekintse meg a [útmutatóink][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
