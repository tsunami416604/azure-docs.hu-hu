---
title: Megismerheti az Azure IoT Hub eszköz – felhő üzenetek |} A Microsoft Docs
description: Fejlesztői útmutató – hogyan használhatja az IoT Hub üzenetküldési eszközről a felhőbe. A telemetriai adatokat, és nem telemtry adatok küldése és kézbesíti az üzeneteket az Útválasztás használatával kapcsolatos információkat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: be87b00f27f0d0b25cd77a0634ab1c653a85e5ac
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126442"
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Eszköz – felhő üzenetek küldése az IoT hubhoz

Idősorozat-telemetria és riasztások küldése az eszközökről a megoldás háttérrendszerének, eszköz – felhő üzeneteket küldhet a az eszközt az IoT hubnak. IoT Hub által támogatott más eszköz – felhő közül, lásd: [eszközt a felhőbe irányuló kommunikáció útmutatóját][lnk-d2c-guidance].

Eszköz elérhető végponton keresztüli eszköz – felhő üzeneteket küld (**/devices/ {deviceId} / üzenetek/események**). Útválasztási szabályok, majd az üzenetek átirányítása egy, a szolgáltatás felé néző végpontok az IoT hubon. Útválasztási szabályokat a fejlécek és törzs az eszköz a felhőbe irányuló üzenetek használatával irányíthatja őket helyének meghatározása. Alapértelmezés szerint üzenetek irányíthatók át a beépített szolgáltatás felé néző végpont (**üzenetek/események**), azaz kompatibilis [az Event Hubs][lnk-event-hubs]. Ezért használhat standard [Event Hubs-integráción és SDK-k] [ lnk-compatible-endpoint] a megoldás háttérrendszere az eszközről a felhőbe – az üzenetek fogadásához.

Az IoT Hub valósítja meg az eszköz-felhő üzenetkezelési streamelési üzenetkezelési minta használatával. Az IoT Hub eszköz – felhő üzeneteket is több, mint például [az Event Hubs] [ lnk-event-hubs] *események* mint [a Service Bus] [ lnk-servicebus] *üzenetek* abban, hogy van egy nagy mennyiségű esemény haladnak át a szolgáltatást, amely több olvasók által is olvasható.

Eszköz-felhő üzenetkezelést biztosít az IoT Hub a következő jellemzőkkel rendelkezik:

* Eszköz – felhő üzenetek, tartós és a egy IoT-központ alapértelmezett megőrzött **üzenetek/események** végpont legfeljebb 7 napig.
* Eszköz – felhő üzeneteket is lehet, legfeljebb 256 KB, és kötegekben küld optimalizálása érdekében csoportosíthatók. Kötegek legfeljebb 256 KB-os lehet.
* Csatornára. További információ a [férhet hozzá az IoT Hub] [ lnk-devguide-security] szakaszban, az IoT Hub lehetővé teszi, hogy az eszközönkénti hitelesítés és a hozzáférés-vezérlés.
* Az IoT Hub legfeljebb 10 egyéni végpontok létrehozását teszi lehetővé. Üzenetek érkeznek alapján az IoT hubot konfigurált útvonalak a végpontok. További információkért lásd: [útválasztási szabályok](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).
* IoT Hub lehetővé teszi több millió egyszerre kapcsolódó eszközről, (tekintse meg [kvóták és szabályozás][lnk-quotas]).
* Az IoT Hub nem teszi lehetővé tetszőleges particionálás. Eszköz – felhő üzenetek vannak particionálva, azok származó alapján **deviceId**.

Az IoT Hub és az Event Hubs közti különbségekkel kapcsolatos további információkért lásd: [összehasonlítása az Azure IoT Hub és az Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Nem telemetriai forgalom küldése

Gyakran mellett telemetriát, eszközöket üzeneteket küldhet, és külön végrehajtását és a megoldás háttérrendszere a kezelési igénylő kérelmek. Például kritikus riasztás, amely egy bizonyos művelet a háttérben lévő kell elindítani. Írhat egy [útválasztási szabály] [ lnk-devguide-custom] az ilyen típusú üzeneteket küldhet a feldolgozás, vagy egy fejléc, az üzenet vagy az üzenettörzsben szereplő érték alapján dedikált végpont.

A legjobb módszer az ilyen típusú üzenetet feldolgozni kapcsolatos további információkért lásd: a [oktatóanyag: az IoT Hub eszköz a felhőbe irányuló üzenetek feldolgozása] [ lnk-d2c-tutorial] oktatóanyag.

## <a name="route-device-to-cloud-messages"></a>Eszköz – felhő üzenetek továbbítását.

A háttér-alkalmazások a eszközt a felhőbe irányuló üzenetek két lehetősége van:

* A beépített [Event Hub-kompatibilis végpont] [ lnk-compatible-endpoint] háttér-alkalmazások a hub által fogadott eszköz a felhőbe irányuló üzenetek olvasásához. A beépített Event Hub-kompatibilis végpont kapcsolatos további információkért lásd: [eszköz – felhő üzenetek olvasásához a beépített végpontról][lnk-devguide-builtin].
* Útválasztási szabályok használatával üzeneteket küldenek az IoT hub az egyedi végpontok. Egyéni végpontok engedélyezése a háttér-alkalmazások, az Event Hubs, Service Bus-üzenetsorok és Service Bus-üzenettémakörök használata eszköz a felhőbe irányuló üzenetek olvasásához. Útválasztási és egyéni végpontok kapcsolatos további információkért lásd: [egyéni végpontok és útválasztási szabályok használata az eszköz a felhőbe irányuló üzenetek][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Hamisításszűrés tulajdonságai

Címek hamisítását az eszköz – felhő üzeneteket, az IoT Hub eszköz elkerülése érdekében minden stampek üzenetekre, az alábbi tulajdonságokkal:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Az első két tartalmazzák a **deviceId** és **generationId** az eredeti eszköz megfelelően [identitás eszköztulajdonságok][lnk-device-properties].

A **ConnectionAuthMethod** tulajdonságot tartalmaz egy JSON-szerializált objektumot, a következő tulajdonságokkal:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>További lépések

Eszköz a felhőbe irányuló üzenetek küldéséhez használhatja az SDK-kkal kapcsolatos információk: [Azure IoT SDK-k][lnk-sdks].

A [útmutatóink] [ lnk-get-started] bemutatják, hogyan küldhet eszközről a felhőbe üzeneteket szimulált eszközökről. További részletekért tekintse meg a [folyamat az IoT Hub eszköz – felhő üzenetek útvonalak] [ lnk-d2c-tutorial] oktatóanyag.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: quickstart-send-telemetry-node.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: tutorial-routing.md
