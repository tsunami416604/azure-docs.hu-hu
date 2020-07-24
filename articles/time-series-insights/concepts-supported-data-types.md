---
title: Támogatott adattípusok – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerkedjen meg a támogatott adattípusokkal Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c2e70a4f5cdbbc7a5a408138c3ec832cc831cf33
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046577"
---
# <a name="supported-data-types"></a>Támogatott adattípusok

A következő táblázat felsorolja a Azure Time Series Insights Gen2 által támogatott adattípusokat

| Adattípus | Leírás | Példa | Tulajdonság oszlopának neve a parkettában
|---|---|---|---|
| **logikai** | Olyan adattípus, amely két állapot egyikét adja meg: `true` vagy `false` . | "isQuestionable": igaz | isQuestionable_bool
| **datetime** | Egy azonnali időpontot jelöl, amely általában dátum és napszak szerint van megadva. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) formátumban kifejezve. A DateTime tulajdonságokat a rendszer mindig UTC formátumban tárolja. Az időzóna-eltolások, ha megfelelően vannak formázva, a rendszer alkalmazza, majd az UTC szerint tárolt értéket fogja alkalmazni. Tekintse meg [ezt](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) a szakaszt a környezet timestamp tulajdonságával és a DateTime típusú eltolásokkal kapcsolatban. | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **duplán** | Kétszeres pontosságú 64 bites szám  | "érték": 31,0482941 | value_double
| **hosszú** | Aláírt 64 bites egész szám  | "érték": 31 | value_long
| **karakterlánc** | A szöveges értékeknek érvényes UTF-8 típusúnak kell lenniük. |  "site": "DIM_MLGGG" | site_string
| **dynamic** | Egy tömbből vagy egy tárolóból (szótárból) álló összetett (nem primitív) típus. Jelenleg csak a sztringesített vagy a TS ID-t, illetve az időbélyeg-t (IES) tartalmazó tömböket tartalmazó JSON-tömbök lesznek dinamikus módon tárolva. Ebből a [cikkből](./concepts-json-flattening-escaping-rules.md) megtudhatja, hogyan történik az objektumok összeolvasztása, és előfordulhat, hogy a tömbök nem helyezhetők el. Az ebben a típusban tárolt hasznos adattartalom-tulajdonságok a Azure Time Series Insights Gen2 Explorer és a GetEvents Query API használatával érhetők el. |  "Values": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * A Azure Time Series Insights Gen2-környezete erősen be van írva. Ha az eszközök vagy címkék mind az integrált, mind a nem integrált adatok küldését végzik, az eszköz tulajdonságának értékét két különálló dupla és hosszú oszlop tárolja, és az [Egyesítés () függvényt](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) kell használni API-hívások készítésekor és az idősorozat-modell változó kifejezésének definiálásakor.

#### <a name="objects-and-arrays"></a>Objektumok és tömbök

Az esemény hasznos adatainak részeként összetett típusokat (például objektumokat és tömböket) is küldhet. A beágyazott objektumokat a rendszer lelapulja, és a tömbök a `dynamic` környezeti konfigurációtól és a JSON-alakzattól függően több esemény előállítására lesznek tárolva. További információ a [JSON-összeolvasztási és-Escape-szabályokról](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>További lépések

* Olvassa el a [JSON-összeolvasztási és-Escape-szabályokat](./concepts-json-flattening-escaping-rules.md) , hogy megtudja, hogyan lesznek tárolva az események. 

* A környezet [adatátviteli korlátainak](./concepts-streaming-ingress-throughput-limits.md) megismerése

* Ismerje meg az adatfolyamok betöltéséhez szükséges [esemény-forrásokat](concepts-streaming-ingestion-event-sources.md) .
