---
title: Event Hubs érzékelőinek száma az Azure Stream Analytics hibaelhárítása
description: Ez a cikk ismerteti a Stream Analytics-feladatok az Event Hubs bemenetek több felhasználói csoport használatával.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312956"
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Event Hubs érzékelőinek száma az Azure Stream Analytics hibaelhárítása

Használhatja az Azure Event Hubs az Azure Stream Analytics betöltési vagy kimeneti egy feladat adatait. Az Event Hubs használata esetén ajánlott eljárás, hogy több felhasználói csoport használja annak feladat méretezhetőség érdekében. Egyik ok: az, hogy az adott bevitel Stream Analytics-feladat az olvasók számát hatással van az olvasók egyetlen felhasználói csoportokban. A fogadók pontos száma belső megvalósítás részletei kibővített topológia logika alapul. A fogadók száma kívülről nem lesz közzétéve. Az olvasók számát módosíthatja a feladat kezdési időpontban vagy feladat frissítéskor.

A hiba látható, ha a fogadók száma meghaladja a maximálisan engedélyezett a következő: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Ha az olvasók számát egy feladat a frissítés során, átmeneti kapcsolatos figyelmeztetések írása a naplók. Stream Analytics-feladatok automatikusan helyreállni ezek átmeneti probléma.

## <a name="add-a-consumer-group-in-event-hubs"></a>Az Event Hubs egy felhasználói csoport hozzáadása
Az Event Hubs-példányt egy új felhasználói csoport hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.

2. Keresse meg az Eseményközpontok.

3. Válassza ki **Event Hubs** alatt a **entitások** fejléc.

4. Válasszon ki az Eseményközpont nevét.

5. A a **Event Hubs példány** lap a **entitások** elemcsoportban válasszon **fogyasztói csoportok**. Egy felhasználói csoport nevű **$Default** szerepel.

6. Válassza ki **+ fogyasztói csoportot** egy új felhasználói csoport hozzáadása. 

   ![Az Event Hubs egy felhasználói csoport hozzáadása](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. A bemeneti a Stream Analytics-feladat, mutasson az Event hubs létrehozásakor megadott van a fogyasztói csoportot. $Default használatos, ha nincs megadva. Ha létrehoz egy új felhasználói csoport, a Stream Analytics-feladat az Eseményközpont bemenet szerkesztése, és adja meg az új felhasználói csoport nevét.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Partíciónként Olvasók száma meghaladja az Event Hubs legfeljebb öt

Ha a folyamatos átviteli lekérdezés szintaxisát az Eseményközpont ugyanazon bemeneti erőforrás többször hivatkozik, a feladat motor használhatja ezt a fogyasztói csoportot a lekérdezésenként több olvasók. Ha túl sok hivatkozás a felhasználói csoportba, a feladat is legfeljebb öt és a hiba lépett fel. Ilyen körülmények között további oszthatja több bemeneti adatok használatával a következő szakaszban leírt megoldással több fogyasztói csoporttal. 

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


## <a name="next-steps"></a>További lépések
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
