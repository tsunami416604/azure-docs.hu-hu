---
title: Teljesítmény- és horizontális leskálázás Durable Functions – Azure
description: Az Azure Functions szolgáltatáshoz a Durable Functions bővítmény bemutatása.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 3c9227a34c1b7208210b84b5b7d64ecdc8654a83
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286380"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Teljesítmény és méretezhetőség a tartós függvények (az Azure Functions)

Optimalizálható a teljesítmény és méretezhetőség, fontos tudni, hogy egyedi méretezési jellemzőit [Durable Functions](durable-functions-overview.md).

Szeretné megtudni, a skálázási viselkedés, kell megérteni az alapul szolgáló Azure Storage-szolgáltató adatait.

## <a name="history-table"></a>Előzménytáblához

A **előzmények** táblában egy Azure Storage-táblába, amely tartalmazza az előzmények események feladat hubon belüli összes orchestration-példánya számára. Ez a táblázat neve szerepel az űrlap *TaskHubName*előzményei. Példányok futtatásakor új sorok hozzáadódnak ebben a táblában. A partíciókulcs a táblázat a vezénylési Példányazonosítója származik. Példányazonosítót véletlenszerű a legtöbb esetben ez biztosítja, hogy az Azure Storage szolgáltatásban belső partíciók optimális eloszlása.

Vezénylési példányát kell futtatni, ha a korábbi tábla megfelelő sorait töltődnek be a memóriába. Ezek *előzmények események* vannak majd játssza vissza az orchestrator függvény kódjába nyerheti vissza a korábban létrehozott ellenőrzőpont állapotba kerülnek. Futtatási előzményei, így állapot újraépítése használatát befolyásolja a [Event Sourcing mintát](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Példányok tábla

A **példányok** táblában egy másik Azure Storage-táblába, amely tartalmaz egy feladat központ összes vezénylési példányok válik. Példányok létrehozásakor új sorok hozzáadódnak ebben a táblában. Ezt a táblázatot a partíciókulcs a vezénylési-példány Azonosítóját, a sorkulcs pedig egy rögzített konstans. Vezénylési példányonként egy sor van.

Ez a tábla szolgál a példány a lekérdezésekre vonatkozó kérelmek teljesítéséhez a [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) és `getStatus` (JavaScript) API-k, valamint a [vonatkozó lekérdezés HTTP API](durable-functions-http-api.md#get-instance-status). Folyamatosan idővel konzisztenssé váljanak a tartalmát a **előzmények** azt korábban említettük a táblában. Egy külön Azure Storage-táblába példány lekérdezési műveletek ily módon való hatékony felhasználása befolyásolja a [Command and Query Responsibility Segregation (CQRS) minta](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Belső eseményindítók

Tevékenység és az orchestrator függvények mindkét által aktivált a függvényalkalmazás feladat hub belső üzenetsorok. Üzenetsorok használata ily módon biztosít a megbízható ", legalább egyszeri" üzenet kézbesítési garanciával könnyítik. Durable Functions-üzenetsorokat két típusa van: a **vezérlő várólista** és a **munkaelem várólista**.

### <a name="the-work-item-queue"></a>A munkaelem várólista

Egy munkaelem soronként, Durable Functions hub feladat van. Egy alapszintű üzenetsort, és bármely más hasonlóan viselkedik `queueTrigger` várólista az Azure Functions szolgáltatásban. Ennek az üzenetsornak használatos kiváltó állapot nélküli *tevékenységfüggvényeket* dequeueing egyszerre csak egy adott üzenet alapján. Mindegyik ezeket az üzeneteket tartalmaz tevékenység függvény bemeneti, illetve további metaadatokat, például hogy melyik függvény végrehajtásához. Több virtuális gépre méretezhető egy Durable Functions-alkalmazás, amikor ezek a virtuális gépek összes versenyeznek beszerezni a munkát a munkaelem várakozási sorból.

### <a name="control-queues"></a>Vezérlő üzenetsorok

Egyszerre több *szabályozhatja az üzenetsorok* Durable Functions feladat hub száma. A *vezérlő várólista* bonyolultabb, mint a egyszerűbb munkaelem várólistát. Vezérlő üzenetsorok aktiválása az állapot-nyilvántartó orchestrator függvények használhatók. Mivel az orchestrator függvény példányai állapot-nyilvántartó singletons, nincs lehetőség osszák szét a virtuális gépek egymással versengő fogyasztó modell használatával. Ehelyett az orchestrator üzeneteket az olyan elosztott terhelésű a vezérlő üzenetsorok. Ezt a viselkedést a további részletek az ezt követő szakaszokban található.

Vezérlő várólisták többféle vezénylési életciklus üzenet típusú tartalmaznak. Ilyenek például [orchestrator vezérlő üzenetek](durable-functions-instance-management.md), tevékenység függvény *válasz* üzenetek és időzítő üzeneteket. Akár 32 darab fog kell üzenetsorból egy vezérlő lekérdezi a. Ezeket az üzeneteket tartalmaznak a hasznos adatok, valamint a metaadatok, mely vezénylési példány szól, többek között. Ha több dequeued üzenetet vezénylési ugyanazon szánt, lesznek dolgozva kötegként.

### <a name="queue-polling"></a>Lekérdezési várólista

A tartós feladat bővítmény valósít meg egy véletlenszerű exponenciális visszatartási algoritmus üresjárati várólista tárolási tranzakciós költségeket a lekérdezési hatásának csökkentése érdekében. Amikor egy üzenet található, a futtatókörnyezet azonnal keres egy másik üzenet; Ha nincs üzenet található, a ideig, mielőtt újra próbálkozna vár. Ezt követő sikertelen kísérletek várólista üzenet jelenik meg, miután a várakozási idő továbbra is nő, amíg eléri a maximális várakozási idő, alapértelmezett értéke 30 másodperc.

Maximális lekérdezési késleltetési idő legyen konfigurálhatók a `maxQueuePollingInterval` tulajdonságot a [host.json fájl](../functions-host-json.md#durabletask). E beállítás hatására a nagyobb érték magasabb üzenetfeldolgozási késések eredményezhet. Csak a tétlen időszakokat követően elvárható, nagyobb késleltetéssel jár. Ez alacsonyabb értékűre állítja azt eredményezheti, hogy magasabb tárolási költségek miatt megnövelt tárolási tranzakció.

> [!NOTE]
> Az Azure Functions-használat és a prémium szintű csomagokat, a futtatásakor a [Azure Functions méretezési vezérlő](../functions-scale.md#how-the-consumption-plan-works) elindítja a lekérdezést minden vezérlő és a munkaelem várólista 10 másodpercenként. A további lekérdezési szükség, hogy mikor alkalmazáspéldány függvény aktiválására és méretezési döntéseket hozhat. Írása idején ez 10 második időtartam alatt állandó, és nem konfigurálható.

## <a name="storage-account-selection"></a>Storage-fiók kiválasztása

Az üzenetsorok, táblák és blobok Durable Functions által használt jönnek létre a konfigurált Azure Storage-fiókban. A használni kívánt fiók használatával adható meg a `durableTask/azureStorageConnectionStringName` beállításával **host.json** fájlt.

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

Ha nincs megadva, az alapértelmezett `AzureWebJobsStorage` tárfiókot használja. Teljesítményigényes számítási feladatokhoz de egy nem alapértelmezett tárfiók konfigurálása ajánlott. Durable Functions erősen használja az Azure Storage, és a egy dedikált tárfiók használatával elkülöníti Durable Functions storage használata az Azure Functions állomás belső használatától.

## <a name="orchestrator-scale-out"></a>Az orchestrator horizontális felskálázás

Tevékenység függvények olyan állapot nélküli és a méretezett ki automatikusan adja hozzá a virtuális gépeket. Az orchestrator-függvények, másrészt olyan *particionált* egy vagy több vezérlő várólistában. Vezérlő várólisták száma van definiálva a **host.json** fájlt. A következő példa host.json kódrészlet készletek a `durableTask/partitionCount` tulajdonságot `3`.

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

Egy feladat hub is konfigurálhatók, 1 és 16 partíciók között. Ha nincs megadva, az alapértelmezett partíciók száma az **4**.

Ha horizontális felskálázás több függvény gazdagép példányra (általában a különböző virtuális gépek), minden példány zárolva van egy vezérlő üzenetsorok szerez be. Zárolás belső használatra vannak megvalósítva, a blob storage bérleteket, és győződjön meg arról, hogy egy vezénylési példányt csak egyetlen példány időpontban lefut egy. Ha egy feladat hub három vezérlő által kezelt üzenetsorok van konfigurálva, orchestration példányok lehet kiegyenlített terhelésű három virtuális gép között. További virtuális gépeket is hozzáadhatók növeli a kapacitást tevékenység függvény végrehajtásához.

A következő ábra szemlélteti a horizontálisan környezetben entitások és az Azure Functions állomás együttműködését.

![Méretezési csoport diagram](./media/durable-functions-perf-and-scale/scale-diagram.png)

Ahogy az az előző ábrán is látható, minden virtuális gép jelenlegi versenyének a munkaelem várólista üzeneteit. Azonban csak három virtuális gépet lekérheti a vezérlő üzenetsorok üzeneteit, és minden virtuális gép egyetlen ellenőrzési üzenetsor zárolja magát.

A vezénylési példányok vezérlőelem összes üzenetsor-példányok között oszlanak meg. A terjesztési a vezénylési Példányazonosítója kivonatolásával történik. Alapértelmezés szerint a Példányazonosítók véletlenszerű GUID-azonosítók, biztosítva, hogy példányok egyenlően elosztva az összes vezérlő várólistán.

Az orchestrator functions általánosan fogalmazva, nem könnyű, és nincs szükség nagy mennyiségű számítási teljesítmény. Ezért nem elengedhetetlen vezérlő nagy számú, nagy átviteli sebesség lekérdezése várólista-partíciók létrehozása. Állapot nélküli tevékenységet függvények, amelyek korlátlanul kiterjeszthető nagy munka nagy részét kell elvégezni.

## <a name="auto-scale"></a>Automatikus méretezés

Az összes Azure Functions, a Használatalapú csomagban fut, Durable Functions támogatja az automatikus skálázás használatával a [Azure Functions méretezési vezérlő](../functions-scale.md#runtime-scaling). A méretezési csoport vezérlő figyeli a késés, az összes várólista rendszeres időközönként kiállításával _betekintési_ parancsokat. A késési peeked üzenetek alapján, a méretezési csoport vezérlő fog döntsön hozzáadása vagy eltávolítása a virtuális gépek.

Ha a méretezési csoport vezérlő határozza meg, hogy vezérlő várólista üzenet késése túl magas, amíg az üzenet késése elfogadható szintre csökken, vagy a vezérlő várólista partíciók száma eléri további Virtuálisgép-példányok. Hasonlóképpen, a méretezési csoport vezérlő folyamatosan hozzáad Virtuálisgép-példányok Ha munkaelem várólista késleltetések magas, függetlenül attól, a partíciók száma.

## <a name="thread-usage"></a>Hozzászóláslánc használat

Az orchestrator függvényen annak biztosítása érdekében a végrehajtási, hogy determinisztikus között számos replays egyetlen szálon. Az egyszálas végrehajtási miatt fontos, hogy az orchestrator függvény szálak nem processzorigényes feladatokat, do i/o vagy bármilyen okból letiltása. Minden olyan munkahelyi ehhez szükség lehet az i/o blokkolja-e, vagy több szálon lesz helyezve a tevékenységfüggvényeket.

Tevékenység-függvények, ugyanazokat viselkedéseket, rendszeres üzenetsor által aktivált függvények. Akkor is biztonságosan do i/o CPU-igényű műveletek végrehajtásához és több szálat használnak. Mivel a tevékenység eseményindítók állapot nélküli, azok szabadon horizontálisan korlátlan számú virtuális gépek.

## <a name="concurrency-throttles"></a>Egyidejűségi szabályozza

Az Azure Functions támogatja, egyszerre egy egyetlen példányt belül több függvények végrehajtása. A párhuzamos végrehajtás növeli a párhuzamosságot, és minimálisra csökkentik a kiküszöbölik"", amely egy tipikus app idő múlásával tapasztalható. Azonban nagy feldolgozási magas VM-enkénti memóriahasználatot eredményezhet. Függően a függvényalkalmazás igényeit azt is szükség lehet a nagy terhelésű helyzetekben memória elfogyását lehetőségét elkerülése érdekében példányonként egyidejűségi szabályozását.

Mindkét tevékenység függvény és az orchestrator függvény egyidejűségi korlátját konfigurálható a **host.json** fájlt. A megfelelő beállítások `durableTask/maxConcurrentActivityFunctions` és `durableTask/maxConcurrentOrchestratorFunctions` jelölik.

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

Az előző példában legfeljebb 10 az orchestrator-funkciók és 10 tevékenységfüggvényeket futtathatja egyetlen virtuális gép egyszerre. Ha nincs megadva, a virtuális gép magjainak a száma X 10 maximumon a párhuzamos tevékenység és az orchestrator függvény végrehajtásainak számát.

> [!NOTE]
> Ezek a beállítások hasznosak, memória és CPU-használat a egyetlen virtuális gép kezeléséhez. Viszont ha több virtuális gép között horizontálisan minden virtuális gép lesz korlátok külön készlete. Ezek a beállítások nem használható globális szinten vezérlésére.

## <a name="orchestrator-function-replay"></a>Az orchestrator függvény visszajátszás

Ahogy korábban említettük, az orchestrator funkciók vannak játssza vissza a tartalma a **előzmények** tábla. Alapértelmezés szerint minden alkalommal, amikor egy üzenetköteget vezérlő üzenetsorból vannak az orchestrator függvénykód játssza vissza.

Ez a viselkedés agresszív visszajátszását letiltható engedélyezésével **bővített munkamenet**. Bővített munkamenet engedélyezve vannak, ha az orchestrator függvény példányok tartják hosszabb és az új üzenetek teljes ismétlés nélküli feldolgozható memóriában. Bővített munkamenet engedélyezve vannak a beállításával `durableTask/extendedSessionsEnabled` való `true` a a **host.json** fájlt. A `durableTask/extendedSessionIdleTimeoutInSeconds` beállítás van annak vezérlésére szolgál, hogy mennyi ideig-üresjárat vissza lesz tartva, a memória:

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

Bővített munkamenet engedélyezése tipikus hatásának csökken az Azure Storage-fiók és a teljes nagyobb átviteli sebességet i/o.

Azonban egy lehetséges hátulütője a szolgáltatás csak a tétlen orchestrator függvény példányok memória hosszabb ideig marad. Nincsenek két hatása érdemes figyelembe vennie:

1. A függvény alkalmazás memóriahasználat növelhető.
2. Ha sok egyidejű, rövid ideig tartó orchestrator függvénykivételek teljes csökkentheti az átviteli sebesség.

Például ha `durableTask/extendedSessionIdleTimeoutInSeconds` értéke 30 másodperc, majd egy rövid élettartamú orchestrator függvény epizód, amely végrehajtja a kevesebb mint 1 másodperc múlva továbbra is által elfoglalt memória 30 másodpercig. Ellen is beleszámít a `durableTask/maxConcurrentOrchestratorFunctions` kvóta korábban említett potenciálisan meggátolja, hogy más orchestrator függvények futtatását.

> [!NOTE]
> Ezek a beállítások csak használható egy orchestrator-funkció teljes fejlesztett és tesztelt után. Az alapértelmezett agresszív visszajátszását viselkedés hasznos idempotens hibáinak észleléséhez az orchestrator functions fejlesztéskor.

## <a name="performance-targets"></a>Teljesítmény-határértékeit

Durable Functions használata a termelési alkalmazások tervezésekor fontos figyelembe venni a tervezési folyamat korai szakaszaiban teljesítmény-követelmények. Ez a szakasz ismertet néhány alapvető használati forgatókönyvek és a várt maximális átviteli sebesség számokat.

* **Soros tevékenység végrehajtási**: Ez a forgatókönyv leírja egy orchestrator-függvény, amely után a másik egy tevékenység funkciók sorozatát futtatja. Leginkább megfelel a [függvény láncolási](durable-functions-sequence.md) minta.
* **Párhuzamos tevékenység-végrehajtási**: Ez a forgatókönyv leírja egy orchestrator-függvényt, amely végrehajtja a tevékenység számos függvénye párhuzamosan használatával a [logikájával, Fan-in](durable-functions-cloud-backup.md) mintát.
* **Párhuzamos feldolgozás válasz**: Ebben a forgatókönyvben a második felében semmit, akkor a [logikájával, Fan-in](durable-functions-cloud-backup.md) mintát. A cikk foglalkozik a fan-in teljesítményét. Fontos logikájával, eltérően fan-in egy egyetlen orchestrator függvény példány végzi el, és ezért csak akkor futtathatnak egyetlen virtuális géphez.
* **Külső eseményfeldolgozás**: Ebben a forgatókönyvben egy egyetlen orchestrator vár, a függvény példányt jelöli [külső események](durable-functions-external-events.md), egyenként.

> [!TIP]
> Erre, ellentétben a fan-in műveletek korlátozva, egyetlen virtuális Gépet. Ha az alkalmazás a logikájával, fan-in mintát használja, és fan-in teljesítmény aggódik, fontolja meg az optimálisnál több értékkel való osztásának a tevékenység függvény logikájával [alárendelt vezénylések](durable-functions-sub-orchestrations.md).

Az alábbi táblázatban láthatók a várt *maximális* átviteli számokat a leírt forgatókönyvek esetén. "Példány" hivatkozik egy orchestrator-függvényt egyetlen kisméretű futó egyetlen példányát ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) virtuális Gépen az Azure App Service-ben. Minden esetben azt feltételezzük, hogy [bővített munkamenet](#orchestrator-function-replay) engedélyezve vannak. A tényleges eredmények eltérőek lehetnek attól függően, a CPU- vagy i/o által elvégzett a függvénykódot.

| Forgatókönyv | Maximális átviteli kapacitás |
|-|-|
| Soros tevékenység-végrehajtás | példány, másodpercenként 5 tevékenység |
| Párhuzamos tevékenység-végrehajtás (logikájával) | 100 tevékenység / másodperc, példányonként |
| Válasz párhuzamos feldolgozási (fan-in) | 150-válaszok száma, példányonként |
| Külső események feldolgozása | 50 események száma másodpercenként, példányonként |

> [!NOTE]
> Ezek csak a Durable Functions bővítmény v1.4.0 (elérhetővé tétel GA) kiadása frissek. Ezek a számok idővel változhat, a szolgáltatás kiforrottá és optimalizálásokat menjenek végbe.

Ha nem jelennek meg a várt átviteli számokat és a CPU és memória használata jelenik meg a megfelelő, ellenőrizze, hogy a OK kapcsolatos [állapotát, a tárfiók](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). A Durable Functions bővítmény helyezheti jelentős betöltése az Azure Storage-fiókot, és elég magas terhelés storage-fiók szabályozás eredményezhet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az első tartós függvény létrehozása C# nyelven](durable-functions-create-first-csharp.md)
