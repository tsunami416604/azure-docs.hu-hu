---
title: "Az Azure IoT-központ méretezése |} Microsoft Docs"
description: "A várható üzenet átviteli támogatásához az IoT hub méretezési módját. Az egyes rétegekhez a támogatott átviteli sebesség és a horizontális skálázási beállításokat összegzését tartalmazza."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0050b620d1cdbe95f6cf53a8e6f65271865c9e0b
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="scale-your-iot-hub-solution"></a>Az IoT hub megoldás méretezése
Az Azure IoT Hub legfeljebb egy millió egyidejűleg csatlakoztatott eszközt támogathat. További információkért lásd: [árképzési IoT-központ][lnk-pricing]. IoT Hub-egységenként lehetővé teszi, hogy egy bizonyos napi üzenetek száma.

A megoldás megfelelően méretezhető, fontolja meg az IoT-központ alkalmazását. Különösen fontolja meg a szükséges maximális átviteli sebesség műveletek az alábbi kategóriákban:

* Az eszközről a felhőbe irányuló üzenetek
* Felhő-eszközre küldött üzenetek
* Identitásjegyzék műveletei

Lásd: az átviteli információk mellett [IoT-központ kvóták és szabályozások] [ IoT Hub quotas and throttles] és ennek megfelelően a megoldás megtervezése.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Eszköz-felhő és a felhő eszközre üzenet átviteli sebesség
A legjobb módszer a következő méretre egy IoT-központ megoldás a forgalom egy egységenként kiértékeléséhez.

Eszköz a felhőbe küldött üzeneteket az alábbiakra fenntartható.

| Szint | Fenntartható | Tartós küldések |
| --- | --- | --- |
| S1 |1111 KB percenként egységenként legfeljebb<br/>(1,5 GB/nap/egység) |278 üzenetek/másodperc egységenként átlaga<br/>(400 000 üzenetek/nap / egység) |
| S2 |Legfeljebb 16 MB percenként egység<br/>(22.8 GB/nap/egység) |4,167 üzenetek/másodperc egységenként átlaga<br/>(6 millió üzenetek/nap / egység) |
| S3 |814 MB percenként egységenként legfeljebb<br/>(1144.4 GB/nap/egység) |208,333 üzenetek/másodperc egységenként átlaga<br/>(300 millió üzenetek/nap / egység) |

## <a name="identity-registry-operation-throughput"></a>Identitás beállításjegyzék művelet átviteli sebesség
IoT Hub identitás kapcsolatos műveletek nem elvileg futásidejű műveletek, mivel ezek többnyire kapcsolódó eszközök kiépítését.

Adott kapacitásnövelés számok, lásd: [IoT-központ kvóták és szabályozások][IoT Hub quotas and throttles].

## <a name="sharding"></a>Horizontális
Egyetlen IoT-központ eszközök millióira méretezhetők, amíg a megoldás specifikus teljesítményt nyújt, amely egyetlen IoT-központ nem garantálható, hogy szükség. Ebben az esetben ajánlott, hogy az IoT-központok több partíció az eszközök. Több IoT-központok forgalom felszakadásáig sima, és szerezze be a szükséges átviteli vagy a művelet díjszabás szükséges.

## <a name="next-steps"></a>Következő lépések
Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Egy eszköz szimulálva Azure IoT oldala][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
