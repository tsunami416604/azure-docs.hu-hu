---
title: Az Azure Event hubs az Azure IoT Hub összehasonlítása |} Microsoft Docs
description: A használati esetek és működési különbségeket kiemelés az IoT-központ és az Event Hubs Azure szolgáltatások összehasonlítása. Az összehasonlítás tartalmazza a támogatott protokollok, kezelés, figyelés, és fájlfeltöltések.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 9ad95071de07777e38533ecec9e8558841d8b1ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633961"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Csatlakozás Azure IoT-eszközök: az IoT-központ és az Event Hubs

Azure-szolgáltatások kifejezetten fejlesztett eltérő típusú kapcsolat és a kommunikációs segítséget nyújtanak a felhő hatványa való összekapcsolásához biztosítja. Azure IoT Hub, mind az Azure Event Hubs olyan felhőszolgáltatások, amelyek nagy mennyiségű adatok és a folyamat betöltési vagy üzleti elemzések készítése adatok tárolásához. A két szolgáltatás hasonlóak az, hogy mindkét támogatják az adatok adatfeldolgozást, alacsony késéssel és nagy megbízhatósággal, de különböző célokra tervezték. Az IoT-központ kifejezetten az IoT-eszközök, a méretezés, kapcsolódik az Azure-felhőbe, miközben az Event Hubs úgy lett kialakítva, a big data streaming egyedi követelményeinek jött létre. Ezért a Microsoft azt javasolja, hogy Azure IoT Hub használatával csatlakozik az Azure IoT-eszközök

Az Azure IoT Hub az átjáró, amely összeköti az IoT-eszközök meghajtó üzleti elemzések készítése és automatizálás az adatok gyűjtéséhez. Továbbá az IoT-központ szolgáltatásai funkciógazdagabbá teheti az eszközök és a háttérkiszolgáló rendszerek közötti kapcsolat. Kétirányú kommunikáció képességeknek, hogy közben az adatok fogadása eszközök is visszaküldi parancsok és házirendek eszközök, például tulajdonságainak frissítése vagy az eszköz felügyeleti műveletek meghívása.  A felhő-eszköz kapcsolatot is fontos képességet felhő eszközintelligencia kézbesíti a peremeszközök Azure IoT oldala megoldásaira épül. Az IoT-központ segítségével nagyobb biztonsága érdekében az IoT-megoldásból potenciális támadások szolgáltatja eszközszintű egyedi azonosítója. 

[Az Azure Event Hubs] [ Azure Event Hubs] a big Data típusú adatok Azure szolgáltatás streaming. A magas teljesítmény adatáttelepítések adatfolyam esetében ahol az ügyfelek kérelem naponta milliárd küldhetnek tervezték. Az Event Hubs egy particionált felhasználói modell segítségével az adatfolyam réteget, és integrálva van a big Data típusú adatok és az Azure Databricks, a Stream Analytics, a ADLS és a HDInsight-analytics szolgáltatást. A szolgáltatások, mint az Event Hubs rögzítése, és automatikusan megnöveli, a szolgáltatás használatával támogatja a big Data típusú adatok alkalmazások és -megoldások. Emellett IoT-központ kihasználja az Event Hubs a telemetriai adatok folyamata elérési úttal, az IoT-megoldásból is előnyeivel az Event hubs rengeteg power.

Összefoglalva, amíg a két megoldás készültek, nagy léptékű adatfeldolgozást, csak az IoT-központ a gazdag IoT-specifikus képességeket biztosít, amelyek ahhoz, hogy az IoT-eszközök Azure felhőben történő csatlakozás az üzleti érték maximalizálása érdekében.  Ha a IoT használatában csak verziótól kezdve IoT-központ az adatok adatfeldolgozást helyzetek feltételeinek megteremtésére biztosítaná a, hogy rendelkezik a teljes körű IoT funkciókat azonnali hozzáférési, amennyiben az a műszaki és üzleti igényeket igénylik.

A következő táblázat ismerteti, hogyan a két réteg IoT hub hasonlítsa össze az Event Hubs értékelése őket IoT képességeinek során. A standard és alapszintű rétegek IoT hub kapcsolatos további információkért lásd: [kiválasztása a megfelelő IoT-központ réteg][lnk-scaling].

| Az IoT-funkció | Az IoT-központ szabványos réteg | Az IoT-központ az alapszintű csomag | Event Hubs |
| --- | --- | --- | --- |
| Üzenetküldési eszközről a felhőbe | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Protokollok: HTTPS-en AMQP, AMQP keresztül websocket elemek | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Protokollok: MQTT, MQTT keresztül websocket elemek | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Eszközönkénti identitás | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Fájlfeltöltés az eszközökről | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Device Provisioning Service | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |  |
| Üzenetküldés a felhőből eszközökre | ![Jelölőnégyzet][1] |  |  |
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
