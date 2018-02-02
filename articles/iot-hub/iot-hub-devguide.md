---
title: "Azure IoT hub fejlesztői útmutató |} Microsoft Docs"
description: "Az Azure IoT Hub fejlesztői útmutató tartalmaz beszélgetéseket végpontok, biztonsági, az identitásjegyzékhez, kezelés, közvetlen módszerek, eszköz twins, fájlfeltöltéseket, feladatok, az IoT-központ lekérdező nyelv, és üzenetkezelés."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 37f9da7dcc8dd527fe0bfbf2fbcc40a3ba0e8a1c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-developer-guide"></a>Az Azure IoT Hub fejlesztői útmutató

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel eszközök millióira közötti megbízható és biztonságos kétirányú kommunikáció engedélyezése és a megoldás háttérrendszere.

Az Azure IoT-központ biztosítja:

* A biztonságos kommunikáció eszközönkénti biztonsági hitelesítő adatok használatával, és hozzáférés-vezérlést.
* Több eszköz-felhő és a felhő eszközre kapacitású kommunikációs lehetőségekről.
* Eszközönkénti állapotadatokat és a metaadatok tárolása lekérdezhető.
* Egyszerű eszközkapcsolatok eszköz könyvtárakkal a legnépszerűbb nyelvekhez és platformokhoz.

Az IoT Hub fejlesztői útmutató tartalmazza a következő cikkeket:

* [Eszköz-felhő kommunikációs útmutatást] [ lnk-d2c-guidance] segítségével választhat eszköz a felhőbe küldött üzeneteket, az eszköz iker jelentett tulajdonságok és a fájl feltöltése között.
* [Útmutatás felhő-és eszköz közötti kommunikációt] [ lnk-c2d-guidance] segítségével választhat a közvetlen módszer eszköz iker kívánt tulajdonságokkal és felhő-eszközre küldött üzenetek között.
* [Eszköz-felhő és a felhő-eszközt az IoT-központ az üzenetküldési] [ devguide-messaging] üzenetkezelési szolgáltatások (eszközről a felhőbe és felhő eszközre), amely az IoT-központ elérhetővé teszi.
  * [Eszköz-felhő üzenetek küldése az IoT-központ][devguide-messages-d2c].
  * [Eszköz a felhőbe küldött üzeneteket beolvasni a beépített végpont][devguide-builtin].
  * [Egyéni végpontokat és útválasztási szabályokat használja az eszköz a felhőbe küldött üzeneteket][devguide-custom].
  * [Felhő-eszközre küldött üzenetek küldése az IoT-központ][devguide-messages-c2d].
  * [Hozzon létre, és az IoT-központ olvashatja][devguide-format].
* [Az eszközről tölt fel] [ devguide-upload] ismerteti, hogyan tölthet fájlok az eszközről. A cikk is magában foglalja a tudnivalókat az értesítéseket küldhet a feltöltési folyamat.
* [Az IoT Hub eszköz identitáskezelést] [ devguide-identities] milyen információkat ismerteti, minden IoT-központ beállításjegyzék identitástárolók. A cikk azt is ismerteti, hogyan elérheti és módosíthatja azt.
* [Az IoT-központ való hozzáférés szabályozása] [ devguide-security] a biztonsági modell segítségével hozzáférést biztosíthat az IoT-központ funkciót mindkét eszközökhöz és összetevők a felhőalapú ismerteti. A cikk tokeneket és X.509-tanúsítványokat, és az engedélyek megadásához részleteit használatával kapcsolatos információt tartalmaz.
* [Eszköz twins segítségével szinkronizálja az állapotot és konfigurációk] [ devguide-device-twins] ismerteti a *eszköz iker* fogalom. A cikk is descibes a funkciók eszköz twins teszi közzé, például az eszköz a két eszköz szinkronizálása. A cikk egy eszköz iker tárolt adatokra vonatkozó információt tartalmaz.
* [Az eszközön közvetlen metódus] [ devguide-directmethods] közvetlen módszer az életciklus ismerteti. A cikk ismerteti a háttér-alkalmazás egy eszközön metódusok és kezelni a közvetlen módszer az eszközön.
* [Több eszközön feladatok ütemezése] [ devguide-jobs] ismerteti, hogyan több eszközön futó feladatot is ütemezheti. A cikk ismerteti, hogyan, feladatokat végrehajtó egy közvetlen módszer, egy eszközt egy eszközt a két frissítési feladatok küldéséhez. Emellett bemutatja, hogyan lehet a feladat állapotának lekérdezéséhez.
* [Olyan kommunikációs protokollt hivatkozhat - a] [ devguide-protocol] írja le a kommunikációs protokollt, hogy az IoT-Központ támogatja az eszközök közötti kommunikáció, és nyitva kell lennie portokat sorolja fel.
* [Referencia - IoT-központok végpontjai] [ devguide-endpoints] ismerteti a különböző végpontok, amelyek minden egyes IoT-központ elérhetővé teszi a futásidejű és felügyeleti műveletek. A cikk is ismerteti, hogyan hozhat létre további végpontokat az IoT hub, és hogyan mező átjáró segítségével engedélyezze a kapcsolatot a nem szabványos helyzetekben az IoT-központok végpontjai.
* [Referencia - IoT-központ lekérdezési nyelv eszköz twins, feladatok és üzenet útválasztási] [ devguide-query] , hogy az IoT-központ lekérdező nyelv, amely lehetővé teszi az adatok lekérését a hub eszköz twins és feladatokat ismerteti.
* [Referencia - kvóták és sávszélesség-szabályozási] [ devguide-quotas] foglalja össze a kvótákat, az IoT-központ szolgáltatás és a sávszélesség-szabályozás beállítása, amely akkor fordul elő, ha az túllépi a kvótát.
* [Hivatkozás - díjszabás] [ devguide-pricing] különböző Termékváltozatai és az IoT Hub és részletek hogyan a különböző IoT-központ funkciók mérése üzenetekként IoT-központ által díjszabása kapcsolatos általános tudnivalókat tartalmazza.
* [Referencia - eszköz és az SDK szolgáltatás] [ devguide-sdks] Azure IoT készült SDK-k alkalmazásfejlesztés eszköz és a szolgáltatás az IoT hub együttműködő sorolja fel. A cikk online API dokumentációjára mutató hivatkozásokat tartalmaz.
* [Referencia - IoT Hub MQTT támogatási] [ devguide-mqtt] részletes információt nyújt arról, hogyan IoT-központ a MQTT protokoll használatát támogatja. A cikk ismerteti a beépített az Azure IoT SDK-k MQTT protokoll támogatását, és közvetlenül a MQTT protokollal kapcsolatos információkat.
* [Szószedet] [ devguide-glossary] általános IoT Hub-mal kapcsolatos kifejezések listája.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
