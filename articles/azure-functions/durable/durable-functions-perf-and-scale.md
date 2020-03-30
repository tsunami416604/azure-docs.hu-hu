---
title: Teljesítmény és méretezés a tartós funkciókban – Azure
description: Bevezetés az Azure Functions tartós függvények bővítményébe.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277907"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Teljesítmény és méretezés a Durable Functionsben (Azure Functions)

A teljesítmény és a méretezhetőség optimalizálása érdekében fontos megérteni a tartós függvények egyedi méretezési [jellemzőit.](durable-functions-overview.md)

A méretezési viselkedés megértéséhez meg kell értenie az alapul szolgáló Azure Storage-szolgáltató néhány részletét.

## <a name="history-table"></a>Előzmények tábla

Az **Előzmények** tábla egy Azure Storage-tábla, amely tartalmazza az előzményesemények et a feladatközponton belüli összes vezénylési példányhoz. A tábla neve a *TaskHubName History*formában található. A példányok futtatásakor a rendszer új sorokat ad hozzá a táblához. A tábla partíciókulcsa a vezénylés példányazonosítójából származik. Egy példányazonosító véletlenszerű a legtöbb esetben, amely biztosítja a belső partíciók optimális elosztását az Azure Storage-ban.

Ha egy vezénylési példányt futtatni kell, az Előzmény tábla megfelelő sorai betöltődnek a memóriába. Ezeket *az előzményeseményeket* ezután visszajátssza az orchestrator függvénykódba, hogy visszakerüljön a korábban ellenőrzőponton lévő állapotába. A végrehajtási előzmények ily módon történő újraépítésére való használatát befolyásolja az [Eseményforrás minta.](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)

## <a name="instances-table"></a>Példányok tábla

A **példányok** tábla egy másik Azure Storage-tábla, amely tartalmazza az összes vezénylési és entitáspéldányok egy feladatközponton belül állapotát. A példányok létrehozásakor új sorok kerülnek a táblába. A tábla partíciókulcsa a vezénylési példányazonosító vagy entitáskulcs, a sorkulcs pedig rögzített állandó. Vezénylési vagy entitáspéldányonként egy sor van.

Ez a tábla a (.NET) és `GetStatusAsync` `getStatus` a (JavaScript) API-kból, valamint az [állapotlekérdezés HTTP API-jától](durable-functions-http-api.md#get-instance-status)érkező példánylekérdezési kérelmek kielégítésére szolgál. Ez végül összhangban van a korábban említett **Előzmény** tábla tartalmával. Egy külön Azure Storage-tábla használatával hatékonyan megfelel a példány lekérdezési műveletek ily módon befolyásolja a [parancs és a lekérdezési felelősség elkülönítése (CQRS) minta.](https://docs.microsoft.com/azure/architecture/patterns/cqrs)

## <a name="internal-queue-triggers"></a>Belső várólista-eseményindítók

Az Orchestrator-függvényeket és a tevékenységfüggvényeket a függvényalkalmazás feladatközpontjában lévő belső várólisták is aktiválják. A várólisták ily módon történő használata megbízható "legalább egyszer" üzenetkézbesítési garanciákat biztosít. A Tartós függvények függvényekben kétféle sorsor létezik: a **vezérlővárólista** és a **munkatétel-várólista.**

### <a name="the-work-item-queue"></a>A munkatétel-várólista

A tartós függvényekben feladatközpontonként egy munkaelem-várólista található. Ez egy alapvető várólista, és az `queueTrigger` Azure Functions bármely más várólistához hasonlóan viselkedik. Ez a várólista állapotmentes *tevékenységfüggvények* indítására szolgál, ha egyszerre csak egy üzenet várólistájának törlését szeretné elindítani. Ezek az üzenetek mindegyike tevékenységfüggvény-bemeneteket és további metaadatokat tartalmaz, például a végrehajtandó függvényt. Ha egy tartós függvények alkalmazás horizontális felskálázása több virtuális gépre, ezek a virtuális gépek mind versenyeznek a munkatétel-várólistából történő munka megszerzéséért.

### <a name="control-queues"></a>Vezérlő várólista(ok)

A tartós függvényekben feladatközpontonként több *vezérlősor* található. A *vezérlőváró lista* kifinomultabb, mint az egyszerűbb munkatétel-várólista. A vezérlővárólisták az állapotalapú orchestrator és entitásfüggvények indítására szolgálnak. Mivel az orchestrator és az entitás függvénypéldányai állapotalapú singletons, nem lehet használni a versengő fogyasztói modell elosztása terhelés virtuális gépek között. Ehelyett az orchestrator és az entitás üzenetek terheléselosztása a vezérlő várólisták között. A viselkedéssel kapcsolatos további részletek a következő szakaszokban találhatók.

A vezérlővárólisták számos vezénylési életciklus-üzenettípust tartalmaznak. Ilyenek például [az orchestrator vezérlő üzenetek,](durable-functions-instance-management.md)a tevékenységfüggvény-válaszüzenetek és az időzítőüzenetek. *response* A vezérlővárólistából 32 üzenet lesz egyetlen szavazáson. Ezek az üzenetek hasznos adatokat, valamint metaadatokat tartalmaznak, beleértve azt is, hogy melyik vezénylési példánynak szánták. Ha több dequeued üzenetek célja az azonos vezénylési példány, akkor feldolgozása kötegként lesz.

### <a name="queue-polling"></a>Várólista lekérdezése

A tartós feladatbővítmény egy véletlenszerű, exponenciális visszakeresési algoritmust valósít meg, hogy csökkentse az automatikus várólista-lekérdezés hatását a tárolási tranzakciós költségekre. Ha egy üzenet található, a futásidejű azonnal ellenőrzi egy másik üzenetet; ha nem található üzenet, egy ideig várakozik, mielőtt újra próbálkozna. A várólista-üzenet lehívására tett későbbi sikertelen próbálkozások után a várakozási idő tovább növekszik, amíg el nem éri a maximális várakozási időt, amely alapértelmezés szerint 30 másodperc.

A maximális lekérdezési késleltetés `maxQueuePollingInterval` a [host.json fájlban](../functions-host-json.md#durabletask)lévő tulajdonságon keresztül konfigurálható. Ha ezt a tulajdonságot magasabb értékre állítja, az üzenetfeldolgozási késések et eredményezhet. Magasabb késések csak az inaktív időszakok után várhatók. Ha ezt a tulajdonságot alacsonyabb értékre állítja, a megnövekedett tárolási tranzakciók miatt magasabb tárolási költségeket eredményezhet.

> [!NOTE]
> Az Azure Functions Consumption és premium csomagokban való futtatáskor az [Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) 10 másodpercenként egyszer lefolytatja az egyes vezérlők és munkaelemek várólistáját. Ez a további lekérdezés szükséges annak meghatározásához, hogy mikor kell aktiválni a függvényalkalmazás-példányokat, és milyen méretezési döntéseket kell hozni. Az írás időpontjában ez a 10 másodperces időköz állandó, ezért nem konfigurálható.

## <a name="storage-account-selection"></a>Tárfiók kiválasztása

A tartós függvények által használt várólisták, táblák és blobok egy konfigurált Azure Storage-fiókban jönnek létre. A használni hozandó fiók a `durableTask/storageProvider/connectionStringName` `durableTask/azureStorageConnectionStringName` **host.json** fájlban található (vagy a Durable Functions 1.x) beállítással adható meg.

### <a name="durable-functions-2x"></a>Tartós funkciók 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Tartós funkciók 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Ha nincs megadva, `AzureWebJobsStorage` a rendszer az alapértelmezett tárfiókot használja. A teljesítményérzékeny számítási feladatok hoz, azonban egy nem alapértelmezett tárfiók konfigurálása ajánlott. A Durable Functions erősen használja az Azure Storage-t, és egy dedikált tárfiók használatával elkülöníti a Durable Functions storage-használatot az Azure Functions gazdagép belső használatától.

## <a name="orchestrator-scale-out"></a>Orchestrator kibővített

A tevékenységfüggvények állapot nélküliek, és virtuális gépek hozzáadásával automatikusan kivannak skálázva. Az Orchestrator függvényei és entitásai viszont egy vagy több vezérlővárólistáközött *vannak felosztva.* A vezérlővárólisták száma a **host.json** fájlban van definiálva. A következő példa a host.json `durableTask/storageProvider/partitionCount` kódrészlet `durableTask/partitionCount` a tulajdonságot (vagy `3`a Durable Functions 1.x-ben) a értékre állítja.

### <a name="durable-functions-2x"></a>Tartós funkciók 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Tartós funkciók 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

A feladatközpont 1 és 16 partíció között konfigurálható. Ha nincs megadva, az alapértelmezett partíciószám **4**.

Több függvénygazdapéldányra való horizontális felskálázáskor (általában különböző virtuális gépeken) minden példány zárolást szerez az egyik vezérlővárólistán. Ezek a zárolások blob storage-bérletekként vannak megvalósítva, és biztosítják, hogy egy vezénylési példány vagy entitás egyszerre csak egyetlen gazdagépen futjon. Ha egy feladatközpont három vezérlővárólistával van konfigurálva, a vezénylési példányok és az entitások akár három virtuális gép terheléselosztása is elvégezhetők. További virtuális gépek is hozzáadhatók a tevékenységfunkció-végrehajtás kapacitásának növelése érdekében.

Az alábbi ábra bemutatja, hogy az Azure Functions gazdagép hogyan lép kölcsönhatásba a tárolóentitásokkal egy kibővített környezetben.

![Méretezésdiagram](./media/durable-functions-perf-and-scale/scale-diagram.png)

Ahogy az előző ábrán látható, minden virtuális gép versenyeznek a munkaelem-várólistán lévő üzenetekért. Azonban csak három virtuális gép kaphat üzeneteket a vezérlővárólistákból, és minden virtuális gép egyetlen vezérlővárólistát zárol.

Vezénylési példányok és entitások között oszlik meg az összes vezérlővárólista példányok. A terjesztés a vezénylés i. vagy az entitásnév és kulcspár példányazonosítójának kivonatolásával történik. Vezénylési példányazonosítók alapértelmezés szerint véletlenszerű GUID- ok, biztosítva, hogy a példányok egyenlően oszlanak el az összes vezérlővárólisták között.

Általánosságban elmondható, hogy az orchestrator funkciók könnyűek, és nem igényelnek nagy mennyiségű számítási teljesítményt. Ezért nem szükséges nagy számú vezérlőváróvár-partíciók létrehozása a vezénylések nagy átviteli. A legtöbb nehéz munkát kell végezni a stateless tevékenység funkciók, amelyek lehet kicsinyített végtelenül.

## <a name="auto-scale"></a>Automatikus méretezés

A Fogyasztásés rugalmas prémium csomagban futó összes Azure-függvényhez, a Durable Functions is támogatja az automatikus skálázást az [Azure Functions méretezési vezérlőn](../functions-scale.md#runtime-scaling)keresztül. A Méretezési vezérlő az összes várólista késését figyeli _betekintő_ parancsok rendszeres időközönként. A betekintő üzenetek késése alapján a méretezési vezérlő dönti el, hogy virtuális gépeket ad-e hozzá vagy távolít el.

Ha a méretezési vezérlő megállapítja, hogy a vezérlőüzenet-késleltetések túl magasak, virtuálisgép-példányokat ad hozzá, amíg az üzenet késése elfogadható szintre nem csökken, vagy el nem éri a vezérlővárólista partíciók számát. Hasonlóképpen a méretezési vezérlő folyamatosan hozzáadja a virtuálisgép-példányokat, ha a munka-elem várólista késése magas, függetlenül a partíciók számától.

> [!NOTE]
> A Durable Functions 2.0-s verziótól kezdve a függvényalkalmazások konfigurálhatók úgy, hogy az Elastic Premium csomag VNET-védelemmel ellátott szolgáltatásvégpontokon belül fussanak. Ebben a konfigurációban a tartós függvények a méretezési vezérlő helyett méretezési kérelmeket kezdeményeznek.

## <a name="thread-usage"></a>Szál használata

Az Orchestrator-függvények egyetlen szálon hajthatók végre annak érdekében, hogy a végrehajtás számos visszajátszásban determinisztikus legyen. Az egyszálas végrehajtás miatt fontos, hogy az orchestrator függvényszálai semmilyen okból ne hajtsanak végre CPU-igényes feladatokat, ne végezzenek I/O-t vagy tiltsanak le. Minden olyan munkát, amely i/o-t, blokkolást vagy több szálat igényelhet, tevékenységfunkciókba kell helyezni.

A tevékenységfüggvények viselkedése megegyezik a várólista által aktivált rendszeres függvényekkel. Biztonságosan elvégezhetik az I/O-t, processzorigényes műveleteket hajthatnak végre, és több szálat használhatnak. Mivel a tevékenységeseményindítók állapot nélküliek, szabadon skálázhatók a virtuális gépek korlátlan számára.

Az entitásfüggvények egyetlen szálon is végrehajtásra kerülnek, és a műveletek feldolgozása egyszerre történik. Az entitásfüggvények azonban nem korlátozzák a végrehajtható kód típusát.

## <a name="concurrency-throttles"></a>Egyidejű fojtószelepek

Az Azure Functions támogatja több függvény egyidejű végrehajtása egyetlen alkalmazáspéldányon belül. Ez az egyidejű végrehajtás segít növelni a párhuzamosság, és minimálisra csökkenti a "hideg indítások", hogy egy tipikus alkalmazás fogja tapasztalni az idő múlásával. A magas egyidejűség azonban kimerítheti a virtuális gép rendszer erőforrásait, például a hálózati kapcsolatokat vagy a rendelkezésre álló memóriát. A függvényalkalmazás igényeitől függően szükség lehet a példányonkénti egyidejűség szabályozására, hogy elkerülje a memória nagy terhelésű helyzetekben való kifutásának lehetőségét.

A tevékenység-, orchestrator- és entitásfüggvény-egyidejűségi korlátok konfigurálhatók a **host.json** fájlban. A vonatkozó `durableTask/maxConcurrentActivityFunctions` beállítások a `durableTask/maxConcurrentOrchestratorFunctions` tevékenységfüggvények és mind az orchestrator és az entitás függvények.

### <a name="functions-20"></a>Funkciók 2.0

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

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

Az előző példában legfeljebb 10 orchestrator vagy entitás függvények és 10 tevékenységfüggvények futtatható egy virtuális gép egyidejűleg. Ha nincs megadva, az egyidejű tevékenység és az orchestrator vagy entitás függvény végrehajtások száma a virtuális gép magok számának 10-szerese.

> [!NOTE]
> Ezek a beállítások hasznosak a memória- és processzorhasználat egyetlen virtuális gépen történő kezeléséhez. Azonban több virtuális gép között horizontális felskálázott, minden virtuális gép saját korlátok készletével rendelkezik. Ezek a beállítások nem használhatók az egyidejűség globális szintű szabályozására.

## <a name="extended-sessions"></a>Bővített munkamenetek

A kiterjesztett munkamenetek olyan beállítás, amely az üzenetek feldolgozásának befejezése után is megtartja a vezényléseket és az entitásokat a memóriában. A kiterjesztett munkamenetek engedélyezésének tipikus hatása az Azure Storage-fiókkal szembeni I/O-kód és az általánosan jobb átviteli teljesítmény.

A bővített munkameneteket `durableTask/extendedSessionsEnabled` a `true` **host.json** fájlban való beállítással engedélyezheti. A `durableTask/extendedSessionIdleTimeoutInSeconds` beállítás sal szabályozhatja, hogy mennyi ideig legyen egy tétlen munkamenet a memóriában:

**Funkciók 2.0**
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

**1.0 függvények**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Ennek a beállításnak két lehetséges hátránya van, amelyeket figyelembe kell venni:

1. A függvényalkalmazás memóriahasználatának általános növekedése.
2. Az átviteli érték általánosan csökkenhet, ha sok egyidejű, rövid életű orchestrator vagy entitásfüggvény-végrehajtás van.

Például, ha `durableTask/extendedSessionIdleTimeoutInSeconds` 30 másodpercre van beállítva, akkor egy rövid életű orchestrator vagy entitás függvény epizód, amely kevesebb, mint 1 másodperc alatt végrehajtja, még mindig 30 másodpercig foglalja el a memóriát. A korábban `durableTask/maxConcurrentOrchestratorFunctions` említett kvótába is beleszámít, potenciálisan megakadályozva más orchestrator vagy entitás függvények futtatását.

A kiterjesztett munkamenetek vezénylési és entitásfüggvényekre gyakorolt konkrét hatásait a következő szakaszok ismertetik.

### <a name="orchestrator-function-replay"></a>Orchestrator függvény visszajátszása

Ahogy korábban említettük, az orchestrator függvények visszajátszása az Előzmények tábla tartalmával lesz **visszajátszva.** Alapértelmezés szerint az orchestrator függvénykód javunkra minden alkalommal, amikor egy köteg üzenetek várólistán egy vezérlő várólistából. Még akkor is, ha használja a fan-out, fan-in minta, és `Task.WhenAll` várja az `context.df.Task.all` összes feladatot, hogy teljes (például a .NET vagy JavaScript), nem lesznek visszajátszások, amelyek akkor fordulnak elő, mint kötegek feladat válaszok feldolgozása az idő múlásával. Ha a kiterjesztett munkamenetek engedélyezve vannak, az orchestrator függvénypéldányai hosszabb ideig tárolódnak a memóriában, és az új üzenetek teljes előzményismétlés nélkül is feldolgozhatók.

A hosszabb munkamenetek teljesítményjavulása leggyakrabban a következő helyzetekben figyelhető meg:

* Ha egyidejűleg korlátozott számú vezénylési példány fut.
* Ha a vezénylések nagy számú szekvenciális műveletet (például több száz tevékenységfüggvény-hívást) végeznek, amelyek gyorsan befejeződnek.
* Amikor vezénylések fan-out és fan-in számos olyan műveletek, amelyek teljes körülbelül ugyanabban az időben.
* Ha az orchestrator-függvényeknek nagy méretű üzeneteket kell feldolgozniuk, vagy processzorigényes adatfeldolgozást kell végeznie.

Minden más helyzetben általában nincs megfigyelhető teljesítményjavulás az orchestrator függvények.

> [!NOTE]
> Ezeket a beállításokat csak akkor szabad használni, ha az orchestrator függvényt teljesen kidolgozták és tesztelték. Az alapértelmezett agresszív visszajátszási viselkedés hasznos lehet az [orchestrator függvénykód-megkötések](durable-functions-code-constraints.md) megsértésének észleléséhez a fejlesztés időpontjában, ezért alapértelmezés szerint le van tiltva.

### <a name="entity-function-unloading"></a>Entitás függvény kiürítése

Az entitásfunkciók legfeljebb 20 műveletet dolgoznak fel egy kötegben. Amint egy entitás befejezi a műveletek kötegének feldolgozását, megőrzi állapotát, és eltávolítja a memóriából. A kiterjesztett munkamenetek beállításával késleltetheti az entitások memóriából való kiürítését. Az entitások továbbra is megőrzik állapotváltozásaikat, mint korábban, de a beállított ideig a memóriában maradnak az Azure Storage-ból érkező terhelések számának csökkentése érdekében. Az Azure Storage-ból származó terhelések csökkentése javíthatja a gyakran használt entitások teljes átviteli kapacitását.

## <a name="performance-targets"></a>Teljesítménycélok

Ha azt tervezi, hogy egy éles alkalmazás tartós függvényeit használja, fontos figyelembe venni a teljesítménykövetelményeket a tervezési folyamat korai szakaszában. Ez a szakasz néhány alapvető használati forgatókönyveket és a várható maximális átviteli számokat ismerteti.

* **Szekvenciális tevékenység végrehajtása:** Ez a forgatókönyv egy vezénylő függvényt ír le, amely tevékenységfüggvények sorozatát futtatja egymás után. Leginkább a [függvényláncolási](durable-functions-sequence.md) mintára hasonlít.
* **Párhuzamos tevékenység végrehajtása:** Ez a forgatókönyv egy orchestrator függvényt ír le, amely számos tevékenységfüggvényt hajt végre párhuzamosan a [Fan-out, Fan-in](durable-functions-cloud-backup.md) minta használatával.
* **Párhuzamos válasz feldolgozása:** Ez a forgatókönyv a második felében a [fan-out, Fan-in](durable-functions-cloud-backup.md) minta. A rajongó teljesítményére összpontosít. Fontos megjegyezni, hogy a fan-out, fan-in történik egyetlen orchestrator függvénypéldány, és ezért csak egyetlen virtuális gépen futtatható.
* **Külső esemény feldolgozása:** Ez a forgatókönyv egy olyan orchestrator függvénypéldányt jelöl, amely egyenként várakozik a [külső eseményekre.](durable-functions-external-events.md)
* **Entitásművelet feldolgozása**: Ez a forgatókönyv azt vizsgálja, hogy _egy_ [számláló entitás](durable-functions-entities.md) milyen gyorsan tudja feldolgozni a műveletek állandó adatfolyamát.

> [!TIP]
> A ventilátorokkal ellentétben a ventilátoros műveletek egyetlen virtuális gépre korlátozódnak. Ha az alkalmazás használja a fan-out, fan-in mintát, és aggódik a fan-in teljesítmény, fontolja meg a tevékenység funkció fan-out több [al-vezénylések](durable-functions-sub-orchestrations.md).

Az alábbi táblázat a korábban leírt esetek várható *maximális* átviteli számát mutatja be. "Példány" egy egyetlen példányegy orchestrator függvény egyetlen kis ([A1](../../virtual-machines/sizes-previous-gen.md)) virtuális gép az Azure App Service-ben futó egyetlen példányát. Minden esetben azt feltételezzük, hogy a [kiterjesztett munkamenetek](#orchestrator-function-replay) engedélyezve vannak. A tényleges eredmények a cpu- vagy I/O-munka függvényében változhatnak.

| Forgatókönyv | Maximális átviteli sebesség |
|-|-|
| Szekvenciális tevékenység végrehajtása | 5 tevékenység másodpercenként, példányonként |
| Párhuzamos tevékenység végrehajtása (fan-out) | 100 tevékenység másodpercenként, példányonként |
| Párhuzamos válaszfeldolgozás (ventilátor) | 150 válasz másodpercenként, példányonként |
| Külső esemény feldolgozása | 50 esemény másodpercenként, példányonként |
| Entitásművelet feldolgozása | 64 művelet másodpercenként |

> [!NOTE]
> Ezek a számok a Durable Functions bővítmény 1.4.0-s (GA) kiadásának aktuálisak. Ezek a számok idővel változhatnak, ahogy a funkció érlelődik, és az optimalizálás történik.

Ha nem látja a várt átviteli átviteli számokat, és a processzor- és memóriahasználat kifogástalan állapotúnak tűnik, ellenőrizze, hogy az ok a [tárfiók állapotához](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)kapcsolódik-e. A Durable Functions bővítmény jelentős terhelést okozhat egy Azure Storage-fiókban, és a megfelelően nagy terhelés ek a tárfiók szabályozását eredményezhetik.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a katasztrófa utáni helyreállításról és a földrajzi elosztásról](durable-functions-disaster-recovery-geo-distribution.md)
