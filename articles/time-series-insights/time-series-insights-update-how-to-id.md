---
title: Ajánlott eljárások a Time Series ID döntöttek az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Ajánlott eljárások ismertetése, ha úgy dönt, a Time Series-azonosító az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 44c6c8a55bbcbf76cf48fd17a6e52d188a5e99fc
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556611"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>A Time Series ID kiválasztására vonatkozó ajánlott eljárások

Ez a cikk ismerteti az Azure Time Series Insights – előzetes partíciókulcsot, a Time Series-azonosító és a egy kiválasztására vonatkozó ajánlott eljárásokat.

## <a name="choose-a-time-series-id"></a>Time Series-azonosító kiválasztása

A Time Series ID kiválasztása hasonlóan olyan partíciókulcsot, egy adatbázis van. Egy fontos döntés, amely tervezési időben kell tenni. Nem lehet frissíteni egy meglévő Time Series Insights előzetes környezet használatára egy különböző Time Series azonosítóját. Más szóval egy Time Series azonosítójú környezet létrehozását követően a házirend a nem módosítható tulajdonságot, amely nem módosítható.

> [!IMPORTANT]
> A Time Series-azonosító, kis-és nagybetűket, és nem módosítható (nem módosítható beállítása után).

Az adott szem előtt válassza a megfelelő Time Series-azonosító, kritikus fontosságú. A Time Series ID kiválasztásakor vegye figyelembe az ajánlott eljárások a következő:
* Válassza ki a tulajdonság nevét, a széles körű értékeket, és még az adathozzáférési mintáknak megfelelően. Ajánlott eljárás a partíciókulcs (például több száz vagy ezer) számos különböző értékekkel rendelkeznek. Sok felhasználónál ez lesz hiba például az eszközazonosító vagy SensorID a JSON-fájlban.
* A Time Series azonosító egyedinek kell lennie a levél csomópont szintjén a [Idősorozat-modell](./time-series-insights-update-tsm.md).
* Egy sztring Time Series azonosító tulajdonság neve legfeljebb 128 karakter lehet, és a Time Series azonosító tulajdonságérték lehet, legfeljebb 1024 karakter hosszú lehet.
* Ha bizonyos egyedi értékeket a Time Series azonosító tulajdonság hiányzik, akkor null az értéke, amely részt vesz az egyediségre vonatkozó feltételnek kell kezelni.

Továbbá kiválaszthat akár *három* (3) kulcstulajdonságok, mint a Time Series azonosítóját.

  > [!NOTE]
  > A *három* (3) kulcstulajdonságok karakterláncoknak kell lenniük.

A következő esetekben ismertetik a Time Series azonosító egynél több kulcstulajdonság kiválasztása:  

### <a name="scenario-1"></a>1. forgatókönyv

* Örökölt flották az eszközök, minden egyes egyedi kulccsal rendelkezik. 
* Ha például egy flotta egyedileg azonosítja a tulajdonság *deviceId* és a egy másik, az egyedi tulajdonság *objectId*. Sem a flotta a flotta egyedi tulajdonság tartalmazza. Ebben a példában a kiválasztott két kulcsot, az eszközazonosító és objectId, egyedi kulcsként. 
* Fogadunk null értékeket, és a egy tulajdonság jelenlétét az esemény hasznos adatainak hiánya számít egy `null` értéket. Ez egyben a megfelelő módon történő kezeléséhez, ahol minden egyes esemény adatforrásban lévő rendelkezik egy egyedi Time Series-azonosító. két különböző eseményforrások történő adatküldés

### <a name="scenario-2"></a>2. forgatókönyv

* Az eszközök a azonos flotta belül egyedinek kell lennie több tulajdonságra van szüksége. 
* Például tegyük meg egy intelligens épület gyártó és minden szobában érzékelő telepítése. Minden egyes szobában, általában rendelkezik ugyanazokat az értékeket a *sensorId*, mint például *sensor1*, *sensor2*, és *sensor3*.
* Ezenkívül az épület emelet és a hely számok átfedésben a tulajdonság a helyek között van-e *flrRm*, amely rendelkezik értékek például *1a*, *2b*, *3a* , és így tovább.
* Végül ott a tulajdonság, *hely*, például tartalmazó értékek *Redmond*, *Barcelona*, és *Tokió*. Egyedi-e létrehozni, az alábbi három tulajdonságot szeretne megjelölni a Time Series azonosító kulcsokat: *sensorId*, *flrRm*, és *hely*.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [adatmodellezés](./time-series-insights-update-tsm.md).
* Tervezi a [Azure Time Series Insights (előzetes verzió) környezet](./time-series-insights-update-plan.md).
