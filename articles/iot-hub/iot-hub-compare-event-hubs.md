---
title: "Az Azure Event hubs az Azure IoT Hub összehasonlítása |} Microsoft Docs"
description: "A használati esetek és működési különbségeket kiemelés az IoT-központ és az Event Hubs Azure szolgáltatások összehasonlítása. Az összehasonlítás tartalmazza a támogatott protokollok, kezelés, figyelés, és fájlfeltöltések."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b515e05d16dda83c7d865113d5d3578c44be084f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Az Azure IoT-központ és az Azure Event Hubs összehasonlítása
Az IoT-központ fő alkalmazási helyzetei egyik telemetria összeállítani eszközökről. Emiatt az IoT-központ gyakran képest [Azure Event Hubs][Azure Event Hubs]. Az IoT-központ, mint az Event Hubs egy Eseményfeldolgozási szolgáltatás, amely lehetővé teszi a felhőbe, nagy léptékű esemény- és telemetriabevitelt érkező, alacsony késéssel és nagy megbízhatósággal.

A szolgáltatások azonban sok különbségek, amely az alábbi táblázat részletezi rendelkezik:

| Terület | IoT Hub | Event Hubs |
| --- | --- | --- |
| Kommunikációs minták | Lehetővé teszi, hogy [eszközről a felhőbe kommunikációs] [ lnk-d2c-guidance] (üzenettovábbítás, fájl, feltöltések és jelentett Tulajdonságok) és [felhő eszközre kommunikációs] [ lnk-c2d-guidance] (közvetlen módszerek, üzenetküldési kívánt tulajdonságai). |Csak lehetővé teszi, hogy esemény érkező (eszköz-felhő forgatókönyvek általában számít). |
| Eszköz állapota adatai | [Eszköz twins] [ lnk-twins] tárolására és a lekérdezési eszköz állapotadatait. | Eszköz állapota adatokat tárolhatja. |
| Eszköz protokoll támogatása |MQTT, MQTT keresztül websocket elemeket, AMQP, AMQP websocket elemeket, és HTTPS protokollt támogatja. Továbbá az IoT-központ működik együtt a [Azure IoT protokoll-átjáró][lnk-azure-protocol-gateway], egy testre szabható átjáró protokollmegvalósítás egyéni protokollok támogatásához. |AMQP, AMQP websocket elemeket, és HTTPS protokollt támogatja. |
| Biztonság |Biztosít az eszközönkénti identitás- és visszavonható hozzáférés-vezérlés. Tekintse meg a [biztonsági szakasz az IoT Hub fejlesztői útmutató]. |Event Hubs kiterjedő biztosít [megosztott hozzáférési házirendek][Event Hubs - security], és a korlátozott visszahívás támogatja segítségével [publisher házirendek][Event Hubs publisher policies]. Az IoT-megoldások gyakran eszközönkénti hitelesítő adatok és mérőszámok hamisításszűrés támogatásához egyéni megoldás megvalósításához szükséges. |
| Műveletek figyelése |Lehetővé teszi, hogy az IoT-megoldások eszköz identity management és a csatlakozási esemény, például egyes eszköz hitelesítési hibák, a sávszélesség-szabályozás és a hibás formátumú kivételek széles skáláját előfizetni. Ezek az események lehetővé teszik a gyorsan azonosíthatja a kapcsolódási problémái vannak az egyes eszközök szintjén. |Csak összesített metrikák mutatja. |
| Méretezés |Arra optimalizálták, hogy egyidejűleg csatlakoztatott eszközök millióira támogatása. |A kapcsolatok megfelelően meters [Azure Event Hubs kvóták][Azure Event Hubs quotas]. Másrészt az Event Hubs lehetővé teszi az egyes üzenetsorokra küldött üzeneteket a partíció megadását. |
| Eszközoldali SDK-k |Itt [eszköz SDK-k] [ Azure IoT SDKs] számos platformon és programnyelven mellett közvetlen MQTT AMQP vagy HTTPS API-k esetében. |Támogatott .NET, Java és a C, továbbá amqp-t és a HTTPS küldési felületek elembe. |
| Fájl feltöltése |Lehetővé teszi, hogy az IoT-megoldások fájlfeltöltés eszközről a felhőbe. A munkafolyamat-integráció és egy támogatási hibakereséshez kategória figyelési műveletek egy fájl értesítési tartozó végpontot tartalmaz. | Nem támogatott. |
| Több végpont üzeneteknek | Legfeljebb 10 egyéni végpontokat támogatottak. A szabályok határozzák meg, hogyan legyenek átirányítva üzenetek egyéni végpontokat. További információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal][lnk-devguide-messaging]. | Kód írása és az üzenetek terjesztéséhez üzemeltetett igényel. |

Összefoglalva akkor is, ha csak a használati eset telemetriai eszközről a felhőbe érkező, az IoT-központ biztosít egy szolgáltatás, amely úgy van kialakítva, IoT-eszköz kapcsolatot. Bontsa ki az IoT-specifikus funkcióival forgatókönyvek esetén a értéknövelő továbbra is. Az Event Hubs egy nagy méretű, mindkettő többek datacenter és intra-datacenter forgatókönyvek környezetében esemény érkező tervezték.

A rendszer nem ritka, hogy az IoT-központ és az Event Hubs egyaránt megoldáskezelőben. Az IoT-központ kezeli az eszközről a felhőbe kommunikációt, és az Event Hubs későbbi szakaszban esemény érkező kezeli a valós idejű feldolgozórendszerekkel való.

### <a name="next-steps"></a>Következő lépések
Az IoT-központ telepítésének tervezése kapcsolatos további információkért lásd: [méretezés, a magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási][lnk-scaling].

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Az Azure IoT peremhálózati peremeszközök AI központi telepítése][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[biztonsági szakasz az IoT Hub fejlesztői útmutató]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
