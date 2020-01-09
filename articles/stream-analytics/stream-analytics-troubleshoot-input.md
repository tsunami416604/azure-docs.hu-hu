---
title: Azure Stream Analytics-bemenetek hibaelhárítása
description: Ez a cikk a Azure Stream Analytics-feladatokban lévő bemeneti kapcsolatok hibaelhárítási módszereit ismerteti.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 20a161ffc82cb8f74cfcac838856434f83c4e258
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354285"
---
# <a name="troubleshoot-input-connections"></a>Bemeneti kapcsolatok hibaelhárítása

Ez a lap a bemeneti kapcsolatokkal és azok hibaelhárításával kapcsolatos gyakori problémákat ismerteti.

## <a name="input-events-not-received-by-job"></a>A feladatok által nem fogadott bemeneti események 
1.  Tesztelje a kapcsolatot. Az egyes bemenetekhez és kimenetekhez tartozó **kapcsolat tesztelése** gomb használatával ellenőrizze, hogy kapcsolódik-e a bemenetekhez és kimenetekhez.

2.  Vizsgálja meg a bemeneti adatokat.

    Annak ellenőrzéséhez, hogy a bemeneti adatok beáramlanak-e az Event hub-ba, a [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) használatával kapcsolódjon az Azure Event hub-hoz (ha az Event hub bemenete van használatban).
        
    Használja az egyes bemenetek [**mintaadatok**](stream-analytics-sample-data-input.md) gombját, és töltse le a bemeneti mintaadatok közül.
        
    A mintaadatok megvizsgálása az adatalakzatok értelmezése érdekében: a séma és az [adattípusok](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="malformed-input-events-causes-deserialization-errors"></a>A helytelen formátumú bemeneti események deszerializálási hibákat okoznak 
A deszerializálási problémákat akkor okozhatja a rendszer, ha a Stream Analytics-feladathoz tartozó bemeneti adatfolyam helytelenül formázott üzeneteket tartalmaz. A helytelenül formázott üzenetet például egy hiányzó zárójel vagy egy JSON-objektumban lévő kapcsos zárójel vagy egy helytelen időbélyeg-formátum okozhatja a Time mezőben. 
 
Ha egy Stream Analytics-feladatban helytelenül formázott üzenet érkezik egy bemenetből, a rendszer elveti az üzenetet, és figyelmeztetést küld. A Stream Analytics feladatának **bemenetek** csempén figyelmeztető szimbólum jelenik meg. Ez a figyelmeztetési jel akkor létezik, ha a feladatsor futási állapotban van:

![Azure Stream Analytics bemenetek csempe](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

A figyelmeztetés részleteinek megtekintéséhez engedélyezze a diagnosztikai naplókat. A helytelenül formázott bemeneti események esetében a végrehajtási naplók egy olyan bejegyzést tartalmaznak, amely az alábbihoz hasonló üzenetet tartalmaz: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Mi okozta a deszerializálási hibát
A következő lépések végrehajtásával részletesen elemezheti a bemeneti eseményeket, hogy egyértelműen megértse, mi okozta a deszerializálási hibát. Az eseményforrás kijavításával a megfelelő formátumban állíthatja be az eseményeket, így megakadályozhatja a probléma újbóli elindítását.

1. Navigáljon a beviteli csempéhez, és kattintson a figyelmeztető szimbólumokra a problémák listájának megtekintéséhez.

2. A bemeneti adatok csempe a figyelmeztetések listáját jeleníti meg az egyes problémák részleteivel együtt. Az alábbi figyelmeztető üzenet tartalmazza a partíciós, az eltolási és a sorszámokat, amelyekben helytelenül formázott JSON-adatok szerepelnek. 

   ![Figyelmeztető üzenet Stream Analytics eltolással](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Ha a JSON-adatformátumot nem megfelelő formátumban szeretné megkeresni, futtassa a [GitHub-minták tárházában](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)elérhető CheckMalformedEvents.cs-kódot. Ez a kód beolvassa a partíció AZONOSÍTÓját, eltolását, és kinyomtatja az adott eltolásban található adatokat. 

4. Az adatok beolvasása után elemezheti és kijavíthatja a szerializáció formátumát.

5. [Az IoT hub az Service Bus Explorerrel is beolvashatja az eseményeket](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>A feladatoknál nagyobb az Event hub-vevőkészülékek maximális száma
A Event Hubs használatának ajánlott gyakorlata több fogyasztói csoport használata a feladatok méretezhetőségének biztosítása érdekében. Egy adott bemenet Stream Analytics feladatában szereplő olvasók száma az egyetlen fogyasztói csoportban lévő olvasók számát érinti. A fogadók pontos száma a kibővíthető topológia logikájának belső implementációs részletein alapul, és nem külsőleg van kitéve. Az olvasók száma a feladatok indításakor vagy a feladatok frissítésekor változhat.

A következő hibaüzenet jelenik meg, ha az érzékelők száma túllépi a korlátot: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Ha az olvasók száma megváltozik a feladatok frissítésekor, a rendszer átmeneti figyelmeztetéseket ír a naplókba. Stream Analytics a feladatok automatikusan helyreállítják ezeket az átmeneti problémákat.

### <a name="add-a-consumer-group-in-event-hubs"></a>Fogyasztói csoport hozzáadása Event Hubs
Ha új fogyasztói csoportot szeretne hozzáadni a Event Hubs-példányban, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.

2. Keresse meg a Event Hubs.

3. Válassza ki **Event Hubs** az **entitások** fejléc alatt.

4. Válassza ki az Event hub nevet név szerint.

5. A **Event Hubs példány** lapon az **entitások** fejléc alatt válassza a **fogyasztói csoportok**lehetőséget. Megjelenik egy **$default** nevű fogyasztói csoport a listáján.

6. Válassza a **+ fogyasztói csoport** lehetőséget egy új fogyasztói csoport hozzáadásához. 

   ![Fogyasztói csoport hozzáadása Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Amikor létrehozta a bemenetet a Stream Analytics feladatban az Event hub-ra való Rámutatás céljából, ott adta meg a fogyasztói csoportot. Ha nincs megadva, a rendszer $Defaultt használ. Miután létrehozta az új fogyasztói csoportot, szerkessze az Event hub-bemenetet a Stream Analytics feladatban, és adja meg az új fogyasztói csoport nevét.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Az olvasók/partíciók száma meghaladja Event Hubs korlátot

Ha az adatfolyam-lekérdezési szintaxis többször is ugyanarra a bemeneti Event hub-erőforrásra hivatkozik, akkor a keresőmotor több olvasót is használhat egy adott felhasználói csoportból lekérdezéssel. Ha túl sok hivatkozás fordul elő ugyanahhoz a fogyasztói csoporthoz, a feladatokhoz legfeljebb öt korlát léphet fel. Ilyen körülmények között a következő szakaszban ismertetett megoldással tovább oszthat meg több adatbevitelt több fogyasztói csoport között. 

Az olyan forgatókönyvek, amelyekben a partíciók száma meghaladja az 5 Event Hubs korlátot, a következők:

* Több SELECT utasítás: Ha több SELECT utasítást használ, amelyek **ugyanarra** az Event hub-bemenetre hivatkoznak, akkor minden SELECT utasítás új fogadót hoz létre.
* UNION: Ha Uniót használ, több bemenet is lehet, amelyek **ugyanarra** az Event hub-ra és a fogyasztói csoportra vonatkoznak.
* Önillesztés: Ha önillesztési műveletet használ, lehetséges, hogy többször is hivatkozhat **ugyanarra** az Event hub-ra.

Az alábbi ajánlott eljárások segíthetnek enyhíteni az olyan forgatókönyveket, amelyekben a partíciók száma meghaladja az 5 Event Hubs korlátot.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>A lekérdezés felosztása több lépésre a WITH záradék használatával

A WITH záradék egy ideiglenes elnevezett eredményhalmazt ad meg, amelyre a lekérdezés FROM záradéka hivatkozhat. A WITH záradék definiálása egyetlen SELECT utasítás végrehajtási hatókörében végezhető el.

Például a következő lekérdezés helyett:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

A lekérdezés használata:

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Győződjön meg arról, hogy a bemenetek a különböző fogyasztói csoportokba vannak kötve

Azokhoz a lekérdezésekhez, amelyekben három vagy több bemenet csatlakozik ugyanahhoz a Event Hubs fogyasztói csoporthoz, hozzon létre külön fogyasztói csoportokat. Ehhez további Stream Analytics bemeneteket kell létrehoznia.

## <a name="get-help"></a>Segítség

További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
