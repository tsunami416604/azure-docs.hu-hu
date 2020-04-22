---
title: Az Azure IoT Hub felhőből az eszközökre beállítási lehetőségei | Microsoft dokumentumok
description: Fejlesztői útmutató – útmutatás a közvetlen módszerek, az ikereszköz kívánt tulajdonságaivagy a felhőből az eszközre irányuló üzenetek használatához a felhőből az eszközre történő kommunikációhoz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b5682334bd3fb23fbbebed5fc8ece6d55e9c5652
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733228"
---
# <a name="cloud-to-device-communications-guidance"></a>Felhőből az eszközre irányuló kommunikációra vonatkozó útmutatás

Az IoT Hub három lehetőséget kínál az eszközalkalmazások számára, hogy a funkciókat elérhetővé tegyezzenek egy háttéralkalmazás számára:

* [Közvetlen kommunikációs módszerek,](iot-hub-devguide-direct-methods.md) amelyek az eredmény azonnali megerősítését igénylik. A közvetlen módszereket gyakran használják az eszközök interaktív vezérlésére, például egy ventilátor bekapcsolásához.

* [Twin kívánt tulajdonságait](iot-hub-devguide-device-twins.md) a hosszú ideig futó parancsok célja, hogy az eszköz egy bizonyos kívánt állapotban. Állítsa be például a telemetriai adatok küldési időközét 30 percre.

* [Felhőből az eszközre irányuló üzenetek](iot-hub-devguide-messages-c2d.md) az eszközalkalmazás egyirányú értesítéseihez.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Itt van egy részletes összehasonlítása a különböző felhő-készülék kommunikációs lehetőségeket.

|  | Közvetlen metódusok | Iker kívánt tulajdonságai | Felhőből eszközre irányuló üzenetek |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Azonnali megerősítést igénylő parancsok, például ventilátor bekapcsolása. | Hosszú ideig futó parancsok célja, hogy az eszköz egy bizonyos kívánt állapotban. Állítsa be például a telemetriai adatok küldési időközét 30 percre. | Egyirányú értesítések az eszközalkalmazáshoz. |
| Adatfolyam | Kétirányú. Az eszközalkalmazás azonnal válaszolaa módszerre. A megoldás háttér-vég ének a kérelemhez az eredményt kapja. | Egyirányú. Az eszközalkalmazás értesítést kap a tulajdonság változásával kapcsolatban. | Egyirányú. Az eszközalkalmazás fogadja az üzenetet
| Tartósság | A kapcsolat bontása nem történt meg. A megoldás háttértartaléka értesítést kap arról, hogy az eszköz nincs csatlakoztatva. | A tulajdonságértékek megmaradnak az ikereszközben. Az eszköz a következő újracsatlakozáskor leolvassa. A tulajdonságértékek az [IoT Hub lekérdezési nyelvével visszakereshetőek.](iot-hub-devguide-query-language.md) | Az Üzenetek et az IoT Hub legfeljebb 48 órán keresztül őrizheti meg. |
| Célokat | Egyetlen eszköz eszköz segítségével **deviceId**, vagy több eszköz segítségével [feladatok](iot-hub-devguide-jobs.md). | Egyetlen eszköz eszköz segítségével **deviceId**, vagy több eszköz segítségével [feladatok](iot-hub-devguide-jobs.md). | Egyetlen eszköz **deviceId**. |
| Méret | A közvetlen metódus maximális hasznos adatmérete 128 KB. | A kívánt tulajdonságok maximális mérete 32 KB. | Akár 64 KB-os üzenet. |
| Frequency | Magas. További információ: [IoT Hub-korlátozások.](iot-hub-devguide-quotas-throttling.md) | Közepes. További információ: [IoT Hub-korlátozások.](iot-hub-devguide-quotas-throttling.md) | Alacsony. További információ: [IoT Hub-korlátozások.](iot-hub-devguide-quotas-throttling.md) |
| Protocol (Protokoll) | MQTT vagy AMQP használatával érhető el. | MQTT vagy AMQP használatával érhető el. | Minden protokollon elérhető. Az eszköznek le kell szavazással rendelkeznie a HTTPS használatakor. |

A következő oktatóanyagokban megtudhatja, hogyan használhatja a közvetlen módszereket, a kívánt tulajdonságokat és a felhőből az eszközre irányuló üzeneteket:

* [Közvetlen metódusok használata](quickstart-control-device-node.md)
* [Eszközök konfigurálása a kívánt tulajdonságokkal](tutorial-device-twins.md) 
* [Üzenetküldés a felhőből az eszközökre](iot-hub-node-node-c2d.md)
