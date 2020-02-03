---
title: Az Azure Stream Analytics hibaelhárítási bemenetek
description: Ez a cikk ismerteti a technikák bemenő kapcsolatok az Azure Stream Analytics-feladatok hibaelhárításához.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: dac3037f82c38980c9ac16685aa7fddac68a2e7b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720299"
---
# <a name="troubleshoot-input-connections"></a>Bemeneti kapcsolatok hibaelhárítása

Ezen a lapon bemeneti kapcsolatok és hibaelhárítási információkat ismertetni, azokat a gyakori problémákat ismerteti.

## <a name="input-events-not-received-by-job"></a>Nem érkezett meg a feladat bemeneti események 
1.  Tesztelje a kapcsolatot. Az egyes bemenetekhez és kimenetekhez tartozó **kapcsolat tesztelése** gomb használatával ellenőrizze, hogy kapcsolódik-e a bemenetekhez és kimenetekhez.

2.  Vizsgálja meg a bemeneti adatokat.

    1. Annak ellenőrzéséhez, hogy a bemeneti adatok beáramlanak-e az Event hub-ba, a [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) használatával kapcsolódjon az Azure Event hub-hoz (ha az Event hub bemenete van használatban).
        
    1. Használja az egyes bemenetek [**mintaadatok**](stream-analytics-sample-data-input.md) gombját. Töltse le a bemeneti mintaadatok.
        
    1. Tekintse át a mintaadatok az adatalakzatok, azaz a séma és az [adattípusok](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)értelmezéséhez.

3.  Győződjön meg arról, hogy a bemeneti előnézetben időtartományt jelölt ki. Válassza az **időtartomány kiválasztása**lehetőséget, majd adja meg a minta időtartamát a lekérdezés tesztelése előtt.

## <a name="malformed-input-events-causes-deserialization-errors"></a>A helytelen formátumú bemeneti események deszerializálási hibákat okoznak 
A deszerializálás problémák vannak az okozza, ha a Stream Analytics-feladat adatfolyam helytelenül formázott üzeneteket tartalmaz. Például egy helytelenül formázott üzenetet oka lehet egy hiányzó zárójelek vagy a JSON-objektum egy zárójelet, vagy egy helytelen időbélyeg formátuma a idő mezőben. 
 
Ha egy Stream Analytics-feladat bemenete egy helytelenül formázott üzenetet kap, elveti az üzeneteket, és figyelmeztetéssel értesíti. A Stream Analytics feladatának **bemenetek** csempén figyelmeztető szimbólum jelenik meg. Ez a figyelmeztetés bejelentkezési létezik mindaddig, amíg a feladat futó állapotban van:

![Az Azure Stream Analytics-bemenetek csempe](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Engedélyezze a diagnosztikai naplók a figyelmeztetést részleteinek megtekintéséhez. Helytelen formátumú bemeneti események a feladatvégrehajtási naplók tartalmaznak egy bejegyzést a következőhöz hasonló üzenetet: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Mi okozta a Deszerializálási hiba
Az alábbi lépéseket a bemeneti események beolvasni a Deszerializálási hiba okáról egyértelművé részletes elemzését is igénybe vehet. Megoldhatja az eseményforrás eseményeket létrehozásához megakadályozhatja, hogy szerezze meg a probléma újra a megfelelő formátumban.

1. Keresse meg a bemeneti csempére, majd kattintson a figyelmeztető szimbólumokat a problémák listája.

2. A bemenet részletei csempét az összes hiba részleteit a figyelmeztetések listáját jeleníti meg. Az alábbi példa figyelmeztető üzenet is tartalmaz, a partíció, az eltolást és sorozatszámok helytelen formátumú JSON-adatok esetén. 

   ![Figyelmeztető üzenet Stream Analytics eltolással](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Ha a JSON-adatformátumot nem megfelelő formátumban szeretné megkeresni, futtassa a [GitHub-minták tárházában](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)elérhető CheckMalformedEvents.cs-kódot. A kód olvasást a Partícióazonosító, eltolás és megrendelése adott eltolás található adatok. 

4. Az adatok beolvasása után elemezheti és kijavíthatja a szerializáció formátumát.

5. [Az IoT hub az Service Bus Explorerrel is beolvashatja az eseményeket](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Feladat meghaladja a maximális Event Hub-érzékelők
Az Event Hubs használatának ajánlott eljárás, hogy több fogyasztói csoportok feladat méretezhetőség biztosítása érdekében. A Stream Analytics-feladat egy adott bevitel az olvasók számát hatással van az olvasók egyetlen felhasználói csoportban. A pontosabban megadhassák a fogadók belső megvalósítási részletei kibővített topológia logika alapján, valamint kívülről nem érhető el. Az olvasók számát egy feladat elindításakor vagy feladat-frissítések során módosíthatja.

A következő hibaüzenet jelenik meg, ha az érzékelők száma túllépi a korlátot: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Ha az olvasók számát egy feladatot a frissítés során, átmeneti figyelmeztetések írja a naplók. Stream Analytics-feladatok automatikusan helyreállni ezek átmeneti probléma.

### <a name="add-a-consumer-group-in-event-hubs"></a>Az Event Hubs egy felhasználói csoport hozzáadása
Az Event Hubs-példány egy új felhasználói csoport hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.

2. Keresse meg az Event hubs szolgáltatás.

3. Válassza ki **Event Hubs** az **entitások** fejléc alatt.

4. Válassza ki az Eseményközpont neve.

5. A **Event Hubs példány** lapon az **entitások** fejléc alatt válassza a **fogyasztói csoportok**lehetőséget. Megjelenik egy **$default** nevű fogyasztói csoport a listáján.

6. Válassza a **+ fogyasztói csoport** lehetőséget egy új fogyasztói csoport hozzáadásához. 

   ![Az Event Hubs egy felhasználói csoport hozzáadása](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. A bemeneti adatok az, hogy az Event Hubs mutasson a Stream Analytics-feladat létrehozásakor megadott van a fogyasztói csoportot. $Default használatos, ha nincs megadva. Miután létrehozott egy új felhasználói csoportokon, a Stream Analytics-feladat az Event Hub bemeneti szerkesztése, és adja meg az új felhasználói csoport nevét.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Az Event Hubs meghaladja az olvasók partíciónként

A streamelési lekérdezési szintaxis hivatkozik az ugyanazon bemeneti Event Hub-erőforrás többször, ha a feladat-motort használhatja a fogyasztói csoportot lekérdezésenként kibővítése több olvasóra. Ha túl sok hivatkozás az ugyanazon felhasználói csoporthoz, a feladat lépheti túl a korlátot, öt és a egy hiba lépett fel. Ilyen körülmények között további oszthatja több bemenet használatával több, a megoldás a következő szakaszban leírt használatával fogyasztói csoportja között. 

Forgatókönyvek, amelyekben az olvasók partíciónként az Event Hubs legfeljebb öt meghaladja a következők:

* Több SELECT utasítás: Ha több SELECT utasítást használ, amelyek **ugyanarra** az Event hub-bemenetre hivatkoznak, akkor minden SELECT utasítás új fogadót hoz létre.
* UNION: Ha Uniót használ, több bemenet is lehet, amelyek **ugyanarra** az Event hub-ra és a fogyasztói csoportra vonatkoznak.
* Önillesztés: Ha önillesztési műveletet használ, lehetséges, hogy többször is hivatkozhat **ugyanarra** az Event hub-ra.

A következő gyakorlati tanácsok segíthet csökkenteni az forgatókönyvek, amelyekben az olvasók partíciónként meghaladja az Event Hubs legfeljebb öt.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>A lekérdezés felosztása több lépést a WITH záradék használatával

A WITH záradékkal egy ideiglenes elnevezett eredményhalmazt, amely hivatkozhat a FROM záradék a lekérdezés meghatározza. A végrehajtási hatóköre egyetlen SELECT utasítással határozhatja meg a WITH záradékkal.

Ha például helyett Ez a lekérdezés:

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Győződjön meg arról, hogy bemeneti kötést létrehozni különböző felhasználói csoportok

A lekérdezések három vagy több bemeneti, amelyben az Event Hubs ugyanazon felhasználói csoporthoz csatlakoztatott hozzon létre külön felhasználói csoportot. Ehhez a Stream Analytics további bemenetek létrehozása.

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
