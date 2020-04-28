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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67179554"
---
### <a name="code-walkthrough"></a>Kódbemutató

Ez a szakasz a mintakód néhány kulcsfontosságú részét ismerteti, és bemutatja, hogyan kapcsolódnak a távoli figyelési megoldáshoz.

A következő kódrészlet azt mutatja be, hogy az eszköz képességeit leíró jelentett tulajdonságok hogyan vannak meghatározva. Ezek a tulajdonságok a következők:

- Annak az eszköznek a helye, amely lehetővé teszi a megoldás-gyorsító számára az eszköz hozzáadását a térképhez.
- A jelenlegi belső vezérlőprogram verziója.
- Az eszköz által támogatott metódusok listája.
- Az eszköz által küldött telemetria-üzenetek sémája.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

A minta tartalmaz egy **serializeToJson** függvényt, amely az adatszerkezetet a plébános-könyvtár használatával szerializálja.

A minta több visszahívási funkciót is tartalmaz, amelyek adatokat nyomtatnak a konzolra, mivel az ügyfél a megoldás-gyorssegédtel kommunikál:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

A következő kódrészlet a **device_method_callback** függvényt mutatja be. Ez a függvény határozza meg azt a műveletet, amelyet akkor kell végrehajtani, ha a megoldás-gyorsító egy metódus hívása érkezik. A függvény a **userContextCallback** paraméterben a **Chiller** adatstruktúrára mutató hivatkozást fogad. A **userContextCallback** értéke akkor van beállítva, ha a visszahívási függvény a **fő** függvényben van konfigurálva:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Amikor a megoldás-gyorsító meghívja a belső vezérlőprogram frissítési módszerét, a minta deszerializálja a JSON-adattartalmat, és elindít egy háttérbeli szálat a frissítési folyamat befejezéséhez. A következő kódrészlet a szálon futó **do_firmware_update** mutatja be:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

A következő kódrészlet azt mutatja be, hogy az ügyfél hogyan küld egy telemetria üzenetet a megoldás-gyorsító felé. Az üzenet tulajdonságai közé tartozik az üzenet sémája, amely segít a megoldás-gyorsító számára a telemetria megjelenítésében az irányítópulton:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

A minta **fő** funkciója:

- Az SDK alrendszer inicializálása és leállítása.
- A **hűtő** adatstruktúrájának inicializálása.
- Elküldi a jelentett tulajdonságokat a megoldás-gyorsító számára.
- Az eszköz metódusának visszahívási függvényének konfigurálása.
- Szimulált telemetria-értékeket küld a megoldás-gyorsító felé.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
