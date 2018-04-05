---
title: Az Azure Event hubs az Azure IoT Hub összehasonlítása |} Microsoft Docs
description: A használati esetek és működési különbségeket kiemelés az IoT-központ és az Event Hubs Azure szolgáltatások összehasonlítása. Az összehasonlítás tartalmazza a támogatott protokollok, kezelés, figyelés, és fájlfeltöltések.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Az Azure IoT-központ és az Azure Event Hubs összehasonlítása

Azure IoT Hub, mind az Azure Event Hubs olyan felhőszolgáltatások, amelyek nagy mennyiségű adatok és a folyamat betöltési vagy üzleti elemzések készítése adatok tárolásához. A két szolgáltatás hasonlóak az, hogy mindkét támogatják-e feldolgozni az esemény- és telemetriabevitelt adatokat, alacsony késéssel és nagy megbízhatósággal. A skála internet dolgot forgatókönyvek támogatásához szükséges adott lehetőségeinek azonban csak az IoT-központ jött létre. 

Az Azure IoT Hub a felhő átjáró, amely összekapcsolja az eszközöket, és üzleti elemzések készítése és automatizálás adatokat gyűjt. Megkönnyíti a felhőre adatok folyamatos átviteléhez és léptékű az eszközök kezeléséhez. Az IoT-központ és az egyéb adatok adatfeldolgozást szolgáltatások közötti, egy fontos különbséget, hogy az IoT-központ szolgáltatásokat tartalmaz, amelyek funkciógazdagabbá teheti az eszközök és a háttérkiszolgáló rendszerek közötti kapcsolat. Kétirányú kommunikáció képességeknek, hogy közben az adatok fogadása eszközök is küldhet üzeneteket biztonsági modulokhoz történő tulajdonságainak frissítése vagy meg kíván hívni egy műveletet. Eszközszintű identitás segít biztosítani a rendszer. Az elosztott számítástechnikai helyezi át felhőalapú szolgáltatás logika peremeszközök helyezik.

[Az Azure Event Hubs] [ Azure Event Hubs] esemény adatfeldolgozást szolgáltatás, amely egyszerűen feldolgozhatja és nagy mennyiségű adatok és a telemetriai adatok tárolásához. Az Event Hubs egy nagy méretű, mindkettő többek datacenter és intra-datacenter forgatókönyvek környezetében esemény adatfeldolgozást készült, de nem adja meg, hogy az IoT-központ sokoldalú IoT-specifikus képességeit. Éppen ezért nem javasoljuk az Event Hubs a IoT-megoldások. 

A következő táblázat ismerteti, hogyan a két réteg IoT hub hasonlítsa össze az Event Hubs értékelése őket IoT képességeinek során. A standard és alapszintű rétegek IoT hub kapcsolatos további információkért lásd: [kiválasztása a megfelelő IoT-központ réteg][lnk-scaling].

| Az IoT-funkció | Az IoT-központ szabványos réteg | Az IoT-központ az alapszintű csomag | Event Hubs |
| --- | --- | --- | --- |
| Üzenetküldési eszközről a felhőbe | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Protokollok: HTTPS-en AMQP, AMQP keresztül websocket elemek | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Protokollok: MQTT, MQTT keresztül websocket elemek | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Eszköz identitás | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Fájlfeltöltés az eszközökről | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Device Provisioning Service | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Felhő eszközre üzenetkezelés | ![Jelölőnégyzet][1] |  |  |
| A két eszköz és kezelése | ![Jelölőnégyzet][1] |  |  |
| IoT Edge | ![Jelölőnégyzet][1] |  |  |

Akkor is, ha csak a használati eset eszközről a felhőbe adatfeldolgozást, erősen ajánlott az IoT-központ használatával, mint egy szolgáltatás, amely úgy van kialakítva, biztosít az IoT-eszköz kapcsolatot. 

### <a name="next-steps"></a>További lépések

Az IoT-központ képességeit további megismeréséhez lásd: a [IoT Hub fejlesztői útmutató][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png