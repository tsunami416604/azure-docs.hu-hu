---
title: Azure-IoT Hub összehasonlítása az Azure Event Hubs-vel | Microsoft Docs
description: A IoT Hub és Event Hubs Azure-szolgáltatások összehasonlítása a funkcionális különbségek és a használati esetek kiemelésével. Az összehasonlítás magában foglalja a támogatott protokollokat, az eszközök felügyeletét, a figyelést és a fájlok feltöltését.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733441"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT-eszközök csatlakoztatása az Azure-hoz: IoT Hub és Event Hubs

Az Azure a különböző típusú kapcsolatokhoz és kommunikációhoz kifejezetten kifejlesztett szolgáltatásokat nyújt, amelyekkel az adatai a felhő teljesítményéhez csatlakoztathatók. Az Azure IoT Hub és az Azure Event Hubs is olyan felhőalapú szolgáltatások, amelyek nagy mennyiségű adatot tölthetnek fel, és az üzleti elemzések során feldolgozhatják vagy tárolhatják azokat. A két szolgáltatás hasonló abban az esetben, ha az alacsony késésű és magas megbízhatóságú, de különböző célokra tervezett adatfeldolgozást is támogat. IoT Hub fejlesztettük ki a IoT-eszközök Azure-felhőhöz való csatlakoztatásának egyedi követelményeit, miközben a Event Hubs big data streaminghez lett tervezve. A Microsoft az Azure IoT Hub használatát javasolja a IoT-eszközök Azure-hoz való csatlakoztatásához

Az Azure IoT Hub a felhőalapú átjáró, amely összekapcsolja a IoT-eszközöket az adatok összegyűjtéséhez és az üzleti elemzések és automatizálás megszerzéséhez. Emellett IoT Hub olyan funkciókat is tartalmaz, amelyek gazdagítják az eszközök és a háttérrendszer közötti kapcsolatot. A kétirányú kommunikációs képességek azt jelentik, hogy az eszközökről érkező adatok fogadása közben parancsok és szabályzatok visszaküldését is lehetővé teszi az eszközök számára. Például a felhőből az eszközre való üzenetküldéssel frissítheti a tulajdonságokat, vagy meghívja az eszközkezelés műveleteit. A felhőből az eszközre irányuló kommunikáció lehetővé teszi a Felhőbeli intelligencia küldését a peremhálózati eszközökre Azure IoT Edge használatával. A IoT Hub által biztosított egyedi Eszközállapot-identitás segíti a IoT-megoldás jobb védelmét az esetleges támadásokkal szemben. 

Az [azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) az Azure Big Data streaming szolgáltatás. Nagy átviteli sebességű adatstreamelési helyzetekhez tervezték, ahol az ügyfelek több milliárd lekérdezést küldhetnek naponta. Az Event Hubs particionált felhasználói modellt használ a stream horizontális skálázásához, és integrálódik az Azure olyan big data- és elemzési szolgáltatásaival, mint a Databricks, a Stream Analytics, az ADLS és a HDInsight. Az olyan funkciókkal, mint a Event Hubs rögzítés és az automatikus feltöltés, ez a szolgáltatás a big data alkalmazások és megoldások támogatására szolgál. Emellett a IoT Hub Event Hubs használ a telemetria flow elérési útjához, így a IoT-megoldás a Event Hubs óriási erejét is kihasználja.

Az összegzéshez mindkét megoldás nagy léptékű adatfeldolgozásra van tervezve. A IoT-eszközök Azure-felhőhöz való csatlakoztatásának üzleti értékének maximalizálása érdekében csak a IoT Hub biztosítja a IoT-specifikus funkciókat.  Ha most kezdi a IoT, az adatfeldolgozási forgatókönyvek támogatásához IoT Hub kezdve azonnal biztosíthatja, hogy az üzleti és technikai igények kielégítése érdekében azonnali hozzáféréssel rendelkezzen a teljes funkcionalitású IoT-funkciókhoz.

Az alábbi táblázat részletesen ismerteti, hogy a IoT Hub két rétege hogyan hasonlítható össze Event Hubs a IoT képességeinek kiértékelése során. A IoT Hub standard és alapszintű szintjeivel kapcsolatos további információkért lásd: [How to Choo the right IoT hub szint](iot-hub-scaling.md).

| IoT képesség | Standard szintű IoT Hub | Alapszintű IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Eszközről a felhőbe irányuló üzenetkezelés | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |
| Protokollok: HTTPS, AMQP, AMQP websocketeken keresztül | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |
| Protokollok: MQTT, MQTT WebSocket-en keresztül | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Eszközönkénti azonosító | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Fájlfeltöltés az eszközökről | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Eszköz kiépítési szolgáltatása | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| A felhőből az eszközre irányuló üzenetküldés | ![Jelölőnégyzet][checkmark] |  |  |
| Eszközök kettős és eszközkezelés | ![Jelölőnégyzet][checkmark] |  |  |
| Eszköz streamek (előzetes verzió) | ![Jelölőnégyzet][checkmark] |  |  |
| IoT Edge | ![Jelölőnégyzet][checkmark] |  |  |

Ha az eszközről a felhőbe irányuló adatfeldolgozás csak az esetet használja, javasoljuk, hogy használja a IoT Hubt, mivel az IoT-hez tervezett szolgáltatást nyújt. 

### <a name="next-steps"></a>További lépések

A IoT Hub képességeinek további megismeréséhez tekintse meg a [IoT hub fejlesztői útmutató](iot-hub-devguide.md)című témakört.

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
