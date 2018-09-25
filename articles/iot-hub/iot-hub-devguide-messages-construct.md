---
title: Megismerheti az Azure IoT Hub üzenet formátuma |} A Microsoft Docs
description: Fejlesztői útmutató – descibes formátuma és az IoT Hub-üzenetek várt tartalom.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: edea20343c2a261902c082dbc5c96b78db6b470d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973219"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-üzenetek létrehozása és olvasása

Zökkenőmentes együttműködés támogatására protokollok között, az IoT Hub egy közös üzenetformátum minden eszköz által használt protokollra határozza meg. Mindkét üzenetformátuma szolgál [eszközről a felhőbe – az Útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) és [felhőből az eszközre](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-c2d) üzeneteket. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub valósítja meg az eszköz-felhő üzenetkezelési streamelési üzenetkezelési minta használatával. Az IoT Hub eszköz – felhő üzeneteket is több, mint például [az Event Hubs](https://docs.microsoft.com/azure/event-hubs/) *események* mint [a Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/) *üzenetek* abban, hogy van egy nagy mennyiségű az események haladnak át a szolgáltatást, amely több olvasók által is olvasható.

IoT Hub-üzenetet áll:
* Egy előre meghatározott készletét *Rendszertulajdonságok* alábbi.
* Egy *alkalmazástulajdonságok*. Egy szótárt az alkalmazás meghatározó karakterlánc-tulajdonságok és a hozzáférés, anélkül, hogy az üzenet törzse deszerializálni. Az IoT Hub soha nem módosítja ezeket a tulajdonságokat.
* Egy nem átlátszó bináris törzse.

Tulajdonság olyan neveket és értékeket csak ASCII alfanumerikus karaktereket tartalmazhat, emellett ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` mikor küldhet eszköz – felhő üzenetek a HTTPS protokollt, vagy üzenetküldés a felhőből az eszközre.

Eszköz-felhő üzenetkezelést biztosít az IoT Hub a következő jellemzőkkel rendelkezik:

* Eszköz – felhő üzenetek, tartós és a egy IoT-központ alapértelmezett megőrzött **üzenetek/események** végpont legfeljebb 7 napig.
* Eszköz – felhő üzeneteket is lehet, legfeljebb 256 KB, és kötegekben küld optimalizálása érdekében csoportosíthatók. Kötegek legfeljebb 256 KB-os lehet.
* Az IoT Hub nem teszi lehetővé tetszőleges particionálás. Eszköz – felhő üzenetek vannak particionálva, azok származó alapján **deviceId**.
* Csatornára. További információ a [férhet hozzá az IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) szakaszban, az IoT Hub lehetővé teszi, hogy az eszközönkénti hitelesítés és a hozzáférés-vezérlés.

Kódolhat és dekódolhat különböző protokollok használatával küldött üzenetek kapcsolatos további információkért lásd: [Azure IoT SDK-k](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Az alábbi táblázat az IoT Hub-üzenetek a rendszer tulajdonságai készletét.

| Tulajdonság | Leírás | Az felhasználó állítható be? |
| --- | --- | --- |
| MessageId |Egy felhasználó állítható kérés-válasz mintákat keressen az üzenet azonosítója. Formátum: A kis-és nagybetűket (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karakterekből álló karakterlánc + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Igen |
| Sorozat száma |Minden egyes felhőből az eszközre irányuló üzenetek IoT Hub által hozzárendelt szám (soronként eszköz egyedi). | A nem C2D üzenetek; Ellenkező esetben igen. |
| Művelet |[Felhőbe – az eszközre irányuló] [lnk-c2d] meghatározott cél. | A nem C2D üzenetek; Ellenkező esetben igen. |
| ExpiryTimeUtc |Dátum és idő, az üzenetek lejáratkor. | Igen |
| EnqueuedTime |Dátum és idő a [felhőből az eszközre irányuló] [lnk-c2d] üzenet érkezett az IoT Hub. | A nem C2D üzenetek; Ellenkező esetben igen. |
| CorrelationId |A válaszüzenetben általában az üzenetazonosító, a kérelem, a kérés-válasz mintákat tartalmazó karakterlánc típusú tulajdonság. | Igen |
| Felhasználói azonosító |Adja meg a forrás, az üzenetek használt azonosító. Az IoT Hub által előállított üzeneteket, ha van-e állítva `{iot hub name}`. | Nem |
| Nyomon követés |Egy visszajelzés üzenet generátort. Ez a tulajdonság használják a felhőből az eszközre irányuló üzenetek létrehozni az üzenet a használatalapú eredményeként visszajelzés üzeneteket az IoT Hub kérése az eszköz. A lehetséges értékek: **nincs** (alapértelmezett): Nincs visszajelzés üzenet jön létre, **pozitív**: visszajelzés üzenetet kap, ha az üzenet fejeződött be, **negatív**: kap egy visszajelzési üzenetek anélkül, hogy az eszköz végzi üzenet lejárt (vagy kézbesítések maximális száma elérte a) Ha vagy **teljes**: pozitív és negatív. További információkért lásd: [üzenet visszajelzés] [lnk-visszajelzés]. | Igen |
| ConnectionDeviceId |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított azonosító. Tartalmazza a **deviceId** az eszköz, amely elküldte az üzenetet. | A nem D2C-messages; Ellenkező esetben igen. |
| ConnectionDeviceGenerationId |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított azonosító. Tartalmazza a **generationId** gyakorisághoz ([identitás properties][lnk-device-properties]) eszköz az eszköz, amely elküldte az üzenetet. | A nem D2C-messages; Ellenkező esetben igen. |
| ConnectionAuthMethod |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított hitelesítési módszert. Ez a tulajdonság az üzenet elküldésekor az eszköz hitelesítésére használt hitelesítési módszert kapcsolatos információt tartalmazza. További információkért lásd: [eszköz – felhő, hamisításszűrés] [lnk-antispoofing]. | A nem D2C-messages; Ellenkező esetben igen. |
| CreationTimeUtc | Létrehozásának dátuma és időpontja az üzenet az eszközön. Egy eszköz explicit módon be kell ezt az értéket. | Igen |

## <a name="message-size"></a>Üzenet mérete

Az IoT Hub méri üzenet mérete a protokoll-független módon csak a tényleges tartalom a mérlegeli. A mérete (bájt) a következő összegeként számítható ki:

* A törzs mérete (bájt).
* A mérete (bájt) összes értékét a üzenet tulajdonságai.
* A mérete (bájt) az összes felhasználó nevét és értékeit.

Nevét és értékeit pedig csak ASCII-karaktereket, így az a karakterlánc hosszának értéke a mérete (bájt).

## <a name="anti-spoofing-properties"></a>Hamisításszűrés tulajdonságai

Címek hamisítását az eszköz – felhő üzeneteket, az IoT Hub eszköz elkerülése érdekében minden stampek üzenetekre, az alábbi tulajdonságokkal:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Az első két tartalmazzák a **deviceId** és **generationId** az eredeti eszköz megfelelően [identitás eszköztulajdonságok](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#device-identity-properties).

A **ConnectionAuthMethod** tulajdonságot tartalmaz egy JSON-szerializált objektumot, a következő tulajdonságokkal:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>További lépések

* Az IoT Hub üzenetek méretkorlátjának kapcsolatos információkért lásd: [IoT Hub kvótái és szabályozása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-quotas-throttling).
* Ismerje meg, hogyan hozhat létre, és az IoT Hub, az üzenetek olvasásakor a különböző programozási nyelvek, tekintse meg a [útmutatóink](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node).