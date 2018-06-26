---
title: Az Azure IoT-központ méretezése |} Microsoft Docs
description: A várható üzenet átviteli sebesség és a kívánt funkciók támogatásához az IoT hub méretezési módját. Az egyes rétegekhez a támogatott átviteli sebesség és a horizontális skálázási beállításokat összegzését tartalmazza.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: b7751bd1b309333d5ef40530b0fa499a42a57cd1
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752248"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>A megoldás a megfelelő IoT-központ szint kiválasztása

Minden IoT-megoldás eltér, így Azure IoT Hub tarifa- és alapján több lehetőséget kínál. Ez a cikk arra szolgál, hogy az IoT-központ igényeinek kiértékeléséhez. Díjszabási IoT-központ rétegek kapcsolatos információkért tekintse meg [árképzési IoT-központ](https://azure.microsoft.com/pricing/details/iot-hub). 

Döntse el, mely az IoT-központ érvényben lévő korlát miatt a megoldás megfelelő, tegye fel magának két kérdéseket:

**Mely szolgáltatások tegye tervez használni?**
Azure IoT Hub kínál a két réteg, egyszerű és szabványos, amely nem egyezik a szolgáltatások támogatják-e száma. Ha az IoT-megoldásból alapú eszközök adatainak begyűjtése és elemzése azt központilag alapszintű rétegben valószínűleg jobb meg. Ha az IoT-eszközök vezérlését vagy terjeszteni a munkaterhelés összevonásához a eszközöktől néhány speciális konfigurációk használni kívánt majd fontolja meg a standard csomagra. A funkciókat, amelyek minden egyes rétegben tartalmaz részletes információkat továbbra is [egyszerű és szabványos rétegek](#basic-and-standard-tiers).

**Mennyi adatot tegye tervezi a naponta áthelyezése?**
Minden egyes IoT-központ réteg érhető három méretben kaphatók, alapú körül hány adatátvitelt azok kezelni tud a bármely adott napon belül. Ezek méretek szám 1, 2 és 3 azonosítja. Például egy 1. szintű IoT-központ egységenként kezelni tud a 400 ezer üzenetek napi, amíg a 3. szint egység kezelni tud a 300 millió. További információt az adatok útmutatást, továbbra is [üzenet átviteli](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Basic és standard rétegek

A standard szint IoT-központ lehetővé teszi, hogy minden funkció, és bármely, kíván tenni az IoT-megoldások működéséhez szükség a kétirányú kommunikáció képességeit használja. Az alapszintű rétegben lehetővé teszi a funkciók egy részét, és célja, hogy az IoT-megoldások, amelyeket csak egyirányú a felhőben csatlakozó eszközökről érkező kommunikációt. Mindkét rétegen nyújtott biztonsági és hitelesítési szolgáltatások.

Az IoT hub létrehozása után frissítheti az alapszintű rétegben standard csomagra a meglévő műveletek megszakítása nélkül. További információkért lásd: [az IoT-központ frissítésével](iot-hub-upgrade.md).

| Képesség | Alapszintű csomag | Standard csomag |
| ---------- | ---------- | ------------- |
| [Eszköz-felhő telemetriai adat](iot-hub-devguide-messaging.md) | Igen | Igen |
| [Eszköz identitás](iot-hub-devguide-identity-registry.md) | Igen | Igen |
| [Üzenet-útválasztás](iot-hub-devguide-messages-read-custom.md) és [esemény rács integráció](iot-hub-event-grid.md) | Igen | Igen |
| [HTTP AMQP és MQTT protokollok](iot-hub-devguide-protocols.md) | Igen | Igen |
| [Eszközök telepítési szolgáltatásával](../iot-dps/about-iot-dps.md) | Igen | Igen |
| [Megfigyelési és diagnosztikai](iot-hub-monitor-resource-health.md) | Igen | Igen |
| [Felhő eszközre üzenetkezelés](iot-hub-devguide-c2d-guidance.md) |   | Igen |
| [Eszköz twins](iot-hub-devguide-device-twins.md), [modul twins](iot-hub-devguide-module-twins.md) és [Eszközkezelés](iot-hub-device-management-overview.md) |   | Igen |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Igen |

Az IoT-központ is biztosít, egy ingyenes szint, amely készült tesztelésében és értékelésében. A standard szint, de korlátozott üzenetkezelési támogatás minden képességet tartalmaz. Ingyenes szint az alapszintű vagy standard nem verzióról. 

### <a name="iot-hub-rest-apis"></a>IoT Hub REST API-k

A támogatott képességek közötti különbség a basic és standard rétegek IoT-központ azt jelenti, hogy néhány API-hívások nem működnek az alapszintű rétegben hubs használatával. A következő táblázat azt mutatja, melyik API-k áll rendelkezésre: 

| API | Alapszintű csomag | Standard csomag |
| --- | ---------- | ------------- |
| [Eszköz törlése](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/deletedevice) | Igen | Igen |
| [Eszköz](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getdevice) | Igen | Igen |
| A modul törlése | Igen | Igen |
| Modul beolvasása | Igen | Igen |
| [Megtekintheti a beállításjegyzék statisztikákat](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getdeviceregistrystatistics) | Igen | Igen |
| [Get-szolgáltatásokra vonatkozó statisztikák](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getservicestatistics) | Igen | Igen |
| [Helyezze az eszközt](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | Igen | Igen |
| PUT modul | Igen | Igen |
| [Eszközök lekérdezése](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | Igen | Igen |
| Lekérdezés-modulok | Igen | Igen |
| [Fájl feltöltése SAS URI-t létrehozni](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/createfileuploadsasuri) | Igen | Igen |
| [Eszköz kötött értesítés](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/receivedeviceboundnotification) | Igen | Igen |
| [Esemény küldése](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/senddeviceevent) | Igen | Igen |
| A modul esemény küldése | Igen | Igen |
| [Fájl feltöltése állapotának frissítése](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/updatefileuploadstatus) | Igen | Igen |
| [Tömeges eszköz művelet](https://docs.microsoft.com/en-us/rest/api/iot-dps/deviceenrollment/bulkoperation) | Igen, kivéve a IoT biztonsági képességei | Igen | 
| [A parancs várólista törlése](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/purgecommandqueue) |   | Igen |
| [A két eszköz beolvasása](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/gettwin) |   | Igen |
| A két modul beolvasása |   | Igen |
| [Eszköz metódus meghívása](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/invokedevicemethod) |   | Igen |
| [A két eszköz frissítése](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/updatetwin) |   | Igen | 
| A modul iker frissítése |   | Igen | 
| [Eszköz kötött értesítési Abandon](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/abandondeviceboundnotification) |   | Igen |
| [Teljes eszköz értesítést kötött](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/completedeviceboundnotification) |   | Igen |
| [Megszakítása](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/canceljob) |   | Igen |
| [Feladat létrehozása](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/createjob) |   | Igen |
| [Feladat beolvasása](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getjob) |   | Igen |
| [Lekérdezés feladatok](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/queryjobs) |   | Igen |

## <a name="message-throughput"></a>Üzenet átviteli sebesség

A legjobb módszer a következő méretre egy IoT-központ megoldás a forgalom egy egységenként kiértékeléséhez. Különösen fontolja meg a szükséges maximális átviteli sebesség műveletek az alábbi kategóriákban:

* Az eszközről a felhőbe irányuló üzenetek
* Felhő-eszközre küldött üzenetek
* Identitásjegyzék műveletei

Egy egységenként, nem / hub a forgalom mérése történik. Egy szint, 1 vagy 2 IoT-központ példánya társítva akár 200 egység is lehet. A 3. szint IoT-központ példánya legfeljebb 10 egység is lehet. Az IoT hub létrehozása után egységek számának módosítása, vagy helyezze át az 1, 2 és 3 méretű belül egy adott réteg között a meglévő műveletek megszakítása nélkül. További információkért lásd: [az IoT-központ frissítésével](iot-hub-upgrade.md).

Minden egyes réteg forgalom képességeket például eszköz a felhőbe küldött üzeneteket az alábbiakra fenntartható:

| Szint | Fenntartható | Tartós küldések |
| --- | --- | --- |
| B1, S1 |1111 KB percenként egységenként legfeljebb<br/>(1,5 GB/nap/egység) |278 üzenetek/másodperc egységenként átlaga<br/>(400 000 üzenetek/nap / egység) |
| B2, S2 |Legfeljebb 16 MB percenként egység<br/>(22.8 GB/nap/egység) |4,167 üzenetek/másodperc egységenként átlaga<br/>(6 millió üzenetek/nap / egység) |
| B3, S3 |814 MB percenként egységenként legfeljebb<br/>(1144.4 GB/nap/egység) |208,333 üzenetek/másodperc egységenként átlaga<br/>(300 millió üzenetek/nap / egység) |

Lásd: az átviteli információk mellett [IoT-központ kvóták és szabályozások] [ IoT Hub quotas and throttles] és ennek megfelelően a megoldás megtervezése.

### <a name="identity-registry-operation-throughput"></a>Identitás beállításjegyzék művelet átviteli sebesség
IoT Hub identitás kapcsolatos műveletek nem elvileg futásidejű műveletek, mivel ezek többnyire kapcsolódó eszközök kiépítését.

Adott kapacitásnövelés számok, lásd: [IoT-központ kvóták és szabályozások][IoT Hub quotas and throttles].

## <a name="sharding"></a>Sharding
Egyetlen IoT-központ eszközök millióira méretezhetők, amíg a megoldás specifikus teljesítményt nyújt, amely egyetlen IoT-központ nem garantálható, hogy szükség. Ebben az esetben az eszközök is partícióazonosító több IoT-központok között. Több IoT-központok forgalom felszakadásáig sima, és szerezze be a szükséges átviteli vagy a művelet díjszabás szükséges.

## <a name="next-steps"></a>További lépések

* További információ a IoT-központ képességek és a teljesítmény részletei: [IoT-központ díjszabás] [hivatkozás – díjszabás] vagy [IoT-központ kvóták és szabályozások][IoT Hub quotas and throttles].
* Az IoT-központ réteg, kövesse a lépéseket [frissítse az IoT hub](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
