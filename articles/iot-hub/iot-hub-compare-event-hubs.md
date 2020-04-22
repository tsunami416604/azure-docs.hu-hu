---
title: Az Azure IoT Hub és az Azure Event Hubs összehasonlítása | Microsoft dokumentumok
description: Az IoT Hub és az Event Hubs Azure-szolgáltatások összehasonlítása, amely kiemeli a funkcionális különbségeket és a használati eseteket. Az összehasonlítás tartalmazza a támogatott protokollokat, az eszközkezelést, a figyelést és a fájlfeltöltéseket.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7aa59f8181ad60b3d43846a3f4f1f471a050b238
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733441"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT-eszközök csatlakoztatása az Azure-hoz: IoT Hub és Event Hubs

Az Azure kifejezetten a különböző típusú kapcsolatokhoz és kommunikációhoz kifejlesztett szolgáltatásokat nyújt, amelyek segítségével adatokat csatlakoztathat a felhő erejéhez. Az Azure IoT Hub és az Azure Event Hubs egyaránt olyan felhőalapú szolgáltatások, amelyek nagy mennyiségű adatot tudnak bedolgozni, és ezeket az adatokat üzleti elemzési célokra feldolgozni vagy tárolni. A két szolgáltatás hasonló, mivel mindkettő támogatja az adatok alacsony késésű és magas megbízhatóságú betöltését, de különböző célokra vannak tervezve. Az IoT Hubot úgy fejlesztették ki, hogy megfeleljen az IoT-eszközök Azure-felhőhöz való csatlakoztatásának egyedi követelményeinek, miközben az Event Hubs-t big data-adatfolyam-továbbításra tervezték. A Microsoft azt javasolja, hogy az Azure IoT Hub használatával csatlakoztassa az IoT-eszközöket az Azure-hoz

Az Azure IoT Hub egy felhőátjáró, amely összeköti az IoT-eszközöket az adatok gyűjtéséhez és az üzleti elemzések és automatizálás ösztönzéséhez. Emellett az IoT Hub olyan funkciókat is tartalmaz, amelyek gazdagítják az eszközök és a háttérrendszerek közötti kapcsolatot. A kétirányú kommunikációs képességek azt jelentik, hogy miközben adatokat fogad az eszközökről, parancsokat és házirendeket is küldhet vissza az eszközökre. Például a felhőből az eszközre irányuló üzenetküldés segítségével frissítse a tulajdonságokat, vagy hívja meg az eszközkezelési műveleteket. A felhőből az eszközre irányuló kommunikáció lehetővé teszi, hogy az Azure IoT Edge segítségével felhőalapú intelligenciát küldjön a peremhálózati eszközökre. Az IoT Hub által biztosított egyedi eszközszintű identitás segít jobban megvédeni az IoT-megoldást a lehetséges támadásoktól. 

[Az Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) az Azure big data streamelési szolgáltatása. Nagy átviteli sebességű adatstreamelési helyzetekhez tervezték, ahol az ügyfelek több milliárd lekérdezést küldhetnek naponta. Az Event Hubs particionált felhasználói modellt használ a stream horizontális skálázásához, és integrálódik az Azure olyan big data- és elemzési szolgáltatásaival, mint a Databricks, a Stream Analytics, az ADLS és a HDInsight. Az olyan funkciókkal, mint az Event Hubs Capture és az Auto-Inflate, ez a szolgáltatás támogatja a big data-alkalmazásokat és -megoldásokat. Emellett az IoT Hub az Event Hubs telemetriai folyamatútvonalát használja, így az IoT-megoldás is kihasználja az Event Hubs hatalmas erejét.

Összefoglalva, mindkét megoldás nagy méretű adatbetöltésre van tervezve. Csak az IoT Hub biztosítja a gazdag IoT-specifikus képességeket, amelyek et úgy tervezte, hogy maximalizálja az IoT-eszközök azure-felhőhöz való csatlakoztatásának üzleti értékét.  Ha az IoT-utazás csak most kezdődik, kezdve az IoT Hub az adatok betöltési forgatókönyvek támogatása biztosítja, hogy azonnali hozzáférést biztosít a teljes funkcionalitású IoT-képességek, ha az üzleti és műszaki igények et.

Az alábbi táblázat bemutatja, hogyan viszonyulaz IoT Hub két szintje az Event Hubs-hoz, amikor kiértékeli őket az IoT-képességek szempontjából. Az IoT Hub alap- és alapszintjeiről a [Megfelelő IoT Hub-szint kiválasztása](iot-hub-scaling.md)című témakörben talál további információt.

| IoT-képesség | Az IoT Hub standard szintje | Az IoT Hub alapszintje | Event Hubs |
| --- | --- | --- | --- |
| Eszközről felhőbe irányuló üzenetküldés | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |
| Protokollok: HTTPS, AMQP, AMQP webSockets felett | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |
| Protokollok: MQTT, MQTT webSockets felett | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Eszközönkénti identitás | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Fájlfeltöltés eszközökről | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Eszközkiépítési szolgáltatás | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Felhőből az eszközre irányuló üzenetküldés | ![Jelölőnégyzet][checkmark] |  |  |
| Az ikereszköz és az eszközkezelés | ![Jelölőnégyzet][checkmark] |  |  |
| Eszközadatfolyamok (előzetes verzió) | ![Jelölőnégyzet][checkmark] |  |  |
| IoT Edge | ![Jelölőnégyzet][checkmark] |  |  |

Még akkor is, ha az egyetlen használati eset az eszközről a felhőbe történő adatbetöltés, javasoljuk az IoT Hub használatát, mivel olyan szolgáltatást biztosít, amely et IoT-eszközkapcsolathoz terveztek. 

### <a name="next-steps"></a>További lépések

Az IoT Hub képességeinek további megismeréséhez tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
