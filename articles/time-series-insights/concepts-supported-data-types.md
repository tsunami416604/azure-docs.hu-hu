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
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170874"
---
# <a name="supported-data-types"></a>Támogatott adattípusok

A következő táblázat felsorolja a Azure Time Series Insights Gen2 által támogatott adattípusokat

| Adattípus | Leírás | Példa | Tulajdonság oszlopának neve a parkettában
|---|---|---|---|
| **logikai** | Olyan adattípus, amely két állapot egyikét adja meg: `true` vagy `false` . | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Egy azonnali időpontot jelöl, amely általában dátum és napszak szerint van megadva. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) formátumban kifejezve. A DateTime tulajdonságokat a rendszer mindig UTC formátumban tárolja. Az időzóna-eltolások, ha megfelelően vannak formázva, a rendszer alkalmazza, majd az UTC szerint tárolt értéket fogja alkalmazni. Tekintse meg [ezt](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) a szakaszt a környezet timestamp tulajdonságával és a DateTime típusú eltolásokkal kapcsolatban. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | Kétszeres pontosságú 64 bites szám  | `"value": 31.0482941` | value_double
| **long** | Aláírt 64 bites egész szám  | `"value" : 31` | value_long
| **karakterlánc** | A szöveges értékeknek érvényes UTF-8 típusúnak kell lenniük. A null értékű és az üres karakterláncok azonosak. |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | Egy tömbből vagy egy tárolóból (szótárból) álló összetett (nem primitív) típus. Jelenleg csak a sztringesített vagy a TS ID-t, illetve az időbélyeg-tulajdonságot (IES) tartalmazó tömböket tartalmazó JSON-tömbök lesznek tárolva dinamikusként. Ebből a [cikkből](./concepts-json-flattening-escaping-rules.md) megtudhatja, hogyan lehet összeállítani az objektumokat, és lehet, hogy a tömbök nem állnak le. Az ebben a típusban tárolt hasznos adattartalom-tulajdonságok a Azure Time Series Insights Gen2 Explorer és a `GetEvents`   lekérdezési API használatával érhetők el. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Vegyes adattípusok küldése

A Azure Time Series Insights Gen2-környezete erősen be van írva. Ha az eszközök vagy címkék különböző típusú adattípusokat küldenek egy eszköz tulajdonságnak, az értékeket két külön oszlopban tárolja a rendszer, és az [Egyesítés () függvényt](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions) kell használni, amikor az API-hívásokban az idősorozat-modell változó kifejezéseit definiálja.

A Azure Time Series Insights Explorer lehetővé teszik, hogy automatikusan egyesítse az azonos eszköz tulajdonságának különálló oszlopait. Az alábbi példában az érzékelő egy olyan `PresentValue` tulajdonságot küld, amely egyszerre hosszú vagy dupla is lehet. Ha a tulajdonság összes tárolt értékére (az adattípustól függetlenül) szeretne lekérdezni `PresentValue` , válassza `PresentValue (Double | Long)` a és az oszlopok coalesced lehetőséget.

[![Explorer automatikus egyesítése](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objektumok és tömbök

Az esemény hasznos adatainak részeként összetett típusokat (például objektumokat és tömböket) is küldhet. A beágyazott objektumokat a rendszer lelapulja, és a tömbök a `dynamic` környezeti konfigurációtól és a JSON-alakzattól függően több esemény előállítására lesznek tárolva. További információ a [JSON-összeolvasztási és-Escape-szabályokról](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>További lépések

* Olvassa el a [JSON-összeolvasztási és-Escape-szabályokat](./concepts-json-flattening-escaping-rules.md) , hogy megtudja, hogyan lesznek tárolva az események.

* A környezet [adatátviteli korlátainak](./concepts-streaming-ingress-throughput-limits.md) megismerése

* Ismerje meg az adatfolyamok betöltéséhez szükséges [esemény-forrásokat](concepts-streaming-ingestion-event-sources.md) .
