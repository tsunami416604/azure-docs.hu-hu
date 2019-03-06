---
title: Az Azure IoT Hub fejlesztői útmutató |} A Microsoft Docs
description: Az Azure IoT Hub fejlesztői útmutató tartalmaz végpontokat, biztonsági, az eszközidentitás-jegyzék, eszközkezelés, közvetlen metódusok, ikereszközök, fájlfeltöltéseket, feladatok, az IoT Hub lekérdezési nyelv, hozzászólások és üzenetkezelés.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 1ff7d430edd3f638ad5efcc5a89604e4ed732211
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450991"
---
# <a name="azure-iot-hub-developer-guide"></a>Az Azure IoT Hub fejlesztői útmutató

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel engedélyezheti a megbízható és biztonságos kétirányú kommunikációt több millió eszköz között, és megoldást biztosít a háttérrendszer.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az Azure IoT Hub a következőket tartalmazza:

* Biztonságos kommunikáció az eszközönkénti biztonsági hitelesítő adatok használatával, és a hozzáférés-vezérlés.

* Több eszköz-felhő és a felhőből az eszközre kapacitású kommunikációs lehetőségeit.

* Az eszközönkénti állapotinformációkat és metaadatok lekérdezhető tárolására.

* A legnépszerűbb nyelvek és platformok eszközkönyvtárait egyszerű eszköz kapcsolat.

Ez az IoT Hub fejlesztői útmutató a következő cikkeket tartalmazza:

* [Eszközről a felhőbe való kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) segít az eszköz – felhő üzeneteket, az eszköz ikereszköz jelentett tulajdonságait és a fájl feltöltése között választja.

* [Útmutató a felhőből az eszközre irányuló kommunikáció](iot-hub-devguide-c2d-guidance.md) segít választhat a közvetlen metódusok, eszköz ikereszköz kívánt tulajdonságait és felhőből az eszközre irányuló üzenetek között.

* [Eszközről a felhőbe és a felhőből az eszközre irányuló, az IoT Hub üzenetküldési](iot-hub-devguide-messaging.md) a üzenettovábbítási szolgáltatások (eszköz-felhő és a felhőből az eszközre), amely az IoT Hub tesz ismerteti.

  * [Eszköz – felhő üzeneteket küldenek az IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Olvassa el az eszközt a felhőbe irányuló üzenetek a beépített végpontról](iot-hub-devguide-messages-read-builtin.md).

  * [Egyéni végpontok és útválasztási szabályok használata az eszköz a felhőbe irányuló üzenetek](iot-hub-devguide-messages-read-custom.md).

  * [Felhőből az eszközre irányuló üzenetek küldése az IoT hubról](iot-hub-devguide-messages-c2d.md).

  * [Hozzon létre és üzenetek olvasásához az IoT Hub](iot-hub-devguide-messages-construct.md).

* [Fájlfeltöltés az eszközökről](iot-hub-devguide-file-upload.md) ismerteti, hogyan tölthet fel fájlokat az eszközről. A cikk többek között az értesítéseket küldhet a feltöltési folyamat információt is tartalmaz.

* [Az IoT Hub eszközidentitások kezelése](iot-hub-devguide-identity-registry.md)azt ismerteti, milyen információkat minden IoT-központ identitás beállításjegyzék tárolja. A cikk azt is leírja, hogyan elérheti és módosíthatja azt.

* [Ki férhet hozzá az IoT Hub](iot-hub-devguide-security.md) a biztonsági modell hozzáférést biztosít az IoT Hub funkció mindkét eszközökhöz és felhőalapú összetevőit ismerteti. A cikk tokeneket és X.509-tanúsítványokat, és részleteket adhat az engedélyek használatával kapcsolatos információt tartalmaz.

* [Ikereszközök használata az állapot és a konfiguráció szinkronizálása](iot-hub-devguide-device-twins.md) ismerteti a *ikereszköz* fogalom. A cikk is ismerteti a funkció eszköz twins teszi közzé, például az eszköz az ikereszköz való szinkronizálása. A cikk az ikereszközök tárolt adatokra vonatkozó információkat tartalmaz.

* [Az eszközön közvetlen metódus meghívása](iot-hub-devguide-direct-methods.md) közvetlen metódus életciklusának ismerteti. A cikk ismerteti az eszköznek a háttéralkalmazás metódusokat hívhat meg, és kezelni a közvetlen metódus az eszközön.

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md) ismerteti, hogyan ütemezhet feladatokat több eszközön. A cikk azt ismerteti, hogyan küldhetők be feladatok, mint az ikereszközök használatával egy eszköz frissítése közvetlen metódus végrehajtása a feladatokat elvégző. Emellett bemutatja, hogyan lehet egy feladat állapotának lekérdezése.

* [Hivatkozás - kommunikációs protokoll kiválasztása](iot-hub-devguide-protocols.md) ismerteti a kommunikációs protokollokat, hogy az IoT Hub eszköz kommunikációt támogatja, és nyitva kell lennie portokat sorolja fel.

* [Referencia – IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különféle végpontok, amely minden egyes IoT hub-futtatókörnyezet és a felügyeleti műveletekhez. A cikk azt is leírja, hogyan hozhat létre további végpontokat az IoT hub és a helyszíni átjáró használata az IoT Hub-végpontok nem szabványos forgatókönyvekben csatlakozásának engedélyezéséhez.

* [Referencia - az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md) ismerteti, hogy az IoT Hub lekérdezési nyelv, amely lehetővé teszi, hogy a hub az ikereszközökhöz és feladatokhoz kapcsolatban lévő információk lekéréséhez.

* [Referencia - kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md) foglalja össze a kvóták, állítsa be az IoT Hub szolgáltatás és a szabályozás, amely akkor fordul elő, ha túllépi a kvótát.

* [Hivatkozás - díjszabás](iot-hub-devguide-pricing.md) általános információkat tartalmaz a különböző SKU-k és az IoT Hub és az információt hogyan az IoT Hub különféle funkciók biztosításához mérjük üzenetekként által az IoT Hub díjszabása.

* [Referencia - eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md) sorolja fel az Azure IoT SDK-k, hogy az IoT hubbal együttműködő eszköz és szolgáltatás alkalmazások fejlesztéséhez. A cikk online API-dokumentáció mutató hivatkozásokat tartalmaz.

* [Referencia – IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md) arról, hogy az IoT Hub hogyan támogatja az MQTT protokoll részletes információkat tartalmaz. A cikk ismerteti az MQTT protokoll beépített, az Azure IoT SDK-k támogatását, és közvetlenül az MQTT protokoll használatával kapcsolatos információkat.

* [Szószedet](iot-hub-devguide-glossary.md) gyakori IoT Hub-mal kapcsolatos kifejezések listája.