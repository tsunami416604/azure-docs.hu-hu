---
title: Az Azure IoT Hub üzenet formátumának ismertetése | Microsoft Docs
description: Fejlesztői útmutató – ismerteti IoT Hub üzenetek formátumát és várt tartalmát.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "69872488"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-üzenetek létrehozása és olvasása

A protokollok közötti zökkenőmentes együttműködési képesség támogatása érdekében a IoT Hub az összes eszközre irányuló protokoll közös üzenet-formátumát határozza meg. Ez az üzenet formátuma az [eszközről a felhőbe](iot-hub-devguide-messages-d2c.md) irányuló Útválasztás és a [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) irányuló üzenetek esetében is használatos. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub az eszközről a felhőbe irányuló üzenetküldést adatfolyam-üzenetküldési minta használatával valósítja meg. A IoT Hub eszközről a felhőbe irányuló üzenetek többek között a [Service Bus](/azure/service-bus-messaging/) *üzenetekben* [Event Hubs](/azure/event-hubs/) *események* , így nagy mennyiségű esemény halad át a szolgáltatáson keresztül, amely több olvasó által is olvasható.

Egy IoT Hub üzenet a következőkből áll:

* A *Rendszertulajdonságok* előre meghatározott készlete az alábbi listában.

* Az *alkalmazás tulajdonságainak*halmaza. Az alkalmazás által definiálható és elérhető karakterlánc-tulajdonságok szótára anélkül, hogy a deszerializálni kellene az üzenet törzsét. IoT Hub soha nem módosítja ezeket a tulajdonságokat.

* Egy átlátszatlan bináris törzs.

A tulajdonságnév és az értékek csak ASCII alfanumerikus karaktereket tartalmazhatnak, ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` és ha az eszközről a felhőbe irányuló üzeneteket a HTTPS protokoll használatával küldi el, vagy a felhőből az eszközre irányuló üzeneteket küld.

Az eszközről a felhőbe irányuló üzenetküldés IoT Hub a következő jellemzőkkel rendelkezik:

* Az eszközről a felhőbe irányuló üzenetek tartósak, és az IoT hub alapértelmezett **üzenetek/események** végpontján belül akár hét napig is megmaradnak.

* Az eszközről a felhőbe irányuló üzenetek legfeljebb 256 KB méretűek lehetnek, és kötegekben csoportosíthatók a Küldés optimalizálása érdekében. A kötegek legfeljebb 256 KB méretűek lehetnek.

* A IoT Hub nem teszi lehetővé az tetszőleges particionálást. Az eszközről a felhőbe irányuló üzenetek particionálása a származó **deviceId**alapján történik.

* Ahogy azt a [IoT hub hozzáférésének vezérlése](iot-hub-devguide-security.md)című részben leírtak szerint, IoT hub engedélyezi az eszközönkénti hitelesítést és a hozzáférés-vezérlést.

* Az üzenetek az alkalmazás tulajdonságaiba bekerülő adatokkal is lebélyegzettek. További információ: [üzenet-gazdagítás](iot-hub-message-enrichments-overview.md).

További információ a különböző protokollok használatával elküldött üzenetek kódolásáról és dekódolásáról: [Azure IoT SDK](iot-hub-devguide-sdks.md)-k.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT hub üzenetek rendszertulajdonságai

| Tulajdonság | Leírás  |A felhasználó beállítható?|Kulcsszó a következőhöz: </br>útválasztási lekérdezés|
| --- | --- | --- | --- |
| üzenet-azonosító |A kérelem-válasz mintákhoz használt üzenet felhasználó által beállítható azonosítója. Format: kis-és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karakterek + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  | Igen | messageId |
| iothub – enqueuedtime |Az [eszközről a felhőbe](iot-hub-devguide-d2c-guidance.md) irányuló üzenet IoT hub általi fogadásának dátuma és időpontja. | Nem | enqueuedTime |
| felhasználói azonosító |Az üzenetek eredetének meghatározására szolgáló azonosító. Ha IoT Hub hozza létre az üzeneteket, az értéke a `{iot hub name}`következő lesz:. | Igen | userId |
| iothub--összekötő-eszköz-azonosító |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított azonosító. Tartalmazza az üzenetet küldő eszköz **deviceId** -számát. | Nem | connectionDeviceId |
| iothub – összekapcsolási modul azonosítója |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított azonosító. Az üzenetet küldő eszköz **moduleId** tartalmazza. | Nem | connectionModuleId |
| iothub – kapcsolatok – hitelesítés – generálási azonosító |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított azonosító. Az üzenetet küldő eszköz **connectionDeviceGenerationId** (az [eszköz identitásának tulajdonságai](iot-hub-devguide-identity-registry.md#device-identity-properties)alapján) tartalmazza. | Nem |connectionDeviceGenerationId |
| iothub – kapcsolatok – hitelesítés – módszer |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított hitelesítési módszer. Ez a tulajdonság az üzenetet küldő eszköz hitelesítéséhez használt hitelesítési módszerről tartalmaz információkat.| Nem | connectionAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>**C2D** IoT hub üzenetek rendszertulajdonságai

| Tulajdonság | Leírás  |A felhasználó beállítható?|
| --- | --- | --- |
| üzenet-azonosító |A kérelem-válasz mintákhoz használt üzenet felhasználó által beállítható azonosítója. Format: kis-és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karakterek + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  |Igen|
| sorozatszám |Az egyes felhőből az eszközre irányuló üzenetek IoT Hub által hozzárendelt szám (egyedi eszközönként). |Nem|
| erre: |A [felhőből az eszközre irányuló](iot-hub-devguide-c2d-guidance.md) üzenetekben megadott célhely. |Nem|
| abszolút – lejárat – idő |Az üzenetek lejáratának dátuma és időpontja. |Nem|   |
| korrelációs azonosító |Egy válaszüzenet karakterlánc-tulajdonsága, amely általában tartalmazza a kérelem MessageId a kérelem-válasz mintákban. |Igen|
| felhasználói azonosító |Az üzenetek eredetének meghatározására szolgáló azonosító. Ha IoT Hub hozza létre az üzeneteket, az értéke a `{iot hub name}`következő lesz:. |Igen|
| iothub-ACK |Visszajelzési üzenet generátora. Ez a tulajdonság a felhőből az eszközre irányuló üzenetekben használatos, hogy az üzenetnek az eszköz általi felhasználásának eredményeképpen a visszajelzési üzenetek létrehozásához IoT Hub kérjenek. Lehetséges értékek: **nincs (alapértelmezett): nem** jön létre visszajelzési üzenet **, pozitív**: visszajelzési üzenet küldése, ha az üzenet elkészült, **negatív**: visszajelzési üzenet küldése, ha az üzenet lejárt (vagy elérte a maximális kézbesítési értéket) az eszköz befejezése nélkül, vagy **teljes**: pozitív és negatív. |Igen|

## <a name="message-size"></a>Üzenet mérete

A IoT Hub az üzenetek méretét a protokolltól független módon méri, és csak a tényleges adattartalmat veszi figyelembe. A bájtban megadott méret kiszámítása a következő értékek összegeként történik:

* A törzs mérete bájtban megadva.
* Az üzenetrendszer-tulajdonságok összes értékének bájtban kifejezett mérete.
* A felhasználói tulajdonságok neveinek és értékeinek mérete bájtban megadva.

A tulajdonságok nevei és értékei csak ASCII-karakterekre korlátozódnak, így a karakterláncok hossza bájtban kifejezve.

## <a name="anti-spoofing-properties"></a>Hamisítás elleni tulajdonságok

Az eszközről a felhőbe irányuló üzenetek hamisításának elkerülése érdekében IoT Hub Stamp összes üzenetet a következő tulajdonságokkal:

* **iothub--összekötő-eszköz-azonosító**
* **iothub – kapcsolatok – hitelesítés – generálási azonosító**
* **iothub – kapcsolatok – hitelesítés – módszer**

Az első kettő tartalmazza a kezdeményező eszköz **deviceId** -és **GenerationId** , az [eszköz azonosító tulajdonságai](iot-hub-devguide-identity-registry.md#device-identity-properties)alapján.

A **iothub-kapcsolat-Auth-Method** tulajdonság JSON szerializált objektumot tartalmaz, a következő tulajdonságokkal:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>További lépések

* További információ a IoT Hub üzenet méretének korlátairól: [IoT hub kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md).

* Ha meg szeretné tudni, hogyan hozhat létre és olvashat IoT Hub üzeneteket különböző programozási nyelveken, tekintse [meg a gyors](quickstart-send-telemetry-node.md)útmutatókat.
