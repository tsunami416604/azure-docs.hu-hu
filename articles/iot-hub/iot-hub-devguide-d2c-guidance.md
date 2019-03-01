---
title: Az Azure IoT Hub eszköz – felhő lehetőségei |} A Microsoft Docs
description: Fejlesztői útmutató – mikor érdemes használni az eszköz – felhő üzeneteket, jelentett tulajdonságok vagy a fájl feltöltése a felhőből az eszközre irányuló kommunikáció útmutatóját.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57007981"
---
# <a name="device-to-cloud-communications-guidance"></a>Eszköz a felhőbe irányuló kommunikáció útmutatóját

Amikor adatokat küld a megoldás háttérrendszere az eszközalkalmazástól érkező vége, az IoT Hub három beállítást tesz elérhetővé:

* [Eszköz – felhő üzeneteket](iot-hub-devguide-messages-d2c.md) a time series telemetriai adatokhoz és riasztásokhoz.

* [Ikereszköz által jelentett tulajdonságokként](iot-hub-devguide-device-twins.md) az eszközök állapotinformációit, például a rendelkezésre álló lehetőségeket, a feltételek vagy a hosszan futó munkafolyamatok állapotát. Például a konfigurációs és szoftverfrissítések.

* [A fájl feltöltése](iot-hub-devguide-file-upload.md) media fájlok és -kötegek nagy telemetriai csak időszakosan kapcsolódó eszközök által feltöltött vagy tömöríti, sávszélességet takaríthat.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Itt látható a különböző eszköz-felhő kommunikációs lehetőségek részletes összehasonlítását láthatja.

|  | Az eszközről a felhőbe irányuló üzenetek | Ikereszköz a jelentett tulajdonságok | Fájlfeltöltések |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Telemetria a time series és riasztások. Például a 256 KB-os érzékelő adatkötegek küldi át 5 percenként. | Elérhető képességek és a feltételeknek. Például a jelenlegi eszköz csatlakozási mód például mobilhálózati vagy Wi-Fi. Hosszan futó munkafolyamatok, például a konfigurációs és a szoftverfrissítések szinkronizálását. | Médiafájlok. Nagy (általában tömörített) telemetriai kötegek. |
| Tárolásához és lekéréséhez | Ideiglenesen tárol az IoT Hub, legfeljebb 7 napra. Csak a Szekvenciális olvasási. | Az IoT Hub az ikereszköz tárolja. Lekérhető használatával a [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md). | Felhasználó által megadott Azure Storage-fiók tárolva. |
| Méret | Legfeljebb 256 KB-os üzenetet. | Maximális jelentett tulajdonságok 8 KB-os mérete. | Az Azure Blob Storage által támogatott maximális fájlméretet. |
| Gyakoriság | Magas. További információkért lásd: [korlátozza az IoT Hub](iot-hub-devguide-quotas-throttling.md). | Közepes. További információkért lásd: [korlátozza az IoT Hub](iot-hub-devguide-quotas-throttling.md). | Alacsony. További információkért lásd: [korlátozza az IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Elérhető az összes protokollon. | Mqtt-ről vagy AMQP használatával akkor érhető el. | Használata esetén érhető el minden olyan protokoll, de HTTPS van szüksége az eszközön. |

Egy alkalmazás információt mind a telemetriát a time series vagy riasztást küld, és lehetővé teszi az ikereszköz szükség lehet. Ebben a forgatókönyvben az alábbi lehetőségek közül választhat:

* Az eszközalkalmazás eszköz a felhőbe irányuló üzenetet küld, és a jelentések tulajdonság módosítása.
* A megoldás háttérrendszere az adatokat tárolhatja az ikereszközök címkék, amikor megkapja az üzenetet.

Mivel az eszköz a felhőbe irányuló üzeneteket egy nagyobb átviteli sebességre, mint az ikereszköz-frissítések engedélyezéséhez kívánatos néha frissítése az ikereszköz minden eszközt a felhőbe irányuló üzenetek elkerülése érdekében.