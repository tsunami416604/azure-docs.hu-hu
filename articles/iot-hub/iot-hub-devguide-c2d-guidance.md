---
title: Az Azure IoT Hub felhő eszköz beállítások |} Microsoft Docs
description: Fejlesztői útmutató - közvetlen módszerek, eszköz iker kívánt tulajdonságokkal vagy felhő-eszközre küldött üzenetek használata a felhőből eszközre kommunikációhoz útmutatást.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: ff81be4bbf6d297c623c5d98b5dc22a540112fcc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634437"
---
# <a name="cloud-to-device-communications-guidance"></a>Útmutatás felhő eszközre kommunikáció
IoT Hub eszköz alkalmazások teszi közzé a funkciót látja el egy háttér-alkalmazás három lehetőséget kínál:

* [Közvetlen módszerek] [ lnk-methods] kommunikációhoz, az eredmény azonnali jóváhagyás szükséges. Közvetlen módszereket gyakran használják az eszközök, például egy ventilátor bekapcsolása az interaktív vezérléshez.
* [Kettős a szükséges tulajdonságok] [ lnk-twins] a hosszan futó parancsokat kell helyezni az eszközt be egy bizonyos szánt szükséges állapotát. Például állítsa be a telemetriai adatok küldése intervallumát és 30 perc.
* [Felhő-eszközre küldött üzenetek] [ lnk-c2d] egyirányú értesítéseket csak az eszköz alkalmazás.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a felhő-és eszköz közötti kommunikációt különböző lehetőségek részletes összehasonlítása.

|  | Közvetlen metódusok | A két a kívánt tulajdonságai | Felhő-eszközre küldött üzenetek |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Azonnali megerősítését, például egy ventilátor bekapcsolásával igénylő parancsok. | Hosszan futó parancsokat célja, hogy az eszköz bizonyos kívánt állapotba helyezni. Például állítsa be a telemetriai adatok küldése intervallumát és 30 perc. | Az eszköz alkalmazás egyirányú értesítések. |
| Adatfolyam | Kétirányú. Az eszköz alkalmazás azonnal válaszolhat a metódust. A megoldás háttérrendszeréhez kimenetel összefüggéseikben való a kérelmet kap. | Egyirányú. Az eszköz alkalmazásának a tulajdonság módosítását értesítést kap. | Egyirányú. Az eszköz alkalmazás fogadja az üzenetet
| Tartósság | Kapcsolat nélküli eszközök nem próbál kapcsolódni. A megoldás háttérrendszeréhez értesítést kap, hogy az eszköz nincs csatlakoztatva. | Tulajdonságok értékeit az eszköz iker a megmaradnak. Eszköz a következő újrakapcsolódási olvasni. A tulajdonságértékek rendelkező lekérhető a [IoT-központ lekérdezési nyelv][lnk-query]. | Üzenetek akár 48 órát az IoT-központ is megőrzi. |
| Célok | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok][lnk-jobs]. | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok][lnk-jobs]. | Egyetlen eszköz által **deviceId**. |
| Méret | Maximális közvetlen módszer hasznos mérete 128 KB. | A keresett maximális tulajdonságok mérete 8 KB-os. | Legfeljebb 64 KB-os üzeneteket. |
| Gyakoriság | Magas. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. | Közepes. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. | Alacsony. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. |
| Protokoll | MQTT vagy AMQP akkor érhető el. | MQTT vagy AMQP akkor érhető el. | Minden protokoll érhető el. Eszköz le kell kérdeznie, amikor HTTPS-kapcsolaton keresztül. |

Megtudhatja, hogyan használható az alábbi oktatóanyagok közvetlen módszereket, a kívánt tulajdonságokat és a felhő-eszközre küldött üzenetek:

* [Közvetlen módszerekkel][lnk-methods-tutorial], közvetlen módszerek;
* [Eszközök konfigurálása kívánt tulajdonságokat használni][lnk-twin-properties], az eszköz iker a szükséges tulajdonságok; 
* [Felhő-eszközre küldött üzenetek küldése][lnk-c2d-tutorial], a felhő-eszközre küldött üzenetek.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
