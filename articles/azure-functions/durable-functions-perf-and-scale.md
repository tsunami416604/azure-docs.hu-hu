---
title: Teljesítmény és méretezhetőség tartós funkciókkal – Azure
description: Bevezetés az Azure Functions a tartós funkciók bővítmény.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 18a3f3080c58e01117e0fc73adad76d28c298536
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Teljesítmény és méretezhetőség tartós funkciókkal (az Azure Functions)

Optimalizálható a teljesítmény és méretezhetőség, fontos tudni, hogy az egyedi méretezési jellemzői [tartós funkciók](durable-functions-overview.md).

Jobban átláthatja a méretezési viselkedését, hogy az Azure Storage Alapszolgáltató részleteit megismerését.

## <a name="history-table"></a>Előzménytábla

A **előzmények** táblát kell egy Azure Storage belül egy feladat hub példányainak vezénylési előzmények eseményeinek tartalmazó tábla. Ez a táblázat neve nem formájában *TaskHubName*előzményeit. Példányokat futtatni, mert ez a táblázat új sorok kerülnek. A partíciós kulcs ezt a táblázatot a vezénylési Példányazonosítója származik. Példányazonosító az esetek többségében véletlenszerű amely biztosítja, hogy az Azure Storage belső partíciók optimális terjesztése.

Vezénylési példány kell futtatni, amikor az előzmények megfelelő sorainak töltődnek be a memóriába. Ezek *előzmények események* vannak majd újból kattintva visszatérhet a korábban létrehozott ellenőrzőpont állapotba orchestrator függvény kódba. Futtatási előzményei, így állapotát, hogy használatát befolyásolja a [esemény forrás mintát](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Példányok tábla

A **példányok** tábla egy másik feladat hubot belül szoftverpéldányok vezénylési állapotok tartalmazó Azure Storage tábla. Példány létrehozásához szükségesek, új sorokat a tábla kerülnek. A partíciós kulcs ezt a táblázatot az orchestration-példány azonosítója és a sorkulcs egy rögzített konstans. Soronként egy vezénylési példány van.

Ezzel a táblával példány lekérdezés kérelmeinek teljesítéséhez a [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) API, valamint a [vonatkozó lekérdezés HTTP API](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-http-api#get-instance-status). Folyamatosan idővel konzisztenssé váljanak a tartalmát a **előzmények** azt korábban említettük a táblában. Példány lekérdezési műveletek, így hatékony kielégítéséhez külön Azure Storage tábla használatát befolyásolja a [parancs és a lekérdezés felelősségi elkülönítése (CQRS) minta](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Belső eseményindítók

Tevékenység és az orchestrator függvények mindkét váltja ki a függvény app feladat központban belső várólisták. Várólisták használata ily módon biztosít a megbízható ": legalább egyszeri" üzenet kézbesítési garanciával. A tartós funkciók várólisták két típusa van: a **vezérlő várólista** és a **munkaelem várólista**.

### <a name="the-work-item-queue"></a>A munkaelem várólistát

Egy munkaelem várólista / tartós funkciók csomópontjában feladat van. Egy alapszintű várólista, és más hasonló módon viselkedik `queueTrigger` az Azure Functions várólista. A várólista használatával indul el, az állapot nélküli *tevékenység funkciók* által dequeueing egyszerre csak egyetlen üzenetben. Ezek az üzenetek mindegyikének tartalmaz tevékenység függvény bemeneti, illetve további metaadatokat, például mely függvény végrehajtása. Ha a tartós funkciók alkalmazás méretezi ki több virtuális gépekhez, minden virtuális gépeken "versenyeznek" szerezni a munkaelem várólista munka.

### <a name="control-queues"></a>Vezérlő várólisták

Egyszerre több *várólisták szabályozása* / tartós funkciók feladat csomópontjában. A *vezérlő várólista* bonyolultabb, mint a egyszerűbb munkaelem várólistát. Vezérlő várólisták indul el, az állapot-nyilvántartó orchestrator függvények használhatók. Mivel az orchestrator függvény példányai állapot-nyilvántartó singletons, nincs lehetőség a terhelés szétosztását a virtuális gépek versengő fogyasztó modell használatával. Ehelyett az orchestrator üzenetek olyan elosztott terhelésű a vezérlő várólistában. Ez a viselkedés a további részletek az ezt követő szakaszok található.

Vezérlő várólisták vezénylési életciklus üzenettípusok számos tartalmaznak. Példák [orchestrator vezérlő üzenetek](durable-functions-instance-management.md), tevékenység függvény *válasz* üzenetek és időzítő üzeneteket. Akár 32 üzenetek fog kell üzenetsorból egy vezérlő lekérdezi a. Ezek az üzenetek tartalmaznak a hasznos adatok, valamint a metaadatok, többek között a vezénylési példányok számára készült. Ha több dequeued üzenetet az orchestration ugyanazon, azok dolgoz fel kötegelt feladatként.

## <a name="storage-account-selection"></a>Storage-fiók kiválasztása

A várólisták, a táblák és a tartós funkciók által használt blobokat létrehozta a konfigurált Azure Storage-fiók. A használandó fiókot adhat meg a `durableTask/azureStorageConnectionStringName` beállítása az **host.json** fájlt.

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

Ha nincs megadva, az alapértelmezett `AzureWebJobsStorage` tárfiókot használja. A teljesítmény-érzékeny munkaterhelésekhez azonban a nem alapértelmezett storage-fiókok konfigurálása ajánlott. Tartós funkciók fokozottan Azure a tárolót használja, és dedikált tárfiókot használ elkülöníti a tartós funkciók tárhelykvótájának használata az Azure Functions állomás belső használatra való.

## <a name="orchestrator-scale-out"></a>Az orchestrator kibővített

Tevékenység függvények olyan állapot nélküli és a kimenő méretezett automatikusan virtuális gépek hozzáadásával. Az orchestrator függvények, másrészt olyan *particionált* egy vagy több vezérlő várólistában. A vezérlő várólisták számát definiálva van a **host.json** fájlt. A következő példa host.json részlet készletek a `durableTask/partitionCount` tulajdonságot `3`.

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```
Egy feladat hub 1 és 16 partíciók között konfigurálható. Ha nincs megadva, az alapértelmezett partíciószám van **4**.

Amikor kiterjesztése több függvény állomás példány (általában a különböző virtuális gépek), akkor minden példány zárolás a vezérlő várólisták egyik. Ezek a zárolások belső megvalósított módon blob-tároló címbérleteket, és győződjön meg arról, hogy az orchestration példány csak egyetlen példányán időpontban lefut egy. Ha egy feladat hub három vezérlő várólistákkal van konfigurálva, orchestration példányok lehet terhelésű három virtuális gépek között. További virtuális gépeket lehet hozzáadni a növelheti tevékenység függvény végrehajtását.

A következő ábra szemlélteti, és a tárolási entitások méretezett kimenő környezetben az Azure Functions állomás együttműködését.

![Diagram méretezése](media/durable-functions-perf-and-scale/scale-diagram.png)

Ahogy az előző ábrán is látható, akkor minden virtuális gép "versenyeznek" a munkaelem várólistát üzeneteket. Azonban csak három virtuális gépek lekérheti a vezérlő várólistákból üzeneteket, és minden virtuális gép egyetlen vezérlő várólista zárolja.

A vezénylési példányok vezérlő várólista példányainak különböző pontjain. A terjesztési a vezénylési Példányazonosítója kivonatolásával történik. Példány alapértelmezés szerint azonosítói véletlenszerű GUID, győződjön meg arról, hogy az összes vezérlő várólistán egyaránt elosztott példányok.

Az orchestrator függvények általánosan fogalmazva, célja, hogy könnyű kell, és nem szükséges számítási teljesítményt nagy mennyiségű. Nincs ezért partíciókat vezérlő nagy számú várólista nagy átviteli sebesség eléréséhez szükséges. A nagy munka nagyobb része az állapot nélküli tevékenységet függvények, amelyek kiterjeszthető végtelenül kell elvégezni.

## <a name="auto-scale"></a>Automatikus méretezés

Az összes Azure Functions a fogyasztás tervben fut, tartós funkciók automatikus méretezése keresztül támogatja a [Azure Functions méretezése vezérlő](functions-scale.md#runtime-scaling). A skála vezérlő figyeli a késés az összes várólista rendszeres kiállításával _betekintés_ parancsok. A késések peeked üzenetek alapján, a skála vezérlő fog döntsön hozzáadása vagy eltávolítása a virtuális gépek.

Ha a skála vezérlő határozza meg, hogy vezérlő várólista üzenet túl magas, akkor addig, amíg a üzenet várakozási csökkenti elfogadható szintre, vagy a vezérlő várólista partíciók száma eléri további Virtuálisgép-példányok. Hasonlóképpen a méretezési vezérlő folyamatosan adja hozzá Virtuálisgép-példányok Ha munkaelem várólista magas, függetlenül attól, a partíciók száma.

## <a name="thread-usage"></a>A szál kihasználtsága

Az orchestrator funkciókat hajtja végre, annak érdekében, hogy végrehajtás sok felhasználásait között determinisztikus lehet egyetlen szálból. A egyszálas végrehajtása miatt fontos, hogy az orchestrator függvény szálak nem processzorigényes feladatok végrehajtása, do i/o vagy bármilyen okból blokkolása. Munka, amely lehet i/o, blokkolja, vagy több szál a tevékenység függvényekké helyezni.

Tevékenység funkciók lehet ugyanazokat viselkedések rendszeres várólista-eseményindítókkal aktivált függvényeket. Hogy biztonságosan do i/o, CPU-intenzív műveleteket, és több szál. Mivel a tevékenység eseményindítók állapotmentes, akkor lehet szabadon horizontálisan virtuális gépek unbounded száma.

## <a name="concurrency-throttles"></a>Párhuzamossági azelőtt gyorsítja fel

Az Azure Functions egyetlen alkalmazáspéldányban belül egyidejűleg több funkciók végrehajtását támogatja. A párhuzamos végrehajtása segítségével növelheti a párhuzamosságot és minimálisra csökkentik a "hidegindítások", amelyeknek a tipikus app adott idő alatt. Azonban nagy feldolgozási magas – a virtuális Gépenkénti memóriahasználatot eredményezhet. A függvény app igényeitől függően elképzelhető szabályozása a példányonkénti egyidejűségi kevés a memória nagyléptékű helyzetekben lehetősége elkerülése érdekében.

Mindkét tevékenységet és az orchestrator függvény feldolgozási korlátok konfigurálható a **host.json** fájlt. A vonatkozó beállítások `durableTask/maxConcurrentActivityFunctions` és `durableTask/maxConcurrentOrchestratorFunctions` kulcsattribútumokkal.

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
  }
}
```

Az előző példában legfeljebb 10 orchestrator és függvények 10 tevékenység egyidejűleg is futtathatók a egyetlen virtuális gép. Ha nincs megadva, a párhuzamos tevékenységek és az orchestrator függvény végrehajtások száma tárfiókonként maximum 10 X a virtuális Gépen lévő magok száma.

> [!NOTE]
> Ezek a beállítások kezeléséhez a memória és CPU-használat a egy virtuális lehetnek hasznosak. Azonban horizontálisan több virtuális gépek között, ha egyes virtuális gépek rendelkezik saját vonatkozó korlátok beállítása készletét. Ezek a beállítások nem használható globális szinten vezérlésére.

## <a name="orchestrator-function-replay"></a>Az orchestrator függvény ismétlés
Amint azt korábban említettük, az orchestrator funkciók vannak a rendszer játssza tartalma a **előzmények** tábla. Alapértelmezés szerint az orchestrator függvény kódot a rendszer játssza minden alkalommal, amikor az üzenetkötegek vezérlő üzenetsorból vannak.

Ez a viselkedés agresszív ismétlési letiltható engedélyezésével **munkamenetek kiterjesztett**. Kiterjesztett munkamenetek, ha engedélyezve vannak az orchestrator függvény példányok tartják hosszabb és az új üzenetek teljes ismétlés nélkül tudja feldolgozni a memóriában. Úgy, hogy engedélyezve vannak a kiterjesztett munkamenetek `durableTask/extendedSessionsEnabled` való `true` a a **host.json** fájlt. A `durableTask/extendedSessionIdleTimeoutInSeconds` beállítás van annak vezérlésére szolgál, hogy mennyi ideig az üresjárat vissza lesz tartva, a memória:

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

A tipikus hatása kiterjesztett munkamenetek engedélyezése csökken az Azure Storage-fiók és a teljes jobb teljesítményt i/o.

A szolgáltatás egy potenciális hátránya azonban a tétlen orchestrator függvény példányok hosszabb a memóriában marad. Tisztában lenni a két hatása van:

1. A függvény app memóriahasználat növelhető.
2. Sok egyidejű, rövid élettartamú orchestrator függvény végrehajtások esetén a teljes csökkenti az átviteli sebesség.

Tegyük fel Ha `durableTask/extendedSessionIdleTimeoutInSeconds` értéke 30 másodperc, majd egy rövid élettartamú orchestrator függvény epizód, amely végrehajtja a kevesebb mint 1 másodperc múlva továbbra is elhelyezkedése memória 30 másodpercig. Rendszer is csökkenti a `durableTask/maxConcurrentOrchestratorFunctions` kvóta korábban említett potenciálisan akadályozza meg, hogy más orchestrator függvények futását.

> [!NOTE]
> Ezeket a beállításokat csak egy orchestrator függvény teljesen fejlesztése és tesztelése után kell használni. Agresszív ismétlési alapértelmezés lesz hasznos idempotencia hibáinak észleléséhez az orchestrator funkciók fejlesztése során.

## <a name="performance-targets"></a>Teljesítménycéloknak

Tervezi a tartós funkciók használata termelési alkalmazások esetében, fontos figyelembe venni a tervezési folyamat korai szakaszaiban teljesítménybeli követelményei. Ez a szakasz ismertet néhány alapvető használati forgatókönyvek és a várható maximális átviteli sebesség számok.

* **Soros tevékenység végrehajtási**: Ez a forgatókönyv leírja egy orchestrator függvény, amely után a másik egy tevékenység funkciók egy sorozatát futtatja. A leginkább megfelel a [függvény láncolás](durable-functions-sequence.md) minta.
* **Párhuzamos tevékenység végrehajtása**: Ez a forgatókönyv leírja egy orchestrator-függvény, amely számos tevékenység funkciót végrehajtja párhuzamosan a [szétterítési, Fan-in](durable-functions-cloud-backup.md) mintát.
* **Párhuzamos feldolgozás válasz**: Ebben a forgatókönyvben a második felében a [szétterítési, Fan-in](durable-functions-cloud-backup.md) mintát. A cikk foglalkozik a fan-in teljesítményét. Fontos megjegyezni, hogy szétterítési, eltérően fan-in egy egyetlen orchestrator függvény példány végezhető el, és ezért csak futtathatja egy virtuális.
* **Külső esemény feldolgozása**: A forgatókönyv bemutat egy egyetlen orchestrator függvény példányt, amely megvárja-e a [külső események](durable-functions-external-events.md), egyenként.

> [!TIP]
> Szétterítési, eltérően fan-in műveletek esetében csak egy virtuális. Ha az alkalmazás a kívánt szétterítési, fan-in mintát használja, és fan-in teljesítmény aggódik, érdemes lehet részterv osztani a tevékenység függvény szétterítési több [alárendelt álló üzenettípusok összehangolását](durable-functions-sub-orchestrations.md).

Az alábbi táblázatban láthatók a várható *maximális* átviteli számokat a leírt forgatókönyvek esetén. "Példány" hivatkozik egy példányban fut a egyetlen kis orchestrator funkcióinak ([A1](../virtual-machines/windows/sizes-general.md#a-series)) méretű Azure App Service-ben. Minden esetben feltételezzük, hogy [munkamenetek kiterjesztett](#orchestrator-function-replay) engedélyezve vannak. Tényleges eredmény eltérhetnek attól függően, hogy a Processzor- vagy i/o munkahelyi végzi el a funkciókódot.

| Forgatókönyv | Maximális átviteli sebesség |
|-|-|
| Soros tevékenység végrehajtása | 5 tevékenységek másodpercenként példányonként |
| Párhuzamos tevékenység végrehajtási (szétterítési) | 100 tevékenységek másodpercenként példányonként |
| Párhuzamos adott válasz feldolgozása (fan-in) | 150-válaszok száma, példányonként |
| Külső esemény feldolgozása | 50 események másodpercenkénti, példányonként |

> [!NOTE]
> Ezek csak a tartós funkciók bővítmény v1.4.0 (GA) kiadás aktuális. Ezeket a számokat idővel megváltozhat, a szolgáltatás Miután kiforrottá válik, és mivel optimalizálásokat történik.

Ha nem látja a várt átviteli számok és a CPU, és a memóriahasználat jelenik meg a megfelelő, ellenőrizze, hogy a OK kapcsolatos [a tárfiók állapotának](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). A bővítmény helyezhetik jelentős tartós funkciók betölteni az Azure Storage-fiók, és elég magas terhelés tárolási fiók sávszélesség-szabályozás eredményezhet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Telepítse a tartós funkciók bővítményt, és minták](durable-functions-install.md)
