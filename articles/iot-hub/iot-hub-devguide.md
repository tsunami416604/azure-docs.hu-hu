---
title: Fejlesztői útmutató az Azure IoT Hubhoz | Microsoft dokumentumok
description: Az Azure IoT Hub fejlesztői útmutató jaain végpontok, biztonság, az identitás-beállításjegyzék, eszközkezelés, közvetlen módszerek, eszköz twins, fájlfeltöltések, feladatok, az IoT Hub lekérdezési nyelv és az üzenetküldés.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 1ff7d430edd3f638ad5efcc5a89604e4ed732211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60400150"
---
# <a name="azure-iot-hub-developer-guide"></a>Az Azure IoT Hub fejlesztői útmutatója

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás háttérkiszolgáló között.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az Azure IoT Hub a következőket biztosítja:

* Biztonságos kommunikáció az eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés használatával.

* Több eszközről felhőre és felhőből az eszközre irányuló nagy léptékű kommunikációs lehetőségek.

* Az eszközönkénti állapotadatok és metaadatok lekérdezhető tárolása.

* Egyszerű eszközkapcsolat a legnépszerűbb nyelvekhez és platformokhoz készült eszközkönyvtárakkal.

Ez az IoT Hub fejlesztői útmutatója a következő cikkeket tartalmazza:

* [Az eszközről a felhőbe](iot-hub-devguide-d2c-guidance.md) irányuló kommunikációról szóló útmutatás segítségével választhat az eszközről a felhőbe irányuló üzenetek, az ikereszköz jelentett tulajdonságai és a fájlfeltöltés között.

* [A felhőből az eszközre irányuló kommunikációútmutatás segítségével](iot-hub-devguide-c2d-guidance.md) választhat a közvetlen módszerek, az ikereszköz kívánt tulajdonságai és a felhőből az eszközre irányuló üzenetek között.

* [Az IoT Hubbal az eszközök ről a felhőbe és a felhőbe irányuló üzenetküldés](iot-hub-devguide-messaging.md) az IoT Hub bal üzenetkezelési funkcióit (eszközről felhőbe és felhőből az eszközre) ismerteti.

  * [Eszközről felhőbe irányuló üzenetekküldése az IoT Hubba.](iot-hub-devguide-messages-d2c.md)

  * [Az eszközről a felhőbe irányuló üzeneteket a beépített végpontról olvashatja.](iot-hub-devguide-messages-read-builtin.md)

  * [Egyéni végpontok és útválasztási szabályok használata az eszközről a felhőbe irányuló üzenetekhez.](iot-hub-devguide-messages-read-custom.md)

  * [Felhőből az eszközre irányuló üzenetek küldése az IoT Hubról.](iot-hub-devguide-messages-c2d.md)

  * [IoT Hub-üzenetek létrehozása és olvasása.](iot-hub-devguide-messages-construct.md)

* [Fájlok feltöltése egy eszközről](iot-hub-devguide-file-upload.md) leírja, hogyan tölthet fel fájlokat egy eszközről. A cikk olyan témakörökkel kapcsolatos információkat is tartalmaz, mint például a feltöltési folyamat által küldhető értesítések.

* [Az IOt Hub ban az eszközök identitásainak kezelése](iot-hub-devguide-identity-registry.md)leírja, hogy az egyes IoT-központ identitás-beállításjegyzéke milyen adatokat tárol. A cikk azt is ismerteti, hogyan érheti el és módosíthatja azt.

* [Az IoT Hubhoz való hozzáférés szabályozása](iot-hub-devguide-security.md) az IoT Hub funkcióihoz való hozzáférés engedélyezéséhez használt biztonsági modellt írja le mind az eszközök, mind a felhőbeli összetevők számára. A cikk a jogkivonatok és az X.509-es tanúsítványok használatával kapcsolatos információkat, valamint a megadhat engedélyek részleteit tartalmazza.

* [Az eszköz twins állapot ának szinkronizálásához és konfigurációk](iot-hub-devguide-device-twins.md) ismerteti az *eszköz iker* koncepciója. A cikk azt is ismerteti, hogy az ikereszközök milyen funkciókat biztosítanak, például szinkronizálja az eszközt az ikereszközével. A cikk az ikereszközben tárolt adatokkal kapcsolatos információkat tartalmazza.

* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md) egy közvetlen metódus életciklusát írja le. A cikk ismerteti, hogyan lehet metódusokat hívni egy eszközön a háttéralkalmazásból, és kezelni a közvetlen módszert az eszközön.

* [A feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md) azt ismerteti, hogyan ütemezheti a feladatokat több eszközön. A cikk ismerteti, hogyan küldheti el a feladatokat végrehajtó feladatokat, mint egy közvetlen metódus végrehajtása, frissítése egy eszköz egy eszköz iker. Azt is leírja, hogyan lehet lekérdezni egy feladat állapotát.

* [Hivatkozás – válasszon egy kommunikációs protokollt,](iot-hub-devguide-protocols.md) amely az IoT Hub által az eszközkommunikációhoz támogatott kommunikációs protokollokat írja le, és felsorolja azokat a portokat, amelyeknek nyitva kell lenniük.

* [Referencia – Az IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különböző végpontok, amelyek az egyes IoT-központok elérhetővé teszi a futásidejű és felügyeleti műveletek. A cikk azt is ismerteti, hogyan hozhat létre további végpontokat az IoT hubban, és hogyan használhat egy helyszíni átjárót az IoT Hub végpontjaihoz való kapcsolódás engedélyezéséhez nem szabványos forgatókönyvekben.

* [Referencia – Az IoT Hub lekérdezési nyelve az eszközök twins, feladatok és üzenet-útválasztás](iot-hub-devguide-query-language.md) leírja, hogy az IoT Hub lekérdezési nyelv, amely lehetővé teszi, hogy az eszköz twins és feladatok adatokat a hubról.

* [Referencia – kvóták és sávszélesség-szabályozás](iot-hub-devguide-quotas-throttling.md) összegzi az IoT Hub szolgáltatásban beállított kvótákat és a sávszélesség-szabályozást, amely akkor következik be, ha túllépi a kvótát.

* [Referencia – az árképzés](iot-hub-devguide-pricing.md) általános információkat nyújt a különböző SK-k és az IoT Hub díjszabása, valamint a rról, hogy a különböző IoT Hub-funkciók at az IoT Hub által imputálásként mérik.

* [Referencia – Az eszköz- és szolgáltatásSDK-k](iot-hub-devguide-sdks.md) az Azure IoT SDK-kat sorolja fel az IoT-központtal interakcióba lépő eszköz- és szolgáltatásalkalmazások fejlesztéséhez. A cikk az online API-dokumentációra mutató hivatkozásokat tartalmaz.

* [Referencia – Az IoT Hub MQTT támogatása](iot-hub-mqtt-support.md) részletes információkat nyújt arról, hogy az IoT Hub hogyan támogatja az MQTT protokollt. A cikk ismerteti az Azure IoT SDK-kba beépített MQTT protokoll támogatását, és az MQTT protokoll közvetlen használatával kapcsolatos információkat ismerteti.

* [Szószedet](iot-hub-devguide-glossary.md) a közös IoT Hub-vonatkozású kifejezések listája.