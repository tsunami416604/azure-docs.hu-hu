---
title: "Az Azure IoT Hub eszközről a felhőbe beállítások |} Microsoft Docs"
description: "Fejlesztői útmutató - eszköz a felhőbe küldött üzeneteket, jelentett tulajdonságok vagy a fájl feltöltése a felhőből eszközre kommunikációhoz használati útmutatást."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: a9a062ebb8d6e3b37d917064209eda618d0dd308
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="device-to-cloud-communications-guidance"></a>Eszköz-felhő kommunikációs útmutató
Amikor adatokat küld az eszköz alkalmazásból a megoldás háttérrendszere vége, az IoT-központ három beállítást tesz elérhetővé:

* [Eszköz a felhőbe küldött üzeneteket] [ lnk-d2c] idő adatsorozat telemetriai adatok és riasztások.
* [Eszköz iker által jelentett tulajdonságok] [ lnk-twins] jelentéskészítési eszköz állapotadatokat például elérhető képességek, a feltételek vagy a hosszan futó munkafolyamatok állapotát. Például a konfigurációs és szoftverfrissítéseket.
* [A fájl feltöltések] [ lnk-fileupload] adathordozó nagy telemetriai kötegek időnként csatlakoztatott eszközök által feltöltött, illetve fájlok és sávszélességet tömörítve.

Ez a különböző eszköz-felhő kommunikációs lehetőségek részletes összehasonlítása.

|  | Az eszközről a felhőbe irányuló üzenetek | Eszköz iker jelentett tulajdonságai | Fájlfeltöltések |
| ---- | ------- | ---------- | ---- |
| Forgatókönyv | Telemetria idősorok és riasztásokat. Például a 256 KB-os érzékelő adatkötegek 5 percenként lett küldve. | Elérhető képességek és a kikötéseket. Például az aktuális eszköz csatlakozási mód például mobilhálózati vagy WiFi. Hosszan futó munkafolyamatok, például a konfigurációs és a szoftverfrissítések szinkronizálása. | Médiafájlok. Nagy (általában tömörített) telemetriai kötegek. |
| Tárolása és lekérése | Ideiglenesen tárolja az IoT-központ, legfeljebb 7 napra. Csak a Szekvenciális olvasási. | Az eszköz iker IoT-központ által tárolja. Lekérhető használatával a [IoT-központ lekérdezési nyelv][lnk-query]. | Felhasználó által megadott Azure Storage-fiókban tárolt. |
| Méret | Legfeljebb 256 KB-os üzeneteket. | Jelentett tulajdonságok maximális mérete 8 KB-os. | Azure Blob Storage által támogatott maximális fájlméretet. |
| Gyakoriság | Magas. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. | Közepes. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. | Alacsony. További információkért lásd: [korlátozza az IoT-központ][lnk-quotas]. |
| Protokoll | Minden protokoll érhető el. | MQTT vagy AMQP akkor érhető el. | Használata esetén elérhető bármely protokoll, de HTTPS PROTOKOLLT igényel az eszközön. |

Az alkalmazás esetleg információkat is telemetriai adatokat a time series vagy riasztást küldjön, és lehetővé teszi az eszköz iker érhető el. Ebben a forgatókönyvben a következő lehetőségek közül választhat:

* Az eszköz alkalmazás eszközről a felhőbe üzenetet, és jelentéseket tulajdonság módosítását.
* A megoldás háttérrendszeréhez az adatok tárolhatók a két eszköz címkék, az üzenet fogadásakor.

Mivel az eszköz a felhőbe küldött üzeneteket egy sokkal nagyobb átviteli sebességgel mint eszköz iker frissítések engedélyezéséhez kívánatos néha frissítése az eszköz a két minden eszköz-felhő üzenet elkerülése érdekében.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
