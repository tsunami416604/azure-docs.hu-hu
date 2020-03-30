---
title: Az Azure IoT Hub üzenetformátumának ismertetése | Microsoft dokumentumok
description: Fejlesztői útmutató – ismerteti az IoT Hub-üzenetek formátumát és várható tartalmát.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69872488"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-üzenetek létrehozása és olvasása

A protokollok közötti zökkenőmentes együttműködés támogatása érdekében az IoT Hub közös üzenetformátumot határoz meg az összes eszközfelé néző protokollhoz. Ez az üzenetformátum az eszközről a [felhőbe irányuló útválasztáshoz](iot-hub-devguide-messages-d2c.md) és a [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) irányuló üzenetekhez is használatos. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub egy streamelési üzenetkezelési minta használatával valósítja meg az eszközről a felhőbe irányuló üzenetküldést. Az IoT Hub eszközről felhőbe irányuló üzenetei inkább [az Event Hubs-eseményekhez hasonlítanak,](/azure/event-hubs/) *events* mint a Service *Bus-üzenetek,* mivel a szolgáltatáson áthaladó események nagy száma több olvasó számára is olvasható. [Service Bus](/azure/service-bus-messaging/)

Az IoT Hub-üzenet a következőkből áll:

* A *rendszertulajdonságok* előre meghatározott készlete az alábbiak szerint.

* Alkalmazástulajdonságok *application properties*készlete . Karakterlánc-tulajdonságok szótára, amelyet az alkalmazás definiálhat és elérhet anélkül, hogy deszerializálnia kellene az üzenettörzset. Az IoT Hub soha nem módosítja ezeket a tulajdonságokat.

* Átlátszatlan bináris törzs.

A tulajdonságnevek és értékek csak ASCII alfanumerikus karaktereket tartalmazhatnak, valamint ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` akkor, ha a HTTPS protokoll használatával eszközről a felhőbe irányuló üzeneteket küld, vagy felhőből eszközre irányuló üzeneteket küld.

Az IoT Hubbal az eszközök közötti üzenetek a következő jellemzőkkel rendelkeznek:

* Az eszközök közötti üzenetek tartósak, és legfeljebb hét napig megmaradnak az IoT-központ alapértelmezett **üzenet-/eseményvégpontjában.**

* Az eszközről a felhőbe irányuló üzenetek legbőlegelhetők legbőlegelhetők lehetnek 256 KB, és kötegekbe csoportosíthatók a küldés optimalizálása érdekében. A kötegek legbőlegelhetőek lehetnek: 256 KB.

* Az IoT Hub nem engedélyezi az tetszőleges particionálást. Az eszközről a felhőbe irányuló üzenetek particionálva vannak az eredeti **eszközazonosítójuk**alapján.

* Az [IoT Hubhoz való hozzáférés vezérlése](iot-hub-devguide-security.md)című részen leírtak szerint az IoT Hub eszközenkénti hitelesítést és hozzáférés-vezérlést tesz lehetővé.

* Az üzeneteket az alkalmazás tulajdonságaiba kerülő információkkal pecsételheti le. További információt az [üzenetgazdagítások](iot-hub-message-enrichments-overview.md)című témakörben talál.

A különböző protokollok használatával küldött üzenetek kódolásáról és dekódolásáról az [Azure IoT SDK-k](iot-hub-devguide-sdks.md)című témakörben talál további információt.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>A **D2C** IoT Hub-üzenetek rendszertulajdonságai

| Tulajdonság | Leírás  |Felhasználói beállítás?|Kulcsszó </br>műveletterv lekérdezés|
| --- | --- | --- | --- |
| üzenet-azonosító |A kérelem-válasz mintákhoz használt üzenet felhasználó által beállított azonosítója. Formátum: ASCII 7 bites alfanumerikus karakterek + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`kis- és nagybetűket megkülönböztető karakterlánca (legfeljebb 128 karakter hosszú).  | Igen | messageId |
| iothub-enqueuedtime |Az [Eszközről a felhőbe](iot-hub-devguide-d2c-guidance.md) irányuló üzenet az IoT Hub általi fogadásának dátuma és időpontja. | Nem | enqueuedTime |
| felhasználói azonosító |Az üzenetek eredetének megadására használt azonosító. Amikor az üzeneteket az IoT Hub `{iot hub name}`hozza létre, a . | Igen | userId |
| iothub-kapcsolat-eszköz-id |Az IoT Hub által beállított azonosító az eszközről a felhőbe irányuló üzeneteken. Az üzenetet küldő eszköz **azonosítóját** tartalmazza. | Nem | connectionDeviceId |
| iothub-connection-module-id |Az IoT Hub által beállított azonosító az eszközről a felhőbe irányuló üzeneteken. Az üzenetet küldő eszköz **modulazonosítóját** tartalmazza. | Nem | connectionModuleId |
| iothub-connection-auth-generation-id |Az IoT Hub által beállított azonosító az eszközről a felhőbe irányuló üzeneteken. Ez tartalmazza a **connectionDeviceGenerationId** (a per [device identity tulajdonságok)](iot-hub-devguide-identity-registry.md#device-identity-properties)az eszköz, amely az üzenetet küldte. | Nem |connectionDeviceGenerationId |
| iothub-connection-auth-módszer |Az IoT Hub által az eszközről a felhőbe irányuló üzeneteken beállított hitelesítési módszer. Ez a tulajdonság az üzenetet küldő eszköz hitelesítési módjáról tartalmaz információkat.| Nem | connectionAuthMetódus |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>A **C2D** IoT Hub-üzenetek rendszertulajdonságai

| Tulajdonság | Leírás  |Felhasználói beállítás?|
| --- | --- | --- |
| üzenet-azonosító |A kérelem-válasz mintákhoz használt üzenet felhasználó által beállított azonosítója. Formátum: ASCII 7 bites alfanumerikus karakterek + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`kis- és nagybetűket megkülönböztető karakterlánca (legfeljebb 128 karakter hosszú).  |Igen|
| sorszám |Az IoT Hub által az egyes felhőből az eszközre irányuló üzenetekhez rendelt szám (eszközvárólistánként egyedi). |Nem|
| erre: |[A felhőből az eszközre](iot-hub-devguide-c2d-guidance.md) irányuló üzenetekben megadott cél. |Nem|
| abszolút lejárati idő |Az üzenet lejáratának dátuma és időpontja. |Nem|   |
| korrelációs azonosító |A válaszüzenet karakterlánc-tulajdonsága, amely általában a kérés MessageId azonosítóját tartalmazza a kérés-válasz mintákban. |Igen|
| felhasználói azonosító |Az üzenetek eredetének megadására használt azonosító. Amikor az üzeneteket az IoT Hub `{iot hub name}`hozza létre, a . |Igen|
| iothub-ack |Egy visszacsatolási üzenet generátor. Ez a tulajdonság felhőből az eszközre irányuló üzenetekben használatos az IoT Hub visszajelzési üzenetek létrehozásához az eszköz általi használat eredményeként. Lehetséges értékek: **nincs** (alapértelmezett): nincs visszajelzési üzenet, **pozitív:** visszajelzési üzenetet kap, ha az üzenet befejeződött, **negatív:** visszajelzési üzenetet kap, ha az üzenet lejárt (vagy elérte a maximális kézbesítési számot) anélkül, hogy az eszköz befejezte volna, vagy **teljes**: pozitív és negatív. |Igen|

## <a name="message-size"></a>Üzenet mérete

Az IoT Hub protokollfüggetlen módon méri az üzenetek méretét, csak a tényleges hasznos terhet figyelembe véve. A bájtban lévő méretet a következő értékek összegeként számítja ki a program:

* A testméret bájtban.
* Az üzenetrendszer tulajdonságainak összes értékének bájtban lévő mérete.
* Az összes felhasználói tulajdonságnév és érték bájtban megadott mérete.

A tulajdonságnevek és értékek ASCII karakterekre korlátozódnak, így a karakterláncok hossza megegyezik a bájtban lévő mérettel.

## <a name="anti-spoofing-properties"></a>Hamisítás elleni tulajdonságok

Az eszköz-hamisítás elkerülése érdekében az eszközök közötti műveletek az eszközök közötti üzenetekben az IoT Hub a következő tulajdonságokkal rendelkező összes üzenetet lebélyegzi:

* **iothub-kapcsolat-eszköz-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-módszer**

Az első kettő tartalmazza az **eszközazonosítót** és a származó eszköz **generációsazonosítóját** az [eszköz identitásának tulajdonságai szerint.](iot-hub-devguide-identity-registry.md#device-identity-properties)

Az **iothub-connection-auth-method** tulajdonság egy JSON szerializált objektumot tartalmaz, amely a következő tulajdonságokkal rendelkezik:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>További lépések

* Az IoT Hub üzenetméret-korlátairól az [IoT Hub-kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)című témakörben talál.

* Az IoT Hub-üzenetek különböző programozási nyelveken történő létrehozásáról és olvasásáról a [rövid útmutatók című témakörben olvashat.](quickstart-send-telemetry-node.md)
