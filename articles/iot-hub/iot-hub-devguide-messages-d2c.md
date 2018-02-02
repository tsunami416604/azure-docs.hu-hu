---
title: "Azure IoT Hub eszközről a felhőbe üzenetküldési megértése |} Microsoft Docs"
description: "Fejlesztői útmutató - eszközről a felhőbe az IoT-központ az üzenetküldési használata. A telemetriai adatokat, és nem telemtry adatok küldését, és üzeneteket a útválasztás használatával kapcsolatos adatokat tartalmaz."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 48b904818c80b9175d45b88345634f11cf4a4812
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Eszköz-felhő üzenetek küldése az IoT-központ

Küldendő idősorozat telemetriai adatok és riasztások az eszközökről a megoldás háttérrendszeréhez, eszköz-felhő üzenetek küldése az eszközről az IoT hub. Az IoT-központ által támogatott eszközről a felhőbe lehetőségeket tárgyalását lásd: [eszközről a felhőbe kommunikációs útmutatást][lnk-d2c-guidance].

Egy eszköz felé néző végpont keresztül eszközről a felhőbe üzeneteket küld (**/devices/ {deviceId} / üzenetek/események**). Útválasztási szabályokat, majd az üzenetek továbbításához a szolgáltatás felé néző végpontok az IoT hub egyikére. Útválasztási szabályokat a fejlécek és az eszköz-felhő üzenetek törzsének használatával ezek helyének meghatározása. Alapértelmezés szerint üzenetek átirányítja a beépített service felé néző végpont (**üzenetek/események**), amely pedig kompatibilis [Event Hubs][lnk-event-hubs]. Ezért, használhatja a szabványos [Event Hubs-integrációval és SDK-k] [ lnk-compatible-endpoint] a megoldás háttérrendszeréhez az eszközről a felhőbe üzeneteket fogadni.

Az IoT-központ megvalósítja az eszközről a felhőbe üzenetküldési adatfolyam üzenetkezelési minta használatával. Az IoT-központ eszközről a felhőbe üzenetek mint [Event Hubs] [ lnk-event-hubs] *események* mint [Service Bus] [ lnk-servicebus] *üzenetek* abban, hogy van egy nagy mennyiségű esemény haladnak át a szolgáltatást, amely több olvasók által is olvasható.

Eszköz-felhő IoT-központ az üzenetküldési jellemzői a következők:

* Eszköz a felhőbe küldött üzeneteket a következők tartós és az IoT-központ alapértelmezett megtartott **üzenetek/események** végpont hét napig.
* Eszköz a felhőbe küldött üzeneteket legfeljebb 256 KB, és küld optimalizálása kötegekben csoportosíthatók. Kötegek legfeljebb 256 KB lehet.
* A a [IoT-központ való hozzáférés szabályozása] [ lnk-devguide-security] szakaszban, IoT-központ lehetővé teszi, hogy a eszközönkénti hitelesítési és hozzáférés-vezérlés.
* Az IoT-központ lehetővé teszi, hogy legfeljebb 10 egyéni végpontokat hoz létre. Az IoT hub konfigurált útvonalak alapján végpontok kézbesíti az üzeneteket. További információkért lásd: [útválasztási szabályok](#routing-rules).
* Az IoT-központ lehetővé teszi, hogy egyidejűleg csatlakoztatott eszközök millióira (lásd: [kvóták és sávszélesség-szabályozási][lnk-quotas]).
* Az IoT-központ nem engedélyezi a tetszőleges particionálást. Eszköz a felhőbe küldött üzeneteket particionáltak, azok származó alapján **deviceId**.

Az IoT-központ és az Event Hubs közötti különbségekről további információkért lásd: [összehasonlítása az Azure IoT-központ és az Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Nem telemetriai forgalom küldése

Gyakran telemetriai adatokat mellett eszközök küldött üzenetek és külön végrehajtásához, és a megoldás háttérrendszeréhez a kezelést igénylő kérelmek. Például kritikus riasztások háttérben lévő olyan adott műveletet kell kiváltani. Írhat egy [útválasztási szabály] [ lnk-devguide-custom] ilyen típusú üzenetek küldésére a feldolgozás, vagy a fejléc az üzenetben vagy az üzenet törzsében érték alapján dedikált végpont.

A legjobb módszer az ilyen üzenet feldolgozása kapcsolatos további információkért tekintse meg a [oktatóanyag: az IoT-központ eszközről a felhőbe üzenetek feldolgozása] [ lnk-d2c-tutorial] oktatóanyag.

## <a name="route-device-to-cloud-messages"></a>Eszköz-felhő üzenetek

Eszköz-felhő üzenetek két lehetőség közül választhat a háttér-alkalmazásokhoz való:

* Használja a beépített [Event Hub-kompatibilis végpont] [ lnk-compatible-endpoint] olvashatja az eszközről a felhőbe a központ által fogadott háttér-alkalmazások engedélyezéséhez. A beépített Event Hub-kompatibilis végpont kapcsolatos további tudnivalókért lásd: [eszköz a felhőbe küldött üzeneteket beolvasni a beépített végpont][lnk-devguide-builtin].
* Útválasztási szabályok használatával üzenetek küldése az IoT hub egyéni végpontok. Egyéni végpontokat engedélyezése a háttér-alkalmazások, az Event Hubs, a Service Bus-üzenetsorok vagy a Service Bus-üzenettémakörök eszközről a felhőbe üzenetek olvasásakor. Útválasztási és egyéni végpontok kapcsolatos további tudnivalókért lásd: [egyéni végpontokat és útválasztási szabályokat használja az eszköz a felhőbe küldött üzeneteket][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Hamisításszűrés tulajdonságai

Az eszközről a felhőbe üzenetekben, IoT Hub-címek hamisítását eszköz elkerülése érdekében minden bélyegzők üzenetek, a következő tulajdonságokkal:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Az első két tartalmaz a **deviceId** és **generationId** az eredeti eszköz megfelelően [identitás eszköztulajdonságok][lnk-device-properties].

A **ConnectionAuthMethod** tulajdonsága tartalmazza egy szerializált JSON-objektum, a következő tulajdonságokkal:

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>További lépések

További információ az SDK-k segítségével eszközről a felhőbe üzenetek küldéséhez,: [Azure IoT SDK-k][lnk-sdks].

A [Ismerkedés] [ lnk-get-started] oktatóprogramok bemutatják a eszközről a felhőbe üzeneteket küldhet a szimulált és a fizikai eszközök. További részletekért tekintse meg a [folyamat IoT Hub eszközről a felhőbe üzenetek útvonalak] [ lnk-d2c-tutorial] oktatóanyag.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
