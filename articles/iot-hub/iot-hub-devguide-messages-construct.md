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
ms.openlocfilehash: 164f3b8ef42d07606d98d200fa9bebcd0add3d38
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319574"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-üzenetek létrehozása és olvasása

Zökkenőmentes együttműködés támogatására protokollok között, az IoT Hub egy közös üzenetformátum minden eszköz által használt protokollra határozza meg. Mindkét üzenetformátuma szolgál [eszközről a felhőbe – az Útválasztás](iot-hub-devguide-messages-d2c.md) és [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) üzeneteket. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub valósítja meg az eszköz-felhő üzenetkezelési streamelési üzenetkezelési minta használatával. Az IoT Hub eszköz – felhő üzeneteket is több, mint például [az Event Hubs](/azure/event-hubs/) *események* mint [a Service Bus](/azure/service-bus-messaging/) *üzenetek* abban, hogy van egy nagy mennyiségű az események haladnak át a szolgáltatást, amely több olvasók által is olvasható.

IoT Hub-üzenetet áll:

* Egy előre meghatározott készletét *Rendszertulajdonságok* alábbi.

* Egy *alkalmazástulajdonságok*. Egy szótárt az alkalmazás meghatározó karakterlánc-tulajdonságok és a hozzáférés, anélkül, hogy az üzenet törzse deszerializálni. Az IoT Hub soha nem módosítja ezeket a tulajdonságokat.

* Egy nem átlátszó bináris törzse.

Tulajdonság olyan neveket és értékeket csak ASCII alfanumerikus karaktereket tartalmazhat, és `{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`"," |} "," ~ "}" mikor küldhet eszköz – felhő üzenetek a HTTPS protokollt, vagy üzenetküldés a felhőből az eszközre.

Eszköz-felhő üzenetkezelést biztosít az IoT Hub a következő jellemzőkkel rendelkezik:

* Eszköz – felhő üzenetek, tartós és a egy IoT-központ alapértelmezett megőrzött **üzenetek/események** végpont legfeljebb 7 napig.

* Eszköz – felhő üzeneteket is lehet, legfeljebb 256 KB, és kötegekben küld optimalizálása érdekében csoportosíthatók. Kötegek legfeljebb 256 KB-os lehet.

* Az IoT Hub nem teszi lehetővé tetszőleges particionálás. Eszköz – felhő üzenetek vannak particionálva, azok származó alapján **deviceId**.

* A [férhet hozzá az IoT Hub](iot-hub-devguide-security.md), IoT Hub lehetővé teszi az eszközönkénti hitelesítés és a hozzáférés-vezérlés.

Kódolhat és dekódolhat különböző protokollok használatával küldött üzenetek kapcsolatos további információkért lásd: [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Az alábbi táblázat az IoT Hub-üzenetek a rendszer tulajdonságai készletét.

| Tulajdonság | Leírás | Az felhasználó állítható be? |
| --- | --- | --- |
| üzenetazonosító |Egy felhasználó állítható kérés-válasz mintákat keressen az üzenet azonosítója. Formátum: A kis-és nagybetűket (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karakterekből álló karakterlánc + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Igen |
| a sorszám |Minden egyes felhőből az eszközre irányuló üzenetek IoT Hub által hozzárendelt szám (soronként eszköz egyedi). | A nem C2D üzenetek; Ellenkező esetben igen. |
| erre: |A megadott cél [felhőből az eszközre](iot-hub-devguide-c2d-guidance.md) üzeneteket. | A nem C2D üzenetek; Ellenkező esetben igen. |
| abszolút-lejárati-idő |Dátum és idő, az üzenetek lejáratkor. | Igen |
| iothub-enqueuedtime |Dátum és idő a [felhőből az eszközre](iot-hub-devguide-c2d-guidance.md) által az IoT Hub-üzenet érkezett. | A nem C2D üzenetek; Ellenkező esetben igen. |
| korrelációs azonosító |A válaszüzenetben általában az üzenetazonosító, a kérelem, a kérés-válasz mintákat tartalmazó karakterlánc típusú tulajdonság. | Igen |
| felhasználói azonosító |Adja meg a forrás, az üzenetek használt azonosító. Az IoT Hub által előállított üzeneteket, ha van-e állítva `{iot hub name}`. | Nem |
| iothub-ack |Egy visszajelzés üzenet generátort. Ez a tulajdonság használják a felhőből az eszközre irányuló üzenetek létrehozni az üzenet a használatalapú eredményeként visszajelzés üzeneteket az IoT Hub kérése az eszköz. A lehetséges értékek: **nincs** (alapértelmezett): Nincs visszajelzés üzenet jön létre, **pozitív**: visszajelzés üzenetet kap, ha az üzenet fejeződött be, **negatív**: kap egy visszajelzési üzenetek anélkül, hogy az eszköz végzi üzenet lejárt (vagy kézbesítések maximális száma elérte a) Ha vagy **teljes**: pozitív és negatív. 
<!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Igen |
| iothub-kapcsolat-eszközazonosító |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított azonosító. Tartalmazza a **deviceId** az eszköz, amely elküldte az üzenetet. | A nem D2C-messages; Ellenkező esetben igen. |
| iothub-kapcsolat – hitelesítés – Létrehozás-azonosítója |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított azonosító. Tartalmazza a **generationId** (megfelelően [identitás eszköztulajdonságok](iot-hub-devguide-identity-registry.md#device-identity-properties)) az eszköz, amely elküldte az üzenetet. | A nem D2C-messages; Ellenkező esetben igen. |
| iothub-kapcsolat – hitelesítés-metódus |Az eszköz a felhőbe irányuló üzeneteket az IoT Hub által beállított hitelesítési módszert. Ez a tulajdonság az üzenet elküldésekor az eszköz hitelesítésére használt hitelesítési módszert kapcsolatos információt tartalmazza. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | A nem D2C-messages; Ellenkező esetben igen. |
| iothub-létrehozási-time-utc | Létrehozásának dátuma és időpontja az üzenet az eszközön. Egy eszköz explicit módon be kell ezt az értéket. | Igen |

## <a name="message-size"></a>Üzenet mérete

Az IoT Hub méri üzenet mérete a protokoll-független módon csak a tényleges tartalom a mérlegeli. A mérete (bájt) a következő összegeként számítható ki:

* A törzs mérete (bájt).
* A mérete (bájt) összes értékét a üzenet tulajdonságai.
* A mérete (bájt) az összes felhasználó nevét és értékeit.

Nevét és értékeit pedig csak ASCII-karaktereket, így az a karakterlánc hosszának értéke a mérete (bájt).

## <a name="anti-spoofing-properties"></a>Hamisításszűrés tulajdonságai

Címek hamisítását az eszköz – felhő üzeneteket, az IoT Hub eszköz elkerülése érdekében minden stampek üzenetekre, az alábbi tulajdonságokkal:

* **iothub-kapcsolat-eszközazonosító**
* **iothub-kapcsolat – hitelesítés – Létrehozás-azonosítója**
* **iothub-kapcsolat – hitelesítés-metódus**

Az első két tartalmazzák a **deviceId** és **generationId** az eredeti eszköz megfelelően [identitás eszköztulajdonságok](iot-hub-devguide-identity-registry.md#device-identity-properties).

A **iothub-kapcsolat – hitelesítés-method** tulajdonságot tartalmaz egy JSON-szerializált objektumot, a következő tulajdonságokkal:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>További lépések

* Az IoT Hub üzenetek méretkorlátjának kapcsolatos információkért lásd: [IoT Hub kvótái és szabályozása](iot-hub-devguide-quotas-throttling.md).

* Ismerje meg, hogyan hozhat létre, és az IoT Hub, az üzenetek olvasásakor a különböző programozási nyelvek, tekintse meg a [útmutatóink](quickstart-send-telemetry-node.md).
