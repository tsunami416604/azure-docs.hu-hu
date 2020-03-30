---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179554"
---
### <a name="code-walkthrough"></a>Kódbemutató

Ez a szakasz a mintakód néhány kulcsfontosságú részét ismerteti, és bemutatja, hogyan kapcsolódnak a távfigyelési megoldás gyorsítóhoz.

A következő kódrészlet bemutatja, hogyan vannak definiálva az eszköz képességeit leíró jelentett tulajdonságok. Ezek a tulajdonságok a következők:

- Az eszköz helye, amely lehetővé teszi, hogy a megoldásgyorsító hozzáadja az eszközt a térképhez.
- A firmware jelenlegi verziója.
- Az eszköz által támogatott módszerek listája.
- Az eszköz által küldött telemetriai üzenetek sémája.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

A minta tartalmaz egy **szerializálásToJson** függvényt, amely szerializálja ezt az adatstruktúrát a Parson könyvtár használatával.

A minta számos visszahívási függvényt tartalmaz, amelyek információkat nyomtatnak a konzolra, miközben az ügyfél együttműködik a megoldásgyorsítóval:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

A következő kódrészlet a **device_method_callback** függvényt mutatja. Ez a függvény határozza meg, hogy a megoldásgyorsító metódushívásesetén milyen műveletet kell végrehajtani. A függvény hivatkozást kap a **chiller** adatstruktúrájára a **userContextCallback** paraméterben. A **userContextCallback** értéke akkor van beállítva, ha a visszahívási függvény a **fő** funkcióban van konfigurálva:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Amikor a megoldásgyorsító meghívja a belső vezérlőprogram frissítési módszerét, a minta deszerializálja a JSON-hasznos terhet, és egy háttérszálat indít el a frissítési folyamat befejezéséhez. A következő kódrészlet a szálon futó **do_firmware_update** mutatja:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

A következő kódrészlet bemutatja, hogy az ügyfél hogyan küld egy telemetriai üzenetet a megoldásgyorsítónak. Az üzenet tulajdonságai tartalmazzák az üzenetsémát, amely segít a megoldásgyorsítónak megjeleníteni a telemetriai adatokat az irányítópulton:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

A **minta fő** funkciója:

- Inicializálja és leállítja az SDK alrendszert.
- Inicializálja a **hűtő** adatszerkezetét.
- Elküldi a jelentett tulajdonságokat a megoldásgyorsítónak.
- Az eszközmetódus visszahívási funkciójának konfigurálása.
- Szimulált telemetriai értékeket küld a megoldásgyorsítónak.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
