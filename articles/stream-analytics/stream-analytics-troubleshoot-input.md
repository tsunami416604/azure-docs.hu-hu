---
title: Az Azure Stream Analytics hibaelhárítási bemenetek
description: Ez a cikk ismerteti a technikák bemenő kapcsolatok az Azure Stream Analytics-feladatok hibaelhárításához.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 6694865909a165842f994501befa404e1bc0a447
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164381"
---
# <a name="troubleshoot-input-connections"></a>A bemeneti kapcsolatok hibaelhárítása

Ezen a lapon bemeneti kapcsolatok és hibaelhárítási információkat ismertetni, azokat a gyakori problémákat ismerteti.

## <a name="input-events-not-received-by-job"></a>Nem érkezett meg a feladat bemeneti események 
1.  Tesztelje a kapcsolatot. Ellenőrizze a csatlakozási bemenetekhez és kimenetekhez a **kapcsolat tesztelése** az egyes bemeneti és kimeneti gombra.

2.  Vizsgálja meg a bemeneti adatokat.

    Győződjön meg arról, hogy a bemeneti adatok beérkeznek Event Hub, használja a [Service Bus Explorerrel](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) szeretne csatlakozni az Azure Event Hubs (ha az Event Hub-bemenet használata esetén).
        
    Használja a [ **mintaadatok** ](stream-analytics-sample-data-input.md) minden egyes bemenet gombra, és töltse le a bemeneti mintaadatokat.
        
    Vizsgálja meg a mintaadatokat az adatok megértéséhez: a séma és a [adattípusok](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Helytelen formátumú bemeneti események okok Deszerializálási hiba 
A deszerializálás problémák vannak az okozza, ha a Stream Analytics-feladat adatfolyam helytelenül formázott üzeneteket tartalmaz. Például egy helytelenül formázott üzenetet oka lehet egy hiányzó zárójelek vagy a JSON-objektum egy zárójelet, vagy egy helytelen időbélyeg formátuma a idő mezőben. 
 
Ha egy Stream Analytics-feladat bemenete egy helytelenül formázott üzenetet kap, elveti az üzeneteket, és figyelmeztetéssel értesíti. Egy figyelmeztető szimbólum jelenik meg a **bemenetek** csempe a Stream Analytics-feladat. Ez a figyelmeztetés bejelentkezési létezik mindaddig, amíg a feladat futó állapotban van:

![Az Azure Stream Analytics-bemenetek csempe](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Engedélyezze a diagnosztikai naplók a figyelmeztetést részleteinek megtekintéséhez. Helytelen formátumú bemeneti események a feladatvégrehajtási naplók tartalmaznak egy bejegyzést a következőhöz hasonló üzenetet: 
<code>Could not deserialize the input event(s) from resource <blob URI> as json.</code>

### <a name="what-caused-the-deserialization-error"></a>Mi okozta a Deszerializálási hiba
Az alábbi lépéseket a bemeneti események beolvasni a Deszerializálási hiba okáról egyértelművé részletes elemzését is igénybe vehet. Megoldhatja az eseményforrás eseményeket létrehozásához megakadályozhatja, hogy szerezze meg a probléma újra a megfelelő formátumban.

1. Keresse meg a bemeneti csempére, majd kattintson a figyelmeztető szimbólumokat a problémák listája.

2. A bemenet részletei csempét az összes hiba részleteit a figyelmeztetések listáját jeleníti meg. Az alábbi példa figyelmeztető üzenet is tartalmaz, a partíció, az eltolást és sorozatszámok helytelen formátumú JSON-adatok esetén. 

   ![Stream Analytics figyelmeztető üzenetet, és az eltolás](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. A helytelen formátumú JSON-adatok megkereséséhez futtassa a CheckMalformedEvents.cs kód érhető el a [GitHub-mintaadattár](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). A kód olvasást a Partícióazonosító, eltolás és megrendelése adott eltolás található adatok. 

4. Az adatok beolvasása után elemezheti és kijavíthatja a szerializáció formátumát.

5. Emellett [eseményeket olvas az IoT Hub, a Service Bus Explorerrel](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Feladat meghaladja a maximális Event Hub-érzékelők
Az Event Hubs használatának ajánlott eljárás, hogy több fogyasztói csoportok feladat méretezhetőség biztosítása érdekében. A Stream Analytics-feladat egy adott bevitel az olvasók számát hatással van az olvasók egyetlen felhasználói csoportban. A pontosabban megadhassák a fogadók belső megvalósítási részletei kibővített topológia logika alapján, valamint kívülről nem érhető el. Az olvasók számát egy feladat elindításakor vagy feladat-frissítések során módosíthatja.

A hiba jelenik meg, amikor a fogadók száma meghaladja a maximálisan engedélyezett a következő: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Ha az olvasók számát egy feladatot a frissítés során, átmeneti figyelmeztetések írja a naplók. Stream Analytics-feladatok automatikusan helyreállni ezek átmeneti probléma.

### <a name="add-a-consumer-group-in-event-hubs"></a>Az Event Hubs egy felhasználói csoport hozzáadása
Az Event Hubs-példány egy új felhasználói csoport hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.

2. Keresse meg az Event hubs szolgáltatás.

3. Válassza ki **az Event Hubs** alatt a **entitások** fejléc.

4. Válassza ki az Eseményközpont neve.

5. Az a **Event Hubs-példány** lapon, a **entitások** szakaszban kattintson **fogyasztói csoportok**. Egy fogyasztói csoport neve **$Default** szerepel a listán.

6. Válassza ki **+ fogyasztói csoportot** egy új felhasználói csoport hozzáadása. 

   ![Az Event Hubs egy felhasználói csoport hozzáadása](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. A bemeneti adatok az, hogy az Event Hubs mutasson a Stream Analytics-feladat létrehozásakor megadott van a fogyasztói csoportot. $Default használatos, ha nincs megadva. Miután létrehozott egy új felhasználói csoportokon, a Stream Analytics-feladat az Event Hub bemeneti szerkesztése, és adja meg az új felhasználói csoport nevét.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Az Event Hubs meghaladja az olvasók partíciónként

A streamelési lekérdezési szintaxis hivatkozik az ugyanazon bemeneti Event Hub-erőforrás többször, ha a feladat-motort használhatja a fogyasztói csoportot lekérdezésenként kibővítése több olvasóra. Ha túl sok hivatkozás az ugyanazon felhasználói csoporthoz, a feladat lépheti túl a korlátot, öt és a egy hiba lépett fel. Ilyen körülmények között további oszthatja több bemenet használatával több, a megoldás a következő szakaszban leírt használatával fogyasztói csoportja között. 

Forgatókönyvek, amelyekben az olvasók partíciónként az Event Hubs legfeljebb öt meghaladja a következők:

* Több SELECT utasítás: Ha több SELECT utasítás hivatkozó **ugyanazon** event hub bemeneti, minden egyes SELECT utasítás hatására létrejön egy új fogadó.
* UNION: Használja a UNION, esetén előfordulhat, hogy több bemenet, amely hivatkozik a **ugyanazon** event hub és a fogyasztói csoportot.
* ÖNÁLLÓAN CSATLAKOZNI: Ha egy ÖNKISZOLGÁLÓ JOIN műveletet használ, akkor lehet tekintse meg a **ugyanazon** eseményközpont többször.

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

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
