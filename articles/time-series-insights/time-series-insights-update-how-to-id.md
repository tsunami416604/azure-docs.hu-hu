---
title: Ajánlott eljárások a Time Series ID kiválasztásakor az Azure Time Series Insightsban |} A Microsoft Docs
description: Ajánlott eljárások ismertetése a Time Series ID kiválasztásakor az Azure Time Series Insightsban
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: a7fd1ff4a0fe96724af0c43019afe53666ab81d7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856044"
---
# <a name="best-practices-when-choosing-a-time-series-id"></a>Ajánlott eljárások a Time Series ID kiválasztásakor

Ez a dokumentum ismerteti az Azure Time Series Insights (előzetes verzió) partíciókulccsal a **Time Series azonosító**, és gyakorlati tanácsok a válasszon egyet.

## <a name="choose-a-time-series-id"></a>Válassza ki a Time Series-azonosító

A kiválasztott a **Time Series azonosító** úgy működik, mint a partíciókulcs-adatbázis kiválasztása. Éppen ezért egy fontos döntés, amely tervezési időben kell tenni. Nem lehet frissíteni egy meglévő TSI (előzetes verzió) környezetet egy másik **Time Series azonosító**. Más szóval a környezet létrehozása után egy **Time Series azonosító**, a szabályzat nem módosítható, mivel ez egy nem módosítható tulajdonság.

> [!IMPORTANT]
> A **Time Series azonosító** kis-és nagybetűket, és nem módosítható (ez nem módosítható beállítása után).

Az adott szem előtt, válassza a megfelelő **Time Series azonosító** kritikus fontosságú. Vegye figyelembe a következő tulajdonságok kiválasztásakor egy **Time Series azonosító**:

> [!div class="checklist"]
> * Válassza ki a tulajdonság nevét, a széles körű értékeket, és még az adathozzáférési mintáknak megfelelően. Ajánlott eljárás a partíciókulcs (például több száz vagy ezer) számos különböző értékekkel rendelkeznek. Sok felhasználónál ez lesz hasonló **Eszközazonosító**, **érzékelő azonosító**stb.  
> * A levél csomópont szintjén egyedinek kell lennie a [Idősorozat-modell](./time-series-insights-update-tsm.md).
> * A **Time Series azonosító** tulajdonság nevét a karakterlánc legfeljebb 128 karakterből állhat, és **Time Series azonosító** tulajdonságértékek legfeljebb 1024 karakter lehet.
> * Ha bizonyos egyedi **Time Series azonosító** tulajdonságértékek némelyike hiányzik, akkor számít null az értéke, amely részt vesz az egyediségre vonatkozó feltételnek.

Továbbá kiválaszthat akár **három** , (3) kulcstulajdonságok a **Time Series azonosító**.

  > [!NOTE]
  > A **három** (3) kulcstulajdonságok karakterláncoknak kell lenniük.

Az alábbi forgatókönyvek szerint végez, egynél több kulcstulajdonság kiválasztása a **Time Series azonosító**:  

* 1. forgatókönyv:

  * Az eszközök, amelyek mindegyikére egyedi kulcsok örökölt flották rendelkezik. 
  * Ha például egy flotta egyedileg azonosítja a tulajdonság **deviceId** és a egy másik, az egyedi tulajdonság **objectId**.  A mindkét flották a flotta egyedi tulajdonság nem található. Ebben a példában két kulcs, a kiválasztott **deviceId**, és **objectId** egyedi kulcsként. 
  * Fogadunk null értékeket, és a egy tulajdonság jelenlétét az esemény hasznos adatainak hiánya számít be, egy `null` értéket.  Ez akkor is lehet kezelni, ahol minden egyes esemény adatforrásban lévő kellett egy másik két különböző eseményforrások történő adatküldés a megfelelő módon egyedi **Time Series azonosító**.

* 2. forgatókönyv:

  * Egyedi-e az eszközök a azonos flotta megjelenítése több tulajdonságra van szüksége. Például tegyük fel, Ön egy intelligens épület gyártó és minden szobában érzékelő telepítése. Minden egyes szobában, általában rendelkezik ugyanazokat az értékeket a **sensorId**, többek között **sensor1**, **sensor2**, **sensor3**, és így tovább.
  * Ezenkívül egymást átfedő emelet és a hely különböző helyeken a tulajdonságban **flrRm** például értékeket tartalmazó `1a`, `2b`, `3a`, és így tovább.
  * Végül ott a tulajdonság, **hely** például értékeket tartalmazó `Redmond`, `Barcelona`, `Tokyo`, és így tovább. Egyedi-e létrehozni, ezeket a tulajdonságokat, mind a három, kijelölheti a **Time Series azonosító** kulcsok – **sensorId**, **flrRm**, és **hely**.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure Time Series Insights (előzetes verzió) Time Series modellek](./time-series-insights-update-tsm.md).

Tervezi a [Azure Time Series Insights (előzetes verzió) környezet](./time-series-insights-update-plan.md).