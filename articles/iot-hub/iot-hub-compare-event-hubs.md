---
title: Az Azure IoT Hub és az Azure Event Hubs összehasonlítása |} A Microsoft Docs
description: Az IoT Hub és az Event Hubs Azure szolgáltatások funkcionális eltérések és a használati esetek menüpont kiemelve összehasonlítása. Összehasonlítását tartalmazza a támogatott protokollok, kezelés, figyelés, és feltölti a fájlt.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669738"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT-eszközök csatlakoztatása az Azure-bA: Az IoT Hub és az Event Hubs

Az Azure kifejezetten fejlesztette ki kapcsolatot és a kommunikáció segítségével csatlakoztatta az adatait a felhő képességeit a különböző típusú szolgáltatásokat biztosít. Azure IoT Hub és az Azure Event Hubs olyan felhőszolgáltatás, amely képes feldolgozni az adatokat és dolgozhat fel nagy mennyiségű vagy üzleti elemzések készítése adatok tárolására. A két szolgáltatás hasonlóak, hogy mindkét támogatják-e az adatok feldolgozási, alacsony késéssel és magas megbízhatósággal, de azokat különböző felhasználási célokra tervezték. Az IoT Hub IoT-eszközök csatlakoztatása és az Azure felhőbe big Data típusú adatok streamelési készült Event Hubs egyedi követelményeinek célja. A Microsoft azt javasolja, Azure IoT Hub IoT-eszközök csatlakoztatása az Azure-ba való használatával

Az Azure IoT Hub, amely csatlakozik az IoT-eszközök gyűjtsön adatokat és az üzleti elemzések és az automation, a felhőátjárónak. Ezenkívül az IoT Hub szolgáltatásai bővítését az eszközök és a háttérrendszerrel közötti kapcsolatot. Kétirányú kommunikációs lehetőségeket jelenti azt, hogy közben adatokat fogad az eszközökről is küldhet parancsokat, és eszközök biztonsági szabályzatokat. Például használatával felhőből az eszközre irányuló üzenetküldés tulajdonságainak frissítése vagy az eszköz felügyeleti tevékenységeket. Felhő és eszköz közötti kommunikációt lehetővé teszi a peremhálózati eszközökre az Azure IoT Edge felhőbeli intelligens technológiák küldeni. Az eszközszintű egyedi azonosítóval az IoT Hub segít jobban biztonságos által biztosított az IoT-megoldás lehetséges támadások ellen. 

[Az Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) a big Data típusú adatok az Azure service folyamatos. Nagy átviteli sebességű adatok streamelési forgatókönyvek ahol ügyfelek küldhet kéréseket naponta több milliárd lett tervezve. Az Event Hubs egy particionált felhasználói modell használ a stream horizontális, és a big data- és elemzési szolgáltatások az Azure Databricks, a Stream Analytics, az ADLS és a HDInsight integrálva van. Olyan funkciókkal, mint az Event Hubs Capture és az automatikus feltöltési ezt a szolgáltatást úgy tervezték, a big data-alkalmazások és megoldások támogatása. Ezenkívül az IoT Hub az Event Hubs segítségével a telemetriát a folyamat elérési útvonalát, így az IoT-megoldás is számos előnyt biztosít az Event Hubs áttekintse hatékonyságát.

Összefoglalva, a két megoldás nagy méretű, adatbetöltés lettek kialakítva. Csak az IoT Hub a gazdag IoT-specifikus funkciókat biztosít, amelyek célja az IoT-eszközök csatlakoztatása az Azure-felhő üzleti értékének maximalizálását.  Ha csak most ismerkedik az IoT-feladatok, kezdve az IoT hubot, hogy az adatok betöltési helyzetek feltételeinek megteremtésére biztosítaná a, hogy azonnal hozzáférhet a teljes körű IoT-képességeik után az üzleti és technikai kell hitelesíteni.

Az alábbi táblázat ismerteti, hogyan a két réteg IoT-központ összehasonlítása az Event hubs szolgáltatásba, amikor a mérlegeli őket az IoT-képességeik részleteit. A standard és alapszintű csomag, az IoT Hub kapcsolatos további információkért lásd: [a megfelelő IoT Hub-csomag kiválasztása](iot-hub-scaling.md).

| IoT-képesség | Az IoT Hub standard szintű csomag | Alapszintű IoT Hub-csomag | Event Hubs |
| --- | --- | --- | --- |
| Eszközről-a-felhőbe üzenetkezelés | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |
| Protokoll: A websockets protokoll keresztül HTTPS, AMQP és AMQP | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |
| Protokoll: Mqtt-ről, a websockets protokoll keresztüli mqtt-ről | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Eszközönkénti identitás | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Fájlfeltöltés az eszközökről | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Device Provisioning Service | ![Jelölőnégyzet][checkmark] | ![Jelölőnégyzet][checkmark] |  |
| Üzenetküldés a felhőből eszközökre | ![Jelölőnégyzet][checkmark] |  |  |
| Ikereszköz- és eszközkezelés | ![Jelölőnégyzet][checkmark] |  |  |
| Eszköz adatfolyamok (előzetes verzió) | ![Jelölőnégyzet][checkmark] |  |  |
| IoT Edge | ![Jelölőnégyzet][checkmark] |  |  |

Akkor is, ha csak a használati eset eszközről a felhőbe – az adatbetöltés, erősen ajánlott egy szolgáltatás, amely lehetővé teszi az IoT-eszköz csatlakoztatása az IoT Hub használatával. 

### <a name="next-steps"></a>További lépések

Részletesebb megismerése az IoT Hub képességeit, tekintse meg a [IoT Hub fejlesztői útmutatójának](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
