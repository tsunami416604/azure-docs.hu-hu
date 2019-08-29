---
title: Teljesítmény és méretezés a Durable Functions-ben – Azure
description: A Azure Functions Durable Functions bővítményének bemutatása.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: ed0fe22903412d4164fb3a85dbd9afafdc7023e6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098001"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Teljesítmény és méretezés Durable Functions (Azure Functions)

A teljesítmény és a méretezhetőség optimalizálása érdekében fontos megérteni a [Durable functions](durable-functions-overview.md)egyedi méretezési jellemzőit.

A méretezési viselkedés megértéséhez ismernie kell az alapul szolgáló Azure Storage-szolgáltató részleteit.

## <a name="history-table"></a>Előzmények táblázat

Az **Előzmények** tábla egy Azure Storage-tábla, amely a tevékenység központján belüli összes előkészítési példány előzményi eseményeit tartalmazza. Ennek a táblának a neve *TaskHubName*-előzmények formájában szerepel. A példányok futtatásakor a rendszer új sorokat ad hozzá ehhez a táblához. Ennek a táblának a partíciós kulcsát a rendszer az előkészítési példány azonosítójával származtatja. A példányok azonosítója a legtöbb esetben véletlenszerű, ami biztosítja a belső partíciók optimális elosztását az Azure Storage-ban.

Ha egy előkészítési példány futtatására van szükség, az előzmények tábla megfelelő sorai betöltődik a memóriába. Ezeket az előzményeket a rendszer a Orchestrator függvény kódjában játssza újra, hogy visszakapja a korábban ellenőrzőpontos állapotba. A végrehajtási előzményeknek az ilyen módon történő újraépítésére való használatát az esemény-beszerzési [minta](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)befolyásolja.

## <a name="instances-table"></a>Példányok tábla

A **instances** tábla egy másik Azure Storage-tábla, amely a feladatok központján belüli összes összehangoló példány állapotát tartalmazza. A példányok létrehozásakor a rendszer új sorokat ad hozzá ehhez a táblához. A tábla partíciós kulcsa a megszervezési példány azonosítója, a sor kulcsa pedig rögzített állandó. Egy összeszerelési példányon egy sor van.

Ez a táblázat a [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.net) és `getStatus` (JavaScript) API-k, valamint az [állapot lekérdezési http API](durable-functions-http-api.md#get-instance-status)-k által küldött példány-lekérdezési kérelmek kielégítésére szolgál. A rendszer végül konzisztensen tartja a korábban említett **History (korábbi** ) tábla tartalmát. Egy különálló Azure Storage-tábla használata a példányok lekérdezési műveleteinek hatékony kielégítése érdekében a [lekérdezési és manipulációs szerepek szétválasztása (CQRS) minta](https://docs.microsoft.com/azure/architecture/patterns/cqrs)befolyásolja.

## <a name="internal-queue-triggers"></a>Belső üzenetsor-eseményindítók

A Orchestrator functions és a Activity függvények is a belső várólisták által aktiválódnak a Function alkalmazás feladatának központja. A várólisták ily módon történő használata megbízható "legalább egyszeri" üzenet-kézbesítési garanciát biztosít. A Durable Functionsban kétféle várólista található: a **vezérlő üzenetsor** és a munkaelem **-várólista**.

### <a name="the-work-item-queue"></a>A munkahelyi elem várólistája

A Durable Functionsben egy munkaelem-várólista (Task hub) van. Ez egy alapszintű várólista, és hasonlóan viselkedik a Azure functions `queueTrigger` összes többi várólistájának. Ez a várólista az állapot nélküli *tevékenységek működésének* elindítására szolgál egy adott üzenet egyetlen üzenetből való törlésével. Mindegyik üzenet tartalmaz tevékenység-és egyéb metaadatokat, például a végrehajtandó műveletet. Ha egy Durable Functions alkalmazás több virtuális gépre is kiterjed, ezek a virtuális gépek mind versenyeznek a munkaelemek várólistából való munkához.

### <a name="control-queues"></a>Vezérlési várólista (ok)

A Durable Functionsban több *ellenőrzési várólista* található. A *vezérlő üzenetsor* sokkal kifinomultabb, mint az egyszerűbb munkaelem-várólista. A vezérlési várólisták az állapot-nyilvántartó Orchestrator funkcióinak aktiválására szolgálnak. Mivel a Orchestrator függvény példányai az állapot-nyilvántartó példányok, nem lehet versengő fogyasztói modellt használni a terhelés elosztásához a virtuális gépek között. Ehelyett a Orchestrator-üzenetek terheléselosztása a vezérlési várólisták között történik. Ennek a viselkedésnek a további részletei a következő részekben olvashatók.

A vezérlési várólisták számos különböző előkészítési életciklus-típusú üzenetet tartalmaznak. Ilyenek például a [Orchestrator-vezérlési üzenetek](durable-functions-instance-management.md), a tevékenység- *visszajelzési* üzenetek és az időzítő üzenetek. Az 32-as számú üzenetek egy lekérdezési sorból lesznek elküldve egyetlen lekérdezésben. Ezek az üzenetek hasznos adatokat és metaadatokat tartalmaznak, beleértve a kívánt előkészítési példányt is. Ha ugyanahhoz a hangelőkészítési példányhoz több elküldött üzenetet kíván, a rendszer kötegként dolgozza fel őket.

### <a name="queue-polling"></a>Üzenetsor-lekérdezés

A tartós feladathoz tartozó bővítmény egy véletlenszerű exponenciális visszakapcsolási algoritmust valósít meg, amely csökkenti az üresjárati üzenetsor lekérdezésének hatását a tárolási tranzakciós költségekre. Ha egy üzenet található, a futtatókörnyezet azonnal egy másik üzenetet keres; Ha nem talál üzenetet, egy ideig várakozik, mielőtt újra próbálkozik. A várakozási sor üzenetének későbbi sikertelen próbálkozásai után a várakozási idő továbbra is növekszik, amíg el nem éri a maximális várakozási időt, amely az alapértelmezett érték 30 másodperc.

A maximális lekérdezési késleltetés a `maxQueuePollingInterval` [Host. JSON fájl](../functions-host-json.md#durabletask)tulajdonságán keresztül konfigurálható. Ha magasabb értékre állítja ezt a beállítást, a rendszer magasabb szintű üzenetkezelési késéseket eredményezhet. A nagyobb késések csak a tétlenségi időszakok után várhatók. Ha ez alacsonyabb értékre van állítva, a megnövekedett tárolási tranzakciók miatt magasabb tárolási költségekhez vezethet.

> [!NOTE]
> A Azure Functions-felhasználás és a prémium csomagok futtatásakor a [Azure functions skálázási vezérlő](../functions-scale.md#how-the-consumption-and-premium-plans-work) 10 másodpercenként egyszer lekérdezi az egyes vezérlőket és a munkaelemek várólistáit. Ez a további lekérdezés a Function app-példányok aktiválásához és a méretezési döntések elvégzéséhez szükséges. Az írás időpontjában ez a 10 másodperces intervallum állandó, és nem konfigurálható.

## <a name="storage-account-selection"></a>Storage-fiók kiválasztása

Az Durable Functions által használt várólisták, táblák és Blobok egy konfigurált Azure Storage-fiókban jönnek létre. A használni kívánt fiók a `durableTask/azureStorageConnectionStringName` **Host. JSON** fájl beállításával adható meg.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Ha nincs megadva, a rendszer `AzureWebJobsStorage` az alapértelmezett Storage-fiókot használja. A teljesítményre érzékeny munkaterhelések esetében azonban ajánlott a nem alapértelmezett Storage-fiók konfigurálása. Durable Functions az Azure Storage szolgáltatást erősen használja, és egy dedikált Storage-fiók elkülöníti Durable Functions tárterület-használatot a Azure Functions gazdagép belső használatáról.

## <a name="orchestrator-scale-out"></a>Orchestrator kibővíthető

A tevékenységi funkciók állapot nélküliek, és a virtuális gépek hozzáadásával automatikusan méretezhetők. A Orchestrator függvények viszont egy vagy több vezérlő várólistán vannak *particionálva* . A vezérlési várólisták száma a **Host. JSON** fájlban van definiálva. A következő példa a Host. JSON kódrészletet `durableTask/partitionCount` állítja be `3`a tulajdonságra.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

A feladatok központja 1 és 16 partíciót is konfigurálhat. Ha nincs megadva, az alapértelmezett partíciók száma **4**.

Ha több Function Host-példányra (jellemzően különböző virtuális gépeken) végez skálázást, az egyes példányok a vezérlési várólisták egyikén zárolják a zárolást. Ezek a zárolások a blob Storage-bérletekben belsőleg valósulnak meg, és biztosítják, hogy egy összehangoló példány egyszerre csak egyetlen gazdagép-példányon fusson. Ha egy feladatsor három vezérlési várólistával van konfigurálva, a koordinálási példányok akár több, mint három virtuális gép között is betölthetők. További virtuális gépek is hozzáadhatók a tevékenységi funkciók végrehajtásának kapacitásának növeléséhez.

Az alábbi ábra azt szemlélteti, hogy a Azure Functions gazdagép hogyan kommunikál a tárolási entitásokkal egy kibővíthető környezetben.

![Diagram méretezése](./media/durable-functions-perf-and-scale/scale-diagram.png)

Ahogy az előző ábrán is látható, az összes virtuális gép versenyez a munkaelem-várólistán lévő üzenetekkel. Azonban csak három virtuális gép kaphat üzeneteket a vezérlési várólistákból, és mindegyik virtuális gép egyetlen vezérlő várólistát zárol.

Az előkészítési példányok az összes vezérlő üzenetsor-példány között oszlanak meg. A terjesztés az előkészítési példány AZONOSÍTÓjának kivonatolásával történik. A példány-azonosítók alapértelmezés szerint véletlenszerű GUID azonosítók, így biztosítható, hogy a példányok egyenlően oszlanak meg az összes vezérlő várólistán.

Általánosságban elmondható, hogy a Orchestrator függvények egyszerűek, és nem igényelnek nagy mennyiségű számítási teljesítményt. Ezért nem szükséges nagy mennyiségű ellenőrzési várólista-partíciót létrehozni a nagy átviteli sebesség eléréséhez. A nagy részét az állapot nélküli tevékenységek függvényében kell elvégezni, ami a végtelen méretekben méretezhető.

## <a name="auto-scale"></a>Automatikus méretezés

Ahogy a használati tervben Azure Functions fut, Durable Functions támogatja az automatikus méretezést a [Azure functions skálázási vezérlőn](../functions-scale.md#runtime-scaling)keresztül. A skálázási vezérlő figyeli az összes várólista késését a _betekintés_ parancsainak rendszeres kiállításával. A beérkező üzenetek késése alapján a méretezési vezérlő eldönti, hogy virtuális gépeket kíván-e hozzáadni vagy eltávolítani.

Ha a méretezési vezérlő meghatározza, hogy a vezérlő üzenetsor-üzeneteinek késése túl magas, akkor a virtuálisgép-példányok hozzáadására csak akkor kerül sor, ha az üzenet késése elfogadható szintre csökken, vagy eléri a vezérlési várólista partícióinak darabszámát. Hasonlóképpen, a skálázási vezérlő folyamatosan hozzá fogja adni a virtuálisgép-példányokat, ha a munkaelem-várólista késése magas, a partíciók számától függetlenül.

## <a name="thread-usage"></a>Szál használata

A Orchestrator függvények egyetlen szálon futnak, így biztosítható, hogy a végrehajtás a sok visszajátszásban is determinisztikus. Az egyszálas végrehajtás miatt fontos, hogy a Orchestrator-függvények ne végezzenek CPU-igényes feladatokat, legyenek az I/O-műveletek, illetve a blokkolás bármilyen okból. Az I/O, blokkoló vagy több szálat igénylő bármilyen munkát tevékenységi funkciókba kell áthelyezni.

A tevékenység-függvények mindegyike ugyanazokkal a viselkedésekkel rendelkezik, mint a normál üzenetsor által aktivált függvények. Biztonságosan végezhetik el az I/O-t, végrehajtják a CPU-intenzív műveleteket, és több szálat is használhatnak. Mivel a tevékenység-eseményindítók állapot nélküliek, szabadon méretezhetők a virtuális gépek számára.

## <a name="concurrency-throttles"></a>Egyidejűségi szabályozások

Azure Functions egyszerre több függvényt hajt végre egyetlen alkalmazás-példányon belül. Az egyidejű végrehajtás segít a párhuzamosság növelésében, és minimálisra csökkenti a "hideg indulások" számát, amelyet egy tipikus alkalmazás az idő múlásával fog tapasztalni. A magas Egyidejűség azonban magas/virtuális virtuálisgép-használatot eredményezhet. A Function alkalmazás igényeitől függően szükség lehet a felhasználónkénti Egyidejűség szabályozására, hogy elkerülje a nagy terhelésű helyzetekben a memória kifutásának lehetőségét.

A **Host. JSON** fájlban a tevékenység és a Orchestrator függvények párhuzamossági korlátai is konfigurálhatók. A megfelelő beállítások a `durableTask/maxConcurrentActivityFunctions` következők `durableTask/maxConcurrentOrchestratorFunctions` : és.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

Az előző példában legfeljebb 10 Orchestrator függvény és 10 tevékenységi funkció futhat egyetlen virtuális gépen egyszerre. Ha nincs megadva, az egyidejű tevékenység és a Orchestrator függvény végrehajtásának száma a virtuális gépen lévő magok száma 10X-re van korlátozva.

> [!NOTE]
> Ezek a beállítások a memória és a CPU-használat egyetlen virtuális gépen való kezeléséhez hasznosak. Ha azonban több virtuális gépen bővíti a skálázást, minden virtuális gépnek saját korlátja lesz. Ezek a beállítások nem használhatók globális szintű Egyidejűség vezérlésére.

## <a name="orchestrator-function-replay"></a>Orchestrator függvény újrajátszása

Ahogy azt korábban említettük, az Orchestrator függvények az **Előzmények** tábla tartalmával lesznek újrajátszva. Alapértelmezés szerint a rendszer minden alkalommal újra lejátssza a Orchestrator-függvény kódját, amikor az üzenetek egy kötegét a rendszer elvégzi a vezérlési sorból.

Ezt az agresszív újralejátszási viselkedést letilthatja a **kiterjesztett munkamenetek**engedélyezésével. Ha a kiterjesztett munkamenetek engedélyezve vannak, a Orchestrator függvény példányai a memóriában maradnak, és az új üzenetek teljes visszajátszás nélkül is feldolgozhatók. A kiterjesztett munkamenetek engedélyezve vannak `durableTask/extendedSessionsEnabled` a `true` **Host. JSON** fájlban való beállítással. A `durableTask/extendedSessionIdleTimeoutInSeconds` beállítással szabályozható, hogy mennyi ideig tart a rendszer az üresjárati munkamenetet a memóriában:

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

A kiterjesztett munkamenetek engedélyezésének tipikus hatása az Azure Storage-fiókra és a teljes továbbfejlesztett átviteli sebességre csökken.

Ennek a funkciónak a hátránya azonban az, hogy az inaktív Orchestrator függvény példányai továbbra is a memóriában maradnak. Két tényezőt kell figyelembe venni:

1. Általános növekedés a Function app memóriahasználat használatával.
2. Az átviteli sebesség teljes csökkenése, ha sok párhuzamos, rövid élettartamú Orchestrator függvény végrehajtása történik.

Ha `durableTask/extendedSessionIdleTimeoutInSeconds` például 30 másodpercre van beállítva, akkor egy rövid élettartamú Orchestrator függvény, amely az 1 másodpercnél kevesebbet hajt végre, 30 másodpercig továbbra is elfoglalja a memóriát. Emellett a `durableTask/maxConcurrentOrchestratorFunctions` korábban említett kvótával is számolni kell, ami megakadályozhatja, hogy más Orchestrator függvények fussanak.

> [!NOTE]
> Ezeket a beállításokat csak akkor kell használni, ha egy Orchestrator függvényt teljesen fejlesztettek ki és teszteltek. Az alapértelmezett agresszív újralejátszási viselkedés hasznos lehet a idempotencia hibák észleléséhez a Orchestrator-függvényekben a fejlesztési időszakban.

## <a name="performance-targets"></a>Teljesítménybeli célok

Ha éles alkalmazások Durable Functions használatát tervezi használni, fontos, hogy a tervezési folyamat elején vegye figyelembe a teljesítménnyel kapcsolatos követelményeket. Ez a szakasz néhány alapszintű használati forgatókönyvet és a várt maximális átviteli sebességet ismerteti.

* **Szekvenciális tevékenységek végrehajtása**: Ez a forgatókönyv egy Orchestrator függvényt mutat be, amely egy sor tevékenységi funkciót futtat egy másik után. A leginkább hasonlít a [függvény láncolására](durable-functions-sequence.md) szolgáló mintára.
* **Párhuzamos tevékenységek végrehajtása**: Ez a forgatókönyv egy olyan Orchestrator függvényt ír le, amely sok tevékenység-funkciót hajt végre párhuzamosan a [ventilátor-out, ventilátor-in](durable-functions-cloud-backup.md) mintázat használatával.
* **Párhuzamos reagálások feldolgozása**: Ez a forgatókönyv a [fan-out, a fan-in](durable-functions-cloud-backup.md) mintázat második fele. A ventilátor teljesítményére koncentrál. Fontos megjegyezni, hogy a ventilátortól eltérően a ventilátort egyetlen Orchestrator-függvény példánya végzi el, ezért csak egyetlen virtuális gépen futtatható.
* **Külső események feldolgozása**: Ez a forgatókönyv egyetlen Orchestrator-függvényt képvisel, amely a [külső eseményekre](durable-functions-external-events.md)vár egy adott időpontban.

> [!TIP]
> A ventilátorokkal ellentétben a ventilátoros műveletek egyetlen virtuális gépre korlátozódnak. Ha az alkalmazás a ventilátort, a ventilátort használja, és Ön aggódik a ventilátorok teljesítményével kapcsolatban, vegye fontolóra a tevékenység funkcióinak több alfolyamaton belüli felosztását [](durable-functions-sub-orchestrations.md).

A következő táblázat a korábban ismertetett forgatókönyvek várható *maximális* átviteli számát mutatja. A "példány" egy Orchestrator függvény egyetlen példányára hivatkozik, amely egy kisméretű ([a1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) virtuális gépen fut Azure app Serviceban. A rendszer minden esetben feltételezi, hogy a [kiterjesztett munkamenetek](#orchestrator-function-replay) engedélyezve vannak. A tényleges eredmények a kód által végzett CPU-vagy I/O-műveletektől függően változhatnak.

| Forgatókönyv | Maximális átviteli kapacitás |
|-|-|
| Szekvenciális tevékenységek végrehajtása | 5 tevékenység másodpercenként, a példányok száma szerint |
| Párhuzamos tevékenységek végrehajtása (ventilátor – kimenő) | 100 tevékenység/másodperc/példány |
| Párhuzamos reagálások feldolgozása (ventilátor) | 150 válasz másodpercenként, a példányok száma szerint |
| Külső események feldolgozása | 50 esemény másodpercenként, a példányok száma szerint |

> [!NOTE]
> Ezek a számok a Durable Functions bővítmény v 1.4.0 (GA) kiadásában aktuálisak. Ezek a számok idővel megváltozhatnak, amikor a funkció kiforr, és optimalizálást végez.

Ha nem látja a várt átviteli sebességet, és a CPU és a memóriahasználat állapota Kifogástalan, ellenőrizze, hogy az ok a [Storage-fiók állapotára](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)vonatkozik-e. Az Durable Functions bővítmény jelentős terhelést helyezhet el egy Azure Storage-fiókban, és a megfelelő terhelés miatt a Storage-fiókok szabályozása is lehetséges.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az első tartós függvény létrehozása C# nyelven](durable-functions-create-first-csharp.md)
