---
title: Azure IoT Hub méretezés | Microsoft dokumentumok
description: Az IoT-központ méretezése a várható üzenetátviteli és a kívánt funkciók támogatásához. Az egyes rétegek támogatott átviteli és a szilánkokra vonatkozó beállítások összegzését tartalmazza.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: ecc1ae5138fe5a1e42fed9be2e31b5afa8b6d5b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497507"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Egy adott megoldáshoz leginkább megfelelő IoT Hub-szint kiválasztása

Minden IoT-megoldás más, így az Azure IoT Hub számos lehetőséget kínál a díjszabás és a méretezés alapján. Ez a cikk célja, hogy segítsen kiértékelni az IoT Hub igényeinek. Az IoT Hub-szintek díjszabási információkért tekintse meg az [IoT Hub díjszabása.](https://azure.microsoft.com/pricing/details/iot-hub)

Annak eldöntéséhez, hogy melyik IoT Hub-szint megfelelő a megoldáshoz, tegyen fel magának két kérdést:

**Milyen funkciókat szeretnék használni?**

Az Azure IoT Hub két alapszintű és standard szintű, amelyek eltérnek a támogatott funkciók száma. Ha az IoT-megoldás az eszközökről történő adatgyűjtésen és azok központi elemzésén alapul, akkor az alapszintű szint valószínűleg megfelelő az Ön számára. Ha azt szeretné, hogy fejlettebb konfigurációk at ioT-eszközök távoli vezérlésére, vagy a számítási feladatok egy részét az eszközökön magukat, majd érdemes megfontolni a standard szint. Az egyes szintekben szereplő funkciók részletes bontásához továbbra is [az alapszintű és a standard szintű szintek rekednek](#basic-and-standard-tiers).

**Mennyi adatot tervezek naponta áthelyezni?**

Minden Egyes IoT Hub-szint három méretben érhető el, az adott napon kezelhető adatátviteli adatok alapján. Ezeket a méreteket számszerűen 1, 2 és 3 értékként azonosítják. Például egy 1 szintű IoT hub minden egysége napi 400 ezer üzenetet képes kezelni, míg egy 3. Az adatirányelvekről további információt az [Üzenet átviteli -ról.](#message-throughput)

## <a name="basic-and-standard-tiers"></a>Alap- és alapszintű szintek

Az IoT Hub szabványos szintje lehetővé teszi az összes funkciót, és minden olyan IoT-megoldáshoz szükséges, amely a kétirányú kommunikációs képességeket szeretné használni. Az alapszint a funkciók egy részhalmazát engedélyezi, és olyan IoT-megoldásokhoz való, amelyek csak egyirányú kommunikációt igényelnek az eszközöktől a felhő felé. Mindkét szint ugyanazokat a biztonsági és hitelesítési funkciókat kínálja.

IoT Hubonként csak egy [típusú kiadás](https://azure.microsoft.com/pricing/details/iot-hub/) választható ki egy rétegen belül. Létrehozhat például egy IoT Hubot több S1 egységgel, de nem különböző kiadásokból származó egységek, például s1 és S2 egységek keverékével.

| Képesség | Alapszintű csomag | Ingyenes/standard szint |
| ---------- | ---------- | ------------- |
| [Eszközről a felhőbe irányuló telemetria](iot-hub-devguide-messaging.md) | Igen | Igen |
| [Eszközönkénti identitás](iot-hub-devguide-identity-registry.md) | Igen | Igen |
| [Üzenettovábbítás](iot-hub-devguide-messages-read-custom.md), [üzenetbővítés](iot-hub-message-enrichments-overview.md)és [eseményrács-integráció](iot-hub-event-grid.md) | Igen | Igen |
| [HTTP, AMQP és MQTT protokollok](iot-hub-devguide-protocols.md) | Igen | Igen |
| [Eszközkiépítési szolgáltatás](../iot-dps/about-iot-dps.md) | Igen | Igen |
| [Ellenőrzés és diagnosztika](iot-hub-monitor-resource-health.md) | Igen | Igen |
| [Felhőből az eszközre irányuló üzenetküldés](iot-hub-devguide-c2d-guidance.md) |   | Igen |
| [Eszköz ikrek](iot-hub-devguide-device-twins.md), [Modul ikrek](iot-hub-devguide-module-twins.md), és az [eszköz kezelése](iot-hub-device-management-overview.md) |   | Igen |
| [Eszközadatfolyamok (előzetes verzió)](iot-hub-device-streams-overview.md) |   | Igen |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Igen |
| [IoT plug and play előnézet](../iot-pnp/overview-iot-plug-and-play.md) |   | Igen |

Az IoT Hub egy ingyenes csomagot is kínál, amely tesztelésre és kiértékelésre szolgál. A standard szintű, de korlátozott üzenetküldési engedmények összes képességével rendelkezik. Az ingyenes szintről nem lehet alapszintűre vagy normál szintre frissíteni.

## <a name="partitions"></a>Partíciók

Az Azure IoT Hubs az [Azure Event Hubs](../event-hubs/event-hubs-features.md)számos alapvető összetevőjét tartalmazza, beleértve [a partíciókat is.](../event-hubs/event-hubs-features.md#partitions) Az IoT Hubok eseményfolyamait általában különböző IoT-eszközök által jelentett bejövő telemetriai adatokkal töltik fel. Az eseményfolyam particionálása csökkenti az eseményfolyamok egyidejű olvasása és írása során előforduló versengések.

A partíciókorlát az IoT Hub létrehozásakor van kiválasztva, és nem módosítható. Az alapszintű IoT Hub és a standard szintű IoT Hub maximális partíciókori korlátja 32. A legtöbb IoT hubcsak 4 partíciót kell igényelni. A partíciók meghatározásáról további információt az Event Hubs gyakori kérdésében talál: [Hány partícióra van szükségem?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Réteges frissítés

Miután létrehozta az IoT hubot, frissíthet az alapszintről a standard szintre a meglévő műveletek megszakítása nélkül. További információ: [Az IoT hub frissítése.](iot-hub-upgrade.md)

A partíció konfigurációja változatlan marad, ha áttelepíti az alapszintű alapszintről a standard szintre.

> [!NOTE]
> Az ingyenes szint nem támogatja az alapszintű vagy szabványos verzióra való frissítést.

## <a name="iot-hub-rest-apis"></a>IoT hub REST API-k

A támogatott képességek közötti különbség az IoT Hub alap- és szabványos szintjei között azt jelenti, hogy egyes API-hívások nem működnek az alapszintű hubokkal. Az alábbi táblázat bemutatja, hogy mely API-k érhetők el:

| API | Alapszintű csomag | Ingyenes/standard szint |
| --- | ---------- | ------------- |
| [Eszköz törlése](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletedevice) | Igen | Igen |
| [Eszköz beszerezni](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevice) | Igen | Igen |
| [Törlés modul](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletemodule) | Igen | Igen |
| [Modul beszerezése](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getmodule) | Igen | Igen |
| [Rendszerleíró statisztika beszerezni](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevicestatistics) | Igen | Igen |
| [Szolgáltatások statisztikáinak beszereznie](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getservicestatistics) | Igen | Igen |
| [Eszköz létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatedevice) | Igen | Igen |
| [Modul létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatemodule) | Igen | Igen |
| [IoT-központ lekérdezése](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/queryiothub) | Igen | Igen |
| [Fájlfeltöltés létrehozása SAS URI](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Igen | Igen |
| [Eszközhöz kötött értesítés fogadása](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Igen | Igen |
| [Eszközesemény küldése](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Igen | Igen |
| Modulesemény küldése | Csak AMQP és MQTT | Csak AMQP és MQTT |
| [Fájlfeltöltési állapot frissítése](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Igen | Igen |
| [Ömlesztett eszköz működése](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/bulkdevicecrud) | Igen, az IoT Edge-képességek kivételével | Igen |
| [Importálási exportálási feladat megszakítása](https://docs.microsoft.com/rest/api/iothub/service/jobclient/cancelimportexportjob) | Igen | Igen |
| [Importálási exportálási feladat létrehozása](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createimportexportjob) | Igen | Igen |
| [Importálási exportálási feladat beszereznie](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjob) | Igen | Igen |
| [Importálási exportálási feladatok beszereznie](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) | Igen | Igen |
| [Parancsvárólista kiürítése](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) |   | Igen |
| [Az ikereszköz beszereznie](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) |   | Igen |
| [Ikermodul beszereznie](https://docs.microsoft.com/rest/api/iothub/service/twin/getmoduletwin) |   | Igen |
| [Eszközmetódus meghívása](https://docs.microsoft.com/rest/api/iothub/service/devicemethod/invokedevicemethod) |   | Igen |
| [Ikereszköz frissítése](https://docs.microsoft.com/rest/api/iothub/service/twin/updatedevicetwin) |   | Igen |
| [Ikermodul frissítése](https://docs.microsoft.com/rest/api/iothub/service/twin/updatemoduletwin) |   | Igen |
| [Eszközhöz kötött értesítés elhagyása](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Igen |
| [Eszközhöz kötött értesítés befejezése](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Igen |
| [Feladat megszakítása](https://docs.microsoft.com/rest/api/iothub/service/jobclient/canceljob) |   | Igen |
| [Feladat létrehozása](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createjob) |   | Igen |
| [Állás beszerezése](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getjob) |   | Igen |
| [Feladatok lekérdezése](https://docs.microsoft.com/rest/api/iothub/service/jobclient/queryjobs) |   | Igen |

## <a name="message-throughput"></a>Üzenet átviteli-átataputá

Az IoT Hub-megoldás méretezésének legjobb módja a forgalom egységenkénti kiértékelése. Különösen a műveletek következő kategóriáihoz szükséges csúcsátarányt kell figyelembe venni:

* Az eszközről a felhőbe irányuló üzenetek
* Felhőből eszközre irányuló üzenetek
* Identitásjegyzék műveletei

A forgalmat az IoT hub egységenként mérik. Amikor létrehoz egy IoT-központot, kiválasztja annak szintjét és kiadását, és beállítja a rendelkezésre álló egységek számát. A B1, B2, S1 vagy S2 kiadáshoz legfeljebb 200, a B3 vagy S3 kiadáshoz legfeljebb 10 egységet vásárolhat. Az IoT hub létrehozása után módosíthatja a kiadásán belül elérhető egységek számát, frissítheti vagy visszaminősítheti a rétegen belüli kiadások (B1-ről B2-re), vagy az alapszintről a standard szintre (B1-ről S1-re) a meglévő műveletek megszakítása nélkül frissítheti vagy visszaminősítheti. További információ: [Az IoT hub frissítése.](iot-hub-upgrade.md)  

Az egyes rétegek forgalmi képességeinek példájaként az eszközök közötti üzenetek az alábbi folyamatos átviteli irányelvek szerint:

| Tier kiadás | Tartós átmenő | Tartós küldési arány |
| --- | --- | --- |
| B1, S1 |Akár 1111 KB/perc egységenként<br/>(1,5 GB/nap/egység) |Átlagosan 278 üzenet/perc egységenként<br/>(400 000 üzenet/nap egységenként) |
| B2, S2 |Akár 16 MB/perc egységenként<br/>(22,8 GB/nap/egység) |Átlagosan 4167 üzenet/perc egységenként<br/>(6 millió üzenet/nap egységenként) |
| B3, S3 |Akár 814 MB/perc egységenként<br/>(1144,4 GB/nap/egység) |Átlagosan 208 333 üzenet/perc egységenként<br/>(300 millió üzenet/nap egységenként) |

Az eszközök közötti átviteli teljesítmény csak egyike azoknak a metrikáknak, amelyeket figyelembe kell vennie egy IoT-megoldás tervezésekor. További átfogó információkért lásd: [IoT Hub kvóták és szabályozások.](iot-hub-devguide-quotas-throttling.md)

### <a name="identity-registry-operation-throughput"></a>Identitás-beállításjegyzék-művelet átviteli fóka

Az IoT Hub identitásjegyzék-műveletei nem lehetnek futásidejű műveletek, mivel ezek többnyire az eszköz kiépítéshez kapcsolódnak.

Adott burst teljesítményszámok, lásd: [IoT Hub kvóták és szabályozások.](iot-hub-devguide-quotas-throttling.md)

## <a name="auto-scale"></a>Automatikus méretezés

Ha közeledik az Engedélyezett üzenetkorlát az IoT hubon, ezekkel a [lépésekkel automatikusan skálázhatja](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) az IoT Hub-egység növekménye ugyanabban az IoT Hub-szinten.

## <a name="next-steps"></a>További lépések

* Az IoT Hub képességeiről és a teljesítményrészleteiről az [IoT Hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub) vagy [az IoT Hub-kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md)című témakörben talál további információt.

* Az IoT Hub-szint módosításához kövesse az [IoT hub frissítése](iot-hub-upgrade.md)című lépéseket.
