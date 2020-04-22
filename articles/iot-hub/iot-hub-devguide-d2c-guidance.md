---
title: Az Azure IoT Hub eszközről felhőbe – beállításai | Microsoft dokumentumok
description: Fejlesztői útmutató – útmutatás arról, hogy mikor kell használni az eszközről a felhőbe irányuló üzeneteket, a jelentett tulajdonságokat vagy a fájlok feltöltését a felhőből az eszközre irányuló kommunikációhoz.
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
ms.openlocfilehash: b984ee7ed662bf089e7bcb6fc7e948fb61ed1209
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733216"
---
# <a name="device-to-cloud-communications-guidance"></a>Az eszközök közötti kommunikációról szóló útmutatás

Amikor adatokat küld az eszközalkalmazásból a megoldás háttérvégére, az IoT Hub három lehetőséget kínál:

* [Eszközről a felhőbe irányuló üzenetek](iot-hub-devguide-messages-d2c.md) idősorozat-telemetriai adatokhoz és riasztásokhoz.

* [Az ikereszköz jelentett tulajdonságai](iot-hub-devguide-device-twins.md) az eszköz állapotadatainak jelentéséhez, például a rendelkezésre álló képességek, feltételek vagy a hosszú ideig futó munkafolyamatok állapota. Például konfigurációs és szoftverfrissítések.

* [Fájlfeltöltések](iot-hub-devguide-file-upload.md) médiafájlokhoz és nagy telemetriai kötegekhez, amelyeket időnként csatlakoztatott eszközök feltölteni vagy tömörítettek a sávszélesség megtakarítása érdekében.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Itt van egy részletes összehasonlítása a különböző eszköz-felhő kommunikációs lehetőségeket.

|  | Az eszközről a felhőbe irányuló üzenetek | Az ikereszköz jelentett tulajdonságai | Fájlfeltöltések |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Telemetriai idősorozatok és riasztások. Például 256 KB-os érzékelő adatkötegek küldött 5 percenként. | Elérhető képességek és feltételek. Például az aktuális eszköz kapcsolati mód, például a mobil- vagy Wi-Fi. Hosszú ideig futó munkafolyamatok, például konfigurációés szoftverfrissítések szinkronizálása. | Médiafájlok. Nagy (általában tömörített) telemetriai kötegek. |
| Tárolás és visszakeresés | Az IoT Hub ideiglenesen legfeljebb 7 napig tárolja. Csak szekvenciális leolvasás. | Az IoT Hub tárolja az ikereszközben. Az [IoT Hub lekérdezési nyelvével](iot-hub-devguide-query-language.md)visszakereshető. | A felhasználó által biztosított Azure Storage-fiókban tárolva. |
| Méret | Akár 256 KB-os üzenet. | A jelentett tulajdonságok maximális mérete 32 KB. | Az Azure Blob Storage által támogatott maximális fájlméret. |
| Frequency | Magas. További információ: [IoT Hub-korlátozások.](iot-hub-devguide-quotas-throttling.md) | Közepes. További információ: [IoT Hub-korlátozások.](iot-hub-devguide-quotas-throttling.md) | Alacsony. További információ: [IoT Hub-korlátozások.](iot-hub-devguide-quotas-throttling.md) |
| Protocol (Protokoll) | Minden protokollon elérhető. | MQTT vagy AMQP használatával érhető el. | Bármely protokoll használata esetén elérhető, de HTTPS szükséges az eszközön. |

Előfordulhat, hogy egy alkalmazásnak telemetriai idősorozatként vagy riasztásként kell információkat küldenie, és elérhetővé kell tennie az ikereszközben. Ebben az esetben az alábbi lehetőségek közül választhat:

* Az eszközalkalmazás eszközről felhőbe irányuló üzenetet küld, és tulajdonságváltozást jelent.
* A megoldás háttérrendszertárolja az információkat az eszköz iker címkéket, amikor megkapja az üzenetet.

Mivel az eszközről a felhőbe irányuló üzenetek sokkal nagyobb átviteli sebességű, mint az eszköz ikerfrissítések, néha kívánatos elkerülni az eszköz iker készülék frissítése minden eszköz ről felhőre üzenet.
