---
title: Az Azure IoT Hub felhőből az eszközre lehetőségei |} A Microsoft Docs
description: Fejlesztői útmutató – mikor érdemes használni a közvetlen metódusok, ikereszköz a kívánt tulajdonságok, vagy a felhőből az eszközre irányuló üzenetek felhőből eszközre irányuló kommunikáció útmutatóját.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 4b738f34ae75478c0120832e7ad2b6a6a83dbf69
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010701"
---
# <a name="cloud-to-device-communications-guidance"></a>Felhőből az eszközre irányuló kommunikáció útmutatóját

Az IoT Hub egy háttéralkalmazás funkciókat elérhetővé készült három lehetőséget kínál:

* [Közvetlen metódusok](iot-hub-devguide-direct-methods.md) az eredmény az azonnali megerősítési igénylő kommunikációhoz. Közvetlen metódusok gyakran használják interaktív ellenőrzésére szolgáló eszközök, például ne tudják bekapcsolni a egy ventilátor.

* [Ikereszköz kívánt tulajdonságait](iot-hub-devguide-device-twins.md) a hosszan futó parancsokat üzembe helyezhető az eszköz egy bizonyos szánt kívánt állapot. Például állítsa be a telemetriai adatok küldési időköz – 30 percet.

* [Felhőből az eszközre irányuló üzenetek](iot-hub-devguide-messages-c2d.md) egyirányú értesítéseket az eszközalkalmazáshoz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Íme a felhőből az eszközre irányuló kommunikáció különböző lehetőségek részletes összehasonlítását láthatja.

|  | Közvetlen metódusok | Ikereszköz kívánt tulajdonságai | Felhőből az eszközre irányuló üzenetek |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Azonnali megerősítési, például egy ventilátor bekapcsolásával igénylő parancsok. | Hosszan futó parancsokat célja, hogy az eszköz elhelyezi egy bizonyos célállapotát. Például állítsa be a telemetriai adatok küldési időköz – 30 percet. | Az eszközalkalmazás egyirányú értesítések. |
| Az adatfolyam | Kétirányú. Az eszközalkalmazás azonnal válaszolhat a metódus. A megoldás háttérrendszere megkapja a kérést, de az eredménye. | Egyirányú. Az eszközalkalmazás tulajdonság módosítás értesítést kap. | Egyirányú. Az eszköz alkalmazás fogadja az üzeneteket
| Tartósság | A leválasztott eszközöket nem próbál kapcsolódni. A megoldás háttérrendszere értesítést kap, hogy az eszköz nem kapcsolódik. | Tulajdonságértékek megmaradnak az ikereszközben. Eszköz a következő újracsatlakozás olvasni. A tulajdonságértékek lekérhető a a [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md). | Üzenetek akár 48 órát megőrzésének IoT Hub által. |
| Célok | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok](iot-hub-devguide-jobs.md). | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok](iot-hub-devguide-jobs.md). | Egyetlen eszköz által **deviceId**. |
| Méret | Maximális közvetlen metódus hasznos adatainak mérete 128 KB-os. | Maximális kívánt tulajdonságok mérete 8 KB-os. | Legfeljebb 64 KB-os üzenetet. |
| Gyakoriság | Magas. További információkért lásd: [korlátozza az IoT Hub](iot-hub-devguide-quotas-throttling.md). | Közepes. További információkért lásd: [korlátozza az IoT Hub](iot-hub-devguide-quotas-throttling.md). | Alacsony. További információkért lásd: [korlátozza az IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Mqtt-ről vagy AMQP használatával akkor érhető el. | Mqtt-ről vagy AMQP használatával akkor érhető el. | Elérhető az összes protokollon. Eszköz kell elindítja a lekérdezést, amikor HTTPS-en keresztül. |

Ismerje meg, hogyan használhatja a közvetlen metódusok, a kívánt tulajdonságok és a felhőből az eszközre irányuló üzenetek az alábbi oktatóanyagok:

* [Közvetlen metódusok használata](quickstart-control-device-node.md)
* [Eszközök konfigurálása a kívánt tulajdonságok használatával](tutorial-device-twins.md) 
* [Üzenetküldés a felhőből az eszközre](iot-hub-node-node-c2d.md)