---
title: Az Azure Stream Analytics bemeneteinek hibáinak elhárítása
description: Ez a cikk az Azure Stream Analytics-feladatokbemeneti kapcsolatainak hibaelhárítási technikáit ismerteti.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409422"
---
# <a name="troubleshoot-input-connections"></a>Bemeneti kapcsolatok hibaelhárítása

Ez a cikk ismerteti az Azure Stream Analytics bemeneti kapcsolatokkal kapcsolatos gyakori problémákat, a bemeneti problémák elhárításának módját és a problémák megoldásának módját. Számos hibaelhárítási lépéshez engedélyezni kell a diagnosztikai naplókat a Stream Analytics-feladathoz. Ha nincs engedélyezve a diagnosztikai naplók, olvassa el [az Azure Stream Analytics hibaelhárítása diagnosztikai naplók használatával című témakört.](stream-analytics-job-diagnostic-logs.md)

## <a name="input-events-not-received-by-job"></a>A feladat által nem fogadott bemeneti események 

1.  Tesztelje a bemeneti és kimeneti kapcsolatot. Ellenőrizze a bemenetekhez és kimenetekhez való kapcsolódást a **Kapcsolat tesztelése** gombbal az egyes bemenetekhez és kimenetekhez.

2.  Vizsgálja meg a bemeneti adatokat.

    1. Használja a [**Mintaadatok**](stream-analytics-sample-data-input.md) gombot az egyes bevitelekhez. Töltse le a bemeneti mintaadatokat.
        
    1. Vizsgálja meg a mintaadatokat a séma és [az adattípusok megismeréséhez.](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
    
    1. Ellenőrizze [az Event Hub metrikákat,](../event-hubs/event-hubs-metrics-azure-monitor.md) hogy megbizonyosodjon arról, események küldése. Az üzenetmutatóknak nullánál nagyobbnak kell lenniük, ha az Event Hubs üzeneteket fogad.

3.  Győződjön meg arról, hogy kijelölt egy időtartományt a bemeneti előnézetben. Válassza **az Időtartomány kiválasztása**lehetőséget, majd a lekérdezés tesztelése előtt adja meg a minta időtartamát.

## <a name="malformed-input-events-causes-deserialization-errors"></a>A helytelen formátumú bemeneti események deszerializálási hibákat okoznak 

Deszerializációs problémák okozzák, ha a bemeneti adatfolyam a Stream Analytics-feladat hibásan formázott üzeneteket tartalmaz. Egy hibásan formázott üzenetet például egy JSON-objektumban lévő hiányzó zárójel vagy zárójel, vagy az időmező helytelen időbélyeg-formátuma okozhatja. 
 
Amikor egy Stream Analytics-feladat hibásan formázott üzenetet kap egy bemenetről, elejti az üzenetet, és figyelmeztetést küld. A Stream Analytics-feladat **Bemeneti** csempéjén egy figyelmeztető szimbólum jelenik meg. A következő figyelmeztető szimbólum mindaddig létezik, amíg a feladat futó állapotban van:

![Az Azure Stream Analytics bemeneti csempéje](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Engedélyezze a diagnosztikai naplókat a hibát okozó hiba és az üzenetet (hasznos adat) részleteinek megtekintéséhez. Ennek több oka is lehet a deszerializálási hibák nak. Az egyes deszerializálási hibákkal kapcsolatos további információkért [lásd: Bemeneti adatok hibái](data-errors.md#input-data-errors). Ha a diagnosztikai naplók nincsenek engedélyezve, egy rövid értesítés lesz elérhető az Azure Portalon.

![A bemenet részleteifigyelmeztetés](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Azokban az esetekben, amikor az üzenet hasznos adata nagyobb, mint 32 KB, vagy bináris formátumú, futtassa a CheckMalformedEvents.cs-kódot a [GitHub mintatárban.](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH) Ez a kód beolvassa a partíció azonosítóját, eltolását és nyomtatását az adott eltolásban található adatokat. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>A feladat meghaladja az Event Hub-fogadók maximális

Az Event Hubs használatával kapcsolatos ajánlott eljárás, ha több fogyasztói csoportot használ a feladat méretezhetőségéhez. Az olvasók száma a Stream Analytics-feladat egy adott bemenetbefolyásolja az olvasók száma egy fogyasztói csoportban. A fogadók pontos száma a horizontális felskálázási topológia logikájának belső megvalósítási részletein alapul, és külsőleg nincs kitéve. Az olvasók száma a feladat indításakor vagy a feladat frissítésekor változhat.

A következő hibaüzenet jelenik meg, ha az érzékelők száma túllépi a korlátot: 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Amikor az olvasók száma megváltozik a feladat frissítése során, a rendszer átmeneti figyelmeztetéseket ír a naplóba. A Stream Analytics-feladatok automatikusan helyreállnak ezekből az átmeneti problémákból.

### <a name="add-a-consumer-group-in-event-hubs"></a>Fogyasztói csoport hozzáadása az Eseményközpontokban

Ha új fogyasztói csoportot szeretne felvenni az Event Hubs-példányba, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.

2. Keresse meg az Event Hubot.

3. Válassza **az Eseményközpontok lehetőséget** az **Entitások** fejléc alatt.

4. Válassza ki az Eseményközpontot név szerint.

5. Az **Eseményközpontok példánya** lap **Entitások** fejlécében válassza a **Fogyasztói csoportok**lehetőséget. A listában **$Default** nevű fogyasztói csoport szerepel.

6. Új fogyasztói csoport hozzáadásához válassza **a + Fogyasztói csoport** lehetőséget. 

   ![Fogyasztói csoport hozzáadása az Eseményközpontokban](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Amikor létrehozta a bemeneti a Stream Analytics-feladat, hogy az Event Hub, megadta a fogyasztói csoport van. **$Default** akkor használatos, ha nincs megadva. Miután létrehozott egy új fogyasztói csoportot, szerkessze az Event Hub-bevitelt a Stream Analytics-feladatban, és adja meg az új fogyasztói csoport nevét.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>A partíciónkénti olvasók túllépik az Eseményközpontok korlátját

Ha a streamelési lekérdezés szintaxisa többször hivatkozik ugyanarra a bemeneti Event Hub-erőforrásra, a feladatmotor ugyanazon fogyasztói csoport lekérdezésenként több olvasót is használhat. Ha túl sok hivatkozás van ugyanarra a fogyasztói csoportra, a feladat meghaladhatja az öt fős korlátot, és hibát okozhat. Ilyen körülmények között tovább oszthat több bemenethasználatával több fogyasztói csoportban a következő szakaszban ismertetett megoldás használatával. 

Olyan esetek, amikor a partíciónkénti olvasók száma meghaladja az Event Hubs öt százalékos korlátját, a következőket tartalmazzák:

* Több SELECT utasítás: Ha több SELECT utasítást használ, amelyek **ugyanarra** az eseményközpont-bemenetre hivatkoznak, minden SELECT utasítás új fogadót hoz létre.

* UNION használata esetén lehetőség van arra, hogy több bemenettel is rendelkezik, amelyek **ugyanarra** az eseményközpontra és fogyasztói csoportra hivatkoznak.

* SELF JOIN: Self JOIN művelet használataesetén többször is hivatkozhat **ugyanarra** az eseményközpontra.

Az alábbi ajánlott eljárások segíthetnek a forgatókönyvek csökkentésében, amelyekben a partíciónkénti olvasók száma meghaladja az Event Hubs öt százalékos korlátját.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>A lekérdezés felosztása több lépésre WITH záradék kal

A WITH záradék egy ideiglenes nevű eredményhalmazt határoz meg, amelya lekérdezésben egy FROM záradékkal hivatkozható. Egyetlen SELECT utasítás végrehajtási hatókörében definiálja a WITH záradékot.

A lekérdezés helyett például:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Használja ezt a lekérdezést:

```SQL
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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Annak biztosítása, hogy a bemenetek különböző fogyasztói csoportokhoz kötődjenek

Olyan lekérdezések esetén, amelyekben három vagy több bemenet kapcsolódik ugyanahhoz az Event Hubs fogyasztói csoporthoz, hozzon létre külön fogyasztói csoportokat. Ehhez további Stream Analytics-bemenetek létrehozására van szükség.

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
