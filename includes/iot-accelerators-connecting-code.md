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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450217"
---
### <a name="code-walkthrough"></a>Kód bemutatása

Ez a szakasz néhány, a legfontosabb elemei a mintakódot, és azt ismerteti, hogyan kapcsolódnak a távoli figyelési megoldásgyorsító.

A következő kódrészlet azt mutatja be, hogyan határozta meg a jelentett tulajdonságokat, amelyek az eszköz képességeit ismertetik. Ezek a tulajdonságok a következők:

- A megoldásgyorsító az eszköz hozzáadása a térkép engedélyezése az eszköz helyét.
- A jelenlegi belső vezérlőprogram verziója.
- Az eszköz támogatja a módszerek listája.
- Az eszköz által küldött telemetriai üzeneteket sémája.



A minta tartalmaz egy **serializeToJson** -függvény, amely az adatszerkezetet a Parson könyvtár használatával szerializálja.

A minta több visszahívási függvényekben, amelyek kinyomtatják a konzol információkat, az ügyfél kommunikál a megoldásgyorsító tartalmazza:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

A következő kódrészlet azt mutatja be a **device_method_callback** függvény. Ez a függvény meghatározza, hogy a megoldásgyorsító a metódushívásokat fogadásakor végrehajtandó műveletet. A függvény egy hivatkozást kap a **hűtő** adatokat a struktúra a **userContextCallback** paraméter. Értékét **userContextCallback** van beállítva, ha a visszahívási függvény konfigurálva van a **fő** függvény:



Amikor a megoldásgyorsító meghívja a belső vezérlőprogram frissítési metódusát, a minta a JSON-adattartalom deserializes, és elindítja egy háttérszálból történik, a frissítés befejezéséhez. A következő kódrészlet azt mutatja be a **do_firmware_update** , amely a szál fut:



A következő kódrészlet azt mutatja be, hogyan az ügyfél a megoldásgyorsító telemetriai üzenetet küld. Az üzenettulajdonságokban szerepel az üzenet séma, a telemetriai adatok megjelenítése az irányítópulton megoldásgyorsító segítségével:



A **fő** funkciót a minta:

- Inicializálja, és az SDK-alrendszer leáll.
- Inicializálja a **hűtő** adatok szerkezetét.
- A megoldásgyorsító elküldi a jelentett tulajdonságokat.
- Az eszköz metódus visszahívást függvény konfigurálása.
- A szimulált telemetriai értékeket a megoldásgyorsító küld.


