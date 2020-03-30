---
title: Gyakorlati tanácsok a Time Series ID kiválasztásához – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg az azure-sorozati adatok előzetes verziójában a Time Series ID kiválasztásakor ajánlott eljárásokat.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083530"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Gyakorlati tanácsok az idősorozat-azonosító kiválasztásához

Ez a cikk összefoglalja a Time Series-azonosító fontosságát az Azure Time Series Insights előzetes verziójában, és ajánlott eljárásokat kell választani.

## <a name="choose-a-time-series-id"></a>Time Series-azonosító kiválasztása

A megfelelő idősorozat-azonosító kiválasztása kritikus fontosságú. A Time Series ID kiválasztása olyan, mint egy partíciókulcs kiválasztása az adatbázishoz. Ez akkor szükséges, ha létrehoz egy Time Series Insights előzetes környezetben. 

> [!IMPORTANT]
> Az idősorozat-azonosítók a következők:
> * A *kis- és nagybetűket megkülönböztető* tulajdonság: a betű- és karakterházak a keresések, az összehasonlítások, a frissítések és a particionálás során használatosak.
> * Nem *módosítható* tulajdonság: a létrehozás után nem módosítható.

> [!TIP]
> Ha az eseményforrás Egy IoT hub, a Time Series ID valószínűleg ***iothub-connection-device-id***lesz.

A követendő legfontosabb gyakorlati tanácsok a következők:

* Válasszon egy partíciókulcsot sok különböző értékkel (például több száz vagy ezer). Sok esetben ez lehet az eszközazonosító, érzékelőazonosító vagy címkeazonosító a JSON-ban.
* Az idősorozat-azonosítónak egyedinek kell lennie a [Time Series modell](./time-series-insights-update-tsm.md)levélcsomópont-szintjén.
* A Time Series ID tulajdonságnév-karakterláncának karakterkorlátja 128. Az idősorozat-azonosító tulajdonságértéke esetén a karakterkorlát 1024.
* Ha hiányzik az idősorozat-azonosító egyedi tulajdonságértéke, a rendszer null értékként kezeli, és az egyediségi megkötés ugyanazon szabályát követi.
* Idősorozat-azonosítóként legfeljebb *három* kulcsfontosságú tulajdonságot is kijelölhet. Ezek kombinációja lesz egy összetett kulcs, amely képviseli a Time Series ID.  
  > [!NOTE]
  > A három fő tulajdonságnak karakterláncnak kell lennie.
  > Ezt az összetett kulcsot egyszerre csak egy tulajdonság helyett kell lekérdeznie.

## <a name="select-more-than-one-key-property"></a>Több kulcstulajdonság kijelölése

A következő esetekben több kulcstulajdonságot jelöl ki idősorozat-azonosítóként.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>1. példa: Idősorozat-azonosító egyedi kulccsal

* Vannak örökölt eszközflottái. Mindegyiknek egyedi kulcsa van.
* Egy flottát egyedileg azonosít az **ingatlan-azonosító**. Egy másik flotta esetében az egyedi tulajdonság **objektumazonosító.** Egyik flotta sem tartalmazza a másik flotta egyedülálló tulajdonát. Ebben a példában két kulcsot kell kijelölnie, **az deviceId** és **az objectId azonosítót**egyedi kulcsként.
* Elfogadjuk a null értékeket, és a tulajdonság jelenlétének hiánya az esemény hasznos adatában null értéknek számít. Ez a megfelelő módja annak, hogy két eseményforrásnak küldjön adatokat, ahol az egyes eseményforrások ban lévő adatok egyedi Idősorozat-azonosítóval rendelkeznek.

### <a name="example-2-time-series-id-with-a-composite-key"></a>2. példa: Idősorozat-azonosító összetett kulccsal

* Több tulajdonságra van szükség ahhoz, hogy ugyanazon az eszközparkon belül egyediek legyenek. 
* Ön okos épületek et gyárt, és érzékelőket telepít minden helyiségben. Minden szobában, akkor általában ugyanazokat az értékeket **sensorId**. Ilyenpéldául **az érzékelő1**, **az érzékelő2**és **az érzékelő3**.
* Az épület egymást átfedő padló- és szobaszámokkal rendelkezik az ingatlan **flrRm**területén. Ezek a számok olyan értékeket tartalmaznak , mint az **1a**, **2b**és **3a**.
* Van egy ingatlan, **hely**, amely olyan értékeket tartalmaz, mint **Redmond,** **Barcelona**és **Tokió**. Egyediség létrehozásához a következő három tulajdonságot kell kijelölnie a Time Series ID kulcsaként: **sensorId**, **flrRm**és **location**.

Példa nyers eseményre:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Az Azure Portalon az alábbi módon adhatja meg az összetett kulcsot: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>További lépések

* További információ az [adatmodellezésről.](./time-series-insights-update-tsm.md)

* Tervezze meg [az Azure Time Series Insights előzetes verzióját.](./time-series-insights-update-plan.md)