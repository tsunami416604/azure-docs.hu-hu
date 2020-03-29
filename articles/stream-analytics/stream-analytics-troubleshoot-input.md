---
title: Az Azure Stream Analytics bemeneteinek hibáinak elhárítása
description: Ez a cikk az Azure Stream Analytics-feladatokbemeneti kapcsolatainak hibaelhárítási technikáit ismerteti.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: dac3037f82c38980c9ac16685aa7fddac68a2e7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720299"
---
# <a name="troubleshoot-input-connections"></a>Bemeneti kapcsolatok hibaelhárítása

Ez a lap a bemeneti kapcsolatokkal kapcsolatos gyakori problémákat és azok elhárítási módját ismerteti.

## <a name="input-events-not-received-by-job"></a>A feladat által nem fogadott bemeneti események 
1.  Tesztelje a kapcsolatot. Ellenőrizze a bemenetekhez és kimenetekhez való kapcsolódást a **Kapcsolat tesztelése** gombbal az egyes bemenetekhez és kimenetekhez.

2.  Vizsgálja meg a bemeneti adatokat.

    1. Annak ellenőrzéséhez, hogy a bemeneti adatok áramlik-e az Event Hub, [a Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) segítségével csatlakozzon az Azure Event Hub (ha Event Hub bemeneti használják).
        
    1. Használja a [**Mintaadatok**](stream-analytics-sample-data-input.md) gombot az egyes bevitelekhez. Töltse le a bemeneti mintaadatokat.
        
    1. Vizsgálja meg a mintaadatokat az adatok alakjának megértéséhez – azaz a séma és az [adattípusok .](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)

3.  Győződjön meg arról, hogy kijelölt egy időtartományt a bemeneti előnézetben. Válassza **az Időtartomány kiválasztása**lehetőséget, majd a lekérdezés tesztelése előtt adja meg a minta időtartamát.

## <a name="malformed-input-events-causes-deserialization-errors"></a>A helytelen formátumú bemeneti események deszerializálási hibákat okoznak 
Deszerializációs problémák okozzák, ha a bemeneti adatfolyam a Stream Analytics-feladat hibásan formázott üzeneteket tartalmaz. Egy hibásan formázott üzenetet például hiányzó zárójel vagy JSON-objektum zárójele, vagy az időmezőhelytelen időbélyeg-formátuma okozhatja. 
 
Amikor egy Stream Analytics-feladat hibásan formázott üzenetet kap egy bemenetről, elejti az üzenetet, és figyelmeztetést küld. A Stream Analytics-feladat **Bemeneti** csempéjén egy figyelmeztető szimbólum jelenik meg. Ez a figyelmeztető jel mindaddig létezik, amíg a feladat futó állapotban van:

![Az Azure Stream Analytics bemeneti csempéje](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Engedélyezze a diagnosztikai naplókat a figyelmeztetés részleteinek megtekintéséhez. Hibásan formázott bemeneti események esetén a végrehajtási naplók egy olyan bejegyzést tartalmaznak, amely a következőképpen néz ki: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Mi okozta a deszerializálási hibát?
A következő lépésekkel elemezheti a bemeneti eseményeket részletesen, hogy világosan megértse, mi okozta a deszerializációs hibát. Ezután kijavíthatja az eseményforrást, hogy az eseményeket a megfelelő formátumban hozza létre, hogy megakadályozza a probléma ismételt ütközését.

1. Keresse meg a beviteli csempét, és kattintson a figyelmeztető szimbólumokra a problémák listájának megtekintéséhez.

2. A bevitelrészletei csempe megjeleníti a figyelmeztetések listáját az egyes problémarészletekkel. Az alábbi figyelmeztető üzenet tartalmazza a partíciót, az eltolást és a sorszámokat, ahol hibásAn formázott JSON-adatok vannak. 

   ![Stream Analytics figyelmeztető üzenet eltolással](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. A JSON-adatok nem megfelelő formátumú megkereséséhez futtassa a CheckMalformedEvents.cs elérhető kódot a [GitHub-minttárházban.](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH) Ez a kód beolvassa a partíció azonosítóját, eltolását és nyomtatását az adott eltolásban található adatokat. 

4. Az adatok beolvasása után elemezheti és kijavíthatja a szerializáció formátumát.

5. Az [eseményeket az IoT Hubról is elolvashatja a Service Bus Explorer segítségével.](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b)

## <a name="job-exceeds-maximum-event-hub-receivers"></a>A feladat meghaladja a maximális eseményközpont-fogadókat
Az Event Hubs használatával kapcsolatos ajánlott eljárás több fogyasztói csoport használata a feladat méretezhetőségének biztosítása érdekében. Az olvasók száma a Stream Analytics-feladat egy adott bemenetbefolyásolja az olvasók száma egy fogyasztói csoportban. A fogadók pontos száma a horizontális felskálázási topológia logikájának belső megvalósítási részletein alapul, és külsőleg nincs kitéve. Az olvasók száma a feladat indításakor vagy a feladat frissítésekor változhat.

A következő hibaüzenet jelenik meg, ha az érzékelők száma túllépi a korlátot: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Amikor az olvasók száma megváltozik a feladat frissítése során, a rendszer átmeneti figyelmeztetéseket ír a naplóba. A Stream Analytics-feladatok automatikusan helyreállnak ezekből az átmeneti problémákból.

### <a name="add-a-consumer-group-in-event-hubs"></a>Fogyasztói csoport hozzáadása az Eseményközpontokban
Ha új fogyasztói csoportot szeretne felvenni az Event Hubs-példányba, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.

2. Keresse meg az Eseményközpontokat.

3. Válassza **az Eseményközpontok lehetőséget** az **Entitások** fejléc alatt.

4. Válassza ki az Eseményközpontot név szerint.

5. Az **Eseményközpontok példánya** lap **Entitások** fejlécében válassza a **Fogyasztói csoportok**lehetőséget. A listában **$Default** nevű fogyasztói csoport szerepel.

6. Új fogyasztói csoport hozzáadásához válassza **a + Fogyasztói csoport** lehetőséget. 

   ![Fogyasztói csoport hozzáadása az Eseményközpontokban](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Amikor létrehozta a bemeneti a Stream Analytics-feladat, hogy az Event Hub, megadta a fogyasztói csoport van. $Default akkor használatos, ha nincs megadva. Miután létrehozott egy új fogyasztói csoportot, szerkessze az Event Hub-bevitelt a Stream Analytics-feladatban, és adja meg az új fogyasztói csoport nevét.


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
