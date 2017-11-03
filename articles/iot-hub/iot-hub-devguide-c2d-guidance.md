---
title: "Az Azure IoT Hub felhő eszköz beállítások |} Microsoft Docs"
description: "Fejlesztői útmutató - közvetlen módszerek, eszköz iker kívánt tulajdonságokkal vagy felhő-eszközre küldött üzenetek használata a felhőből eszközre kommunikációhoz útmutatást."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: elioda
ms.openlocfilehash: c0f9d0e13cb159188bdaf2b915c1bf6de73be855
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-to-device-communications-guidance"></a>Útmutatás felhő eszközre kommunikáció
IoT Hub eszköz alkalmazások teszi közzé a funkciót látja el egy háttér-alkalmazás három lehetőséget kínál:

* [Közvetlen módszerek] [ lnk-methods] kommunikációhoz, az eredmény azonnali jóváhagyás szükséges. Közvetlen módszereket gyakran használják az eszközök, például egy ventilátor bekapcsolása az interaktív vezérléshez.
* [Kettős a szükséges tulajdonságok] [ lnk-twins] a hosszan futó parancsokat kell helyezni az eszközt be egy bizonyos szánt szükséges állapotát. Például állítsa be a telemetriai adatok küldése intervallumát és 30 perc.
* [Felhő-eszközre küldött üzenetek] [ lnk-c2d] egyirányú értesítéseket csak az eszköz alkalmazás.

Ez a felhő-és eszköz közötti kommunikációt különböző lehetőségek részletes összehasonlítása.

|  | Közvetlen metódusok | A két a kívánt tulajdonságai | Felhő-eszközre küldött üzenetek |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Azonnali megerősítését, például egy ventilátor bekapcsolásával igénylő parancsok. | Hosszan futó parancsokat célja, hogy az eszköz bizonyos kívánt állapotba helyezni. Például állítsa be a telemetriai adatok küldése intervallumát és 30 perc. | Az eszköz alkalmazás egyirányú értesítések. |
| Adatfolyam | Kétirányú. Az eszköz alkalmazás azonnal válaszolhat a metódust. A megoldás háttérrendszeréhez kimenetel összefüggéseikben való a kérelmet kap. | Egyirányú. Az eszköz alkalmazásának a tulajdonság módosítását értesítést kap. | Egyirányú. Az eszköz alkalmazás fogadja az üzenetet
| Tartósság | Kapcsolat nélküli eszközök nem próbál kapcsolódni. A megoldás háttérrendszeréhez értesítést kap, hogy az eszköz nincs csatlakoztatva. | Tulajdonságok értékeit az eszköz iker a megmaradnak. Eszköz a következő újrakapcsolódási olvasni. A tulajdonságértékek rendelkező lekérhető a [IoT-központ lekérdezési nyelv][lnk-query]. | Üzenetek akár 48 órát az IoT-központ is megőrzi. |
| Cél | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok][lnk-jobs]. | Egyetlen eszköz használatával **deviceId**, vagy több eszköz [feladatok][lnk-jobs]. | Egyetlen eszköz által **deviceId**. |
| Méret | Legfeljebb 8 KB-kérelmek és 8 KB-os válaszokat. | A keresett maximális tulajdonságok mérete 8 KB-os. | Legfeljebb 64 KB-os üzeneteket. |
| gyakoriság | Magas. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. | Közepes. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. | Alacsony. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. |
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
