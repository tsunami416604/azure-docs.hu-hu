---
title: Az event hubs érzékelőinek Azure Stream Analytics Debug |} Microsoft Docs
description: A lekérdezés ajánlott eljárások az Event Hubs fogyasztói csoportok a Stream Analytics-feladatok figyelembe véve.
keywords: Event hub határt, a fogyasztói csoportot
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e860705d38aadf7676dad7e9ff8ed7a9af67e210
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Azure Stream Analytics a hibakereséshez event hubs érzékelőinek száma

Használhatja az Azure Event Hubs az Azure Stream Analytics betöltési vagy kimeneti egy feladat adatait. Az Event Hubs használata esetén ajánlott eljárás, hogy több felhasználói csoport használja annak feladat méretezhetőség érdekében. Egyik ok: az, hogy az adott bevitel Stream Analytics-feladat az olvasók számát hatással van az olvasók egyetlen felhasználói csoportokban. A fogadók pontos száma belső megvalósítás részletei kibővített topológia logika alapul. A fogadók száma kívülről nem lesz közzétéve. Az olvasók számát módosíthatja a feladat kezdési időpontban vagy feladat frissítéskor.

> [!NOTE]
> Ha az olvasók számát egy feladat a frissítés során, átmeneti kapcsolatos figyelmeztetések írása a naplók. Stream Analytics-feladatok automatikusan helyreállni ezek átmeneti probléma.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Partíciónként Olvasók száma meghaladja az Event Hubs legfeljebb öt

Forgatókönyvek, amelyben az olvasók partíciónként száma meghaladja az Event Hubs legfeljebb öt közé tartoznak a következők:

* Több KIVÁLASZTÓ utasítást: Ha több KIVÁLASZTÓ utasítást hivatkozó **azonos** eseményközpont bemeneti, minden KIVÁLASZTÓ utasításhoz hatására létrejön egy új fogadó.
* Unió: UNION használatához esetén előfordulhat, hogy több bemenet, amely hivatkozik a **azonos** event hub és fogyasztói csoportot.
* ÖNILLESZTÉS: Automatikus csatlakozás művelet használatakor is lehet hivatkozni a **azonos** eseményközpont több alkalommal.

## <a name="solution"></a>Megoldás

A következő gyakorlati tanácsok segíthet csökkenteni az forgatókönyvek, amelyben az olvasók partíciónként száma meghaladja az Event Hubs legfeljebb öt.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>A lekérdezés felosztása több lépést a WITH záradék használatával

A WITH záradékkal ideiglenes elnevezett eredménykészletet, amely a FROM záradék a lekérdezés hivatkozhat határozza meg. A WITH záradékkal egyetlen SELECT utasítás a végrehajtás hatókörében határozza meg.

Például helyett ezt a lekérdezést:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Használja ezt a lekérdezést:

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Győződjön meg arról, hogy bemeneti kötése különböző felhasználói csoportokhoz

A lekérdezések, amelyben legalább három bemeneti adatokat a ugyanazt az Event Hubs fogyasztói csoportot csatlakozik külön felhasználói csoportok létrehozása. Ehhez szükséges további Stream Analytics bemenetek létrehozását.


## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [A Stream Analytics bemutatása](stream-analytics-introduction.md)
* [A Stream Analytics használatába](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [A Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
