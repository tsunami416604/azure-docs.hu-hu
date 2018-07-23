---
title: Az Azure IoT Hub felhőből az eszközre lehetőségei |} A Microsoft Docs
description: Fejlesztői útmutató – mikor érdemes használni a közvetlen metódusok, ikereszköz a kívánt tulajdonságok, vagy a felhőből az eszközre irányuló üzenetek felhőből eszközre irányuló kommunikáció útmutatóját.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 2cc9bd39371741caaa3ae025df494e225dc754b0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187036"
---
# <a name="cloud-to-device-communications-guidance"></a>Felhőből az eszközre irányuló kommunikáció útmutatóját
Az IoT Hub egy háttéralkalmazás funkciókat elérhetővé készült három lehetőséget kínál:

* [Közvetlen metódusok] [ lnk-methods] kommunikációhoz szükséges azonnali erősítse meg az eredményt. Közvetlen metódusok gyakran használják interaktív ellenőrzésére szolgáló eszközök, például ne tudják bekapcsolni a egy ventilátor.
* [Ikereszköz kívánt tulajdonságait] [ lnk-twins] a hosszan futó parancsokat üzembe helyezhető az eszköz egy bizonyos szánt kívánt állapot. Például állítsa be a telemetriai adatok küldési időköz – 30 percet.
* [Felhőből az eszközre irányuló üzenetek] [ lnk-c2d] egyirányú értesítéseket az eszközalkalmazáshoz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Íme a felhőből az eszközre irányuló kommunikáció különböző lehetőségek részletes összehasonlítását láthatja.

|  | Közvetlen metódusok | Ikereszköz kívánt tulajdonságai | Felhőből az eszközre irányuló üzenetek |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Azonnali megerősítési, például egy ventilátor bekapcsolásával igénylő parancsok. | Hosszan futó parancsokat célja, hogy az eszköz elhelyezi egy bizonyos célállapotát. Például állítsa be a telemetriai adatok küldési időköz – 30 percet. | Az eszközalkalmazás egyirányú értesítések. |
| Az adatfolyam | Kétirányú. Az eszközalkalmazás azonnal válaszolhat a metódus. A megoldás háttérrendszere megkapja a kérést, de az eredménye. | Egyirányú. Az eszközalkalmazás tulajdonság módosítás értesítést kap. | Egyirányú. Az eszköz alkalmazás fogadja az üzeneteket
| Tartósság | A leválasztott eszközöket nem próbál kapcsolódni. A megoldás háttérrendszere értesítést kap, hogy az eszköz nem kapcsolódik. | Tulajdonságértékek megmaradnak az ikereszközben. Eszköz a következő újracsatlakozás olvasni. A tulajdonságértékek lekérhető a a [IoT Hub lekérdezési nyelv][lnk-query]. | Üzenetek akár 48 órát megőrzésének IoT Hub által. |
| Célok | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok][lnk-jobs]. | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok][lnk-jobs]. | Egyetlen eszköz által **deviceId**. |
| Méret | Maximális közvetlen metódus hasznos adatainak mérete 128 KB-os. | Maximális kívánt tulajdonságok mérete 8 KB-os. | Legfeljebb 64 KB-os üzenetet. |
| Gyakoriság | Magas. További információkért lásd: [korlátozza az IoT Hub][lnk-quotas]. | Közepes. További információkért lásd: [korlátozza az IoT Hub][lnk-quotas]. | Alacsony. További információkért lásd: [korlátozza az IoT Hub][lnk-quotas]. |
| Protokoll | Mqtt-ről vagy AMQP használatával akkor érhető el. | Mqtt-ről vagy AMQP használatával akkor érhető el. | Elérhető az összes protokollon. Eszköz kell elindítja a lekérdezést, amikor HTTPS-en keresztül. |

Ismerje meg, hogyan használhatja a közvetlen metódusok, a kívánt tulajdonságok és a felhőből az eszközre irányuló üzenetek az alábbi oktatóanyagok:

* [Közvetlen metódusok használata][lnk-methods-tutorial], a közvetlen metódusok;
* [Eszközök konfigurálása a kívánt tulajdonságok használatával][lnk-twin-properties], ikereszköz kívánt tulajdonságait; 
* [Üzenetküldés a felhőből az eszközre][lnk-c2d-tutorial], a felhőből az eszközre.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
