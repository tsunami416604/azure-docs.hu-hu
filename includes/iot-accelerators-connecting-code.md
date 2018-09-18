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
ms.openlocfilehash: d087a3d5746396d81ef4ea44d90e917f25ebf19d
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45739181"
---
### <a name="code-walkthrough"></a>Kód bemutatása

Ez a szakasz néhány, a legfontosabb elemei a mintakódot, és azt ismerteti, hogyan kapcsolódnak a távoli figyelési megoldásgyorsító.

A következő kódrészlet azt mutatja be, hogyan határozta meg a jelentett tulajdonságokat, amelyek az eszköz képességeit ismertetik. Ezek a tulajdonságok a következők:

- A megoldásgyorsító az eszköz hozzáadása a térkép engedélyezése az eszköz helyét.
- A jelenlegi belső vezérlőprogram verziója.
- Az eszköz támogatja a módszerek listája.
- Az eszköz által küldött telemetriai üzeneteket sémája.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

A minta tartalmaz egy **serializeToJson** -függvény, amely az adatszerkezetet a Parson könyvtár használatával szerializálja.

A minta több visszahívási függvényekben, amelyek kinyomtatják a konzol információkat, az ügyfél kommunikál a megoldásgyorsító tartalmazza:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

A következő kódrészlet azt mutatja be a **device_method_callback** függvény. Ez a függvény meghatározza, hogy a megoldásgyorsító a metódushívásokat fogadásakor végrehajtandó műveletet. A függvény egy hivatkozást kap a **hűtő** adatokat a struktúra a **userContextCallback** paraméter. Értékét **userContextCallback** van beállítva, ha a visszahívási függvény konfigurálva van a **fő** függvény:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Amikor a megoldásgyorsító meghívja a belső vezérlőprogram frissítési metódusát, a minta a JSON-adattartalom deserializes, és elindítja egy háttérszálból történik, a frissítés befejezéséhez. A következő kódrészlet azt mutatja be a **do_firmware_update** , amely a szál fut:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

A következő kódrészlet azt mutatja be, hogyan az ügyfél a megoldásgyorsító telemetriai üzenetet küld. Az üzenettulajdonságokban szerepel az üzenet séma, a telemetriai adatok megjelenítése az irányítópulton megoldásgyorsító segítségével:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=sendmessage "Send telemetry")]

A **fő** funkciót a minta:

- Inicializálja, és az SDK-alrendszer leáll.
- Inicializálja a **hűtő** adatok szerkezetét.
- A megoldásgyorsító elküldi a jelentett tulajdonságokat.
- Az eszköz metódus visszahívást függvény konfigurálása.
- A szimulált telemetriai értékeket a megoldásgyorsító küld.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=main "Main")]
