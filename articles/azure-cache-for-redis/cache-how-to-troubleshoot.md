---
title: Hogyan háríthatók el az Azure Cache redis |} A Microsoft Docs
description: Ismerje meg az Azure Cache kapcsolatos gyakori problémák megoldása a redis.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: 154f5200872dbc06550f396717cb215f3db4f7dd
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199578"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Hogyan háríthatók el az Azure Cache redis
Ez a cikk nyújt útmutatást a következő kategóriákba tartozó Azure Cache Redis-hibák elhárítása.

* [Ügyféloldali hibaelhárítási](#client-side-troubleshooting) – Ez a szakasz hasznos útmutatást ad az azonosító és az alkalmazás csatlakoztatása az Azure Cache a Redis által okozott problémák elhárításához.
* [Kiszolgálóoldali hibaelhárítási](#server-side-troubleshooting) – Ez a szakasz hasznos útmutatást ad az azonosító és az Azure Cache a Redis-kiszolgáló oldalán a okozta problémák elhárításához.
* [StackExchange.Redis időtúllépési kivételeket](#stackexchangeredis-timeout-exceptions) – Ez a szakasz információt nyújt a problémák elhárítása a StackExchange.Redis ügyfél használata esetén.

> [!NOTE]
> A hibaelhárítási lépéseket ebben az útmutatóban számos Redis parancsok futtatása és figyelése a korábbi teljesítménymutatók utasításokat tartalmaz. További információk és útmutatás, lásd: a cikkek a [további információkat](#additional-information) szakaszban.
> 
> 

## <a name="client-side-troubleshooting"></a>Ügyféloldali hibáinak elhárítása
Ez a szakasz bemutatja az ügyfélalkalmazásban feltétel miatt előforduló problémák elhárítása.

* [Rendelkezésre álló memória mennyisége az ügyfélen](#memory-pressure-on-the-client)
* [Az adatforgalom hirtelen](#burst-of-traffic)
* [Nagyszámú ügyféllel CPU-használat](#high-client-cpu-usage)
* [Ügyféloldali sávszélesség túllépve](#client-side-bandwidth-exceeded)
* [Nagy méretű kérelem/válasz mérete](#large-requestresponse-size)
* [Mi történt a redis adataimat?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Rendelkezésre álló memória mennyisége az ügyfélen
#### <a name="problem"></a>Probléma
Rendelkezésre álló memória mennyisége az ügyfélszámítógépen teljesítményproblémákat okozhat, amelyek késleltetheti-e a feldolgozási késedelem nélkül a Redis-példány által küldött adatok különféle vezet. Találatok száma a rendelkezésre álló memória mennyisége, amikor a rendszer általában rendelkezik lap adatokat a fizikai memória a virtuális memória, amely a lemezen van megvalósítva. Ez *lap hibát okozó* jelentősen lelassíthatja a rendszer.

#### <a name="measurement"></a>Mérés
1. Gépen, győződjön meg arról, hogy ne haladják rendelkezésre álló memória, a memóriahasználat figyelésére. 
2. A figyelő a `Page Faults/Sec` teljesítményadatait mutatja. Legtöbb rendszer rendelkezik néhány oldalhibák még a normál működés során, ezért tekintse meg ezt oldal hibák teljesítményszámláló kiugrások, ami időtúllépéseket megfeleljen a.

#### <a name="resolution"></a>Megoldás:
Az ügyfél frissítése egy nagyobb ügyfélnek több memóriával rendelkező virtuális gép méretét, vagy a memória használati mintákat memóriafogyasztás csökkentése érdekében be dig.

### <a name="burst-of-traffic"></a>Az adatforgalom hirtelen
#### <a name="problem"></a>Probléma
A forgalom adatlöketekkel kombinálva gyenge `ThreadPool` beállításai már a Redis-kiszolgáló által küldött, de még nem használják az ügyféloldali adatok feldolgozása az késleltetések eredményezhet.

#### <a name="measurement"></a>Mérés
A figyelő hogyan a `ThreadPool` statisztika kóddal idővel változni [ehhez hasonló](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Ezenkívül megnézheti a `TimeoutException` StackExchange.Redis üzenetét. Például:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Az előző üzenetben számos érdekes problémák vannak:

1. Figyelje meg, hogy az a `IOCP` szakaszban, és a `WORKER` szakaszban van egy `Busy` tulajdonság értéke nagyobb, mint a `Min` értéket. Ez a különbség azt jelenti, hogy a `ThreadPool` beállításait kell módosítani.
2. Emellett megtekintheti `in: 64221`. Ez azt jelzi, hogy 64,211 bájt a kernel szoftvercsatorna-réteg nem érkezett, de még nem még olvasni az alkalmazás (például a StackExchange.Redis). Ez a különbség általában azt jelenti, hogy az alkalmazás nem adatok olvasása a hálózatról gyorsan a kiszolgálón van elküldené azt.

#### <a name="resolution"></a>Megoldás:
Konfigurálja a [szálkészlet beállítások](https://gist.github.com/JonCole/e65411214030f0d823cb) győződjön meg arról, hogy a szálkészlet felskálázással alatt gyorsan átmenetileg megnövelhető a forgatókönyveket.

### <a name="high-client-cpu-usage"></a>Nagyszámú ügyféllel CPU-használat
#### <a name="problem"></a>Probléma
Magas CPU-használat az ügyfélen utal, hogy a rendszer nem tud lépést tartani a munkát, felkérték végrehajtásához. Ez a helyzet, az azt jelenti, hogy az ügyfél lehet, hogy nem tudja feldolgozni a Redis válaszára időben feldolgozza annak ellenére, hogy a Redis gyorsan a választ küldött.

#### <a name="measurement"></a>Mérés
A rendszer széles processzorhasználat figyelése az Azure Portal vagy a kapcsolódó teljesítményszámlálóhoz. Ügyeljen arra, hogy nem figyelheti *folyamat* mivel egyetlen folyamat CPU-használat alacsony egyszerre ideje, hogy a rendszer általános CPU Processzor magas lehet. Tekintse meg a CPU-használat, amelyek megfelelnek az időtúllépések kiugrások. Magas CPU eredményeként is megjelenhetnek magas `in: XXX` lévő értékeknek `TimeoutException` hibaüzenetek leírtak szerint a [az adatforgalom hirtelen](#burst-of-traffic) szakaszban.

> [!NOTE]
> StackExchange.Redis 1.1.603, és később tartalmazza a `local-cpu` a metrika `TimeoutException` hibaüzenetek. Ügyeljen arra, hogy a legújabb verzióját használja a [StackExchange.Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/). Nincsenek a kódot, még robusztusabbá időtúllépések, ezért fontos a legújabb verzióra kellene folyamatosan javított hibák.
> 
> 

#### <a name="resolution"></a>Megoldás:
Egy nagyobb méretű virtuális gép több Processzor kapacitása használjon, vagy vizsgálja meg, mi okozza a CPU adatforgalmi csúcsokhoz. 

### <a name="client-side-bandwidth-exceeded"></a>Ügyféloldali sávszélesség túllépve
#### <a name="problem"></a>Probléma
Az ügyfélgépek architektúrájának függően lehet a korlátozások a mekkora hálózati sávszélességre van érhető el. Ha az ügyfél hálózati kapacitás túlterhelés által meghaladja a rendelkezésre álló sávszélességet, majd adatok nem lett feldolgozva az ügyféloldalon gyorsan, ahogy az a kiszolgáló küld. Ez a helyzet időtúllépéseket eredményezhet.

#### <a name="measurement"></a>Mérés
Hogyan a sávszélesség-használat a kóddal időbeli változásait figyelni [ehhez hasonló](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ez a kód nem lehetséges, hogy sikeresen lefutott bizonyos környezetekben (például az Azure-webhelyeken) a korlátozott engedélyekkel.

#### <a name="resolution"></a>Megoldás:
Ügyfél virtuális gép méretének növeléséhez, vagy csökkentse a hálózati sávszélesség-használatot.

### <a name="large-requestresponse-size"></a>Nagy méretű kérelem/válasz mérete
#### <a name="problem"></a>Probléma
Egy nagy kérés/válasz időtúllépések okozhat. Például tegyük fel, hogy az időkorlát az ügyfélen beállított értéke 1 másodperc. Az alkalmazás két kulcs (például "A" és "B") kérelmek egyszerre (ugyanazt a fizikai hálózati kapcsolatot használja). A legtöbb ügyfél támogatja a "Pipelining" kérések úgy, hogy mind a kérelmekre, "A" és "B" nélkül küldi el a kiszolgálóra, egy hálózati egymás után várakozás a válaszra. A kiszolgáló a válaszok küld vissza az ugyanabban a sorrendben. Ha "A" válasz elég nagy, azt leggyakrabban az időtúllépés további kérelmeknél is étkezési. 

A következő példa bemutatja az ebben a forgatókönyvben. Ebben a forgatókönyvben "A" és "B" kérelem gyorsan küldik, elindul a kiszolgáló gyors küldése válaszok "A" és "B", de miatt adatátviteli idők, "B" elakadnak a kérelem és időtúllépés történik mögött annak ellenére, hogy a kiszolgáló válasza gyorsan.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mérés
Ez a kérelem/válasz, adott nehéz méréséhez. Alapvetően kell alakítsa ki az Ügyfélkód nagyméretű kérelmek és válaszok nyomon követéséhez. 

#### <a name="resolution"></a>Megoldás:
1. A redis nagy számú kis-értékek ahelyett, hogy néhány nagy értékeket van optimalizálva. Az előnyben részesített megoldás, ha a kapcsolódó a kisebb értékek az adatokat. Tekintse meg a [Mi az az ideális érték mérettartománya redis? Túl nagy érték 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) további információ a Miért ajánlott a kisebb értékek közzététele.
2. Magasabb sávszélesség képességeket, csökkentve a nagyobb válaszok adatátviteli idők beolvasásához (az ügyfél és az Azure Cache Redis-kiszolgáló), a virtuális gép méretének növeléséhez. További sávszélesség csak a kiszolgálón, vagy csak az első az ügyfél nem feltétlenül elegendő. A sávszélesség-használat mérésére, és hasonlítsa össze a virtuális gép jelenlegi mérete képességeit.
3. Ha több `ConnectionMultiplexer` -objektumok használata és ciklikus időszeletelési kérelmek különböző kapcsolatokon keresztül.

### <a name="what-happened-to-my-data-in-redis"></a>Mi történt a redis adataimat?
#### <a name="problem"></a>Probléma
Az egyes várt az adatokat a Redis-példányt az Azure Cache-ben, nem verziónk, de nincs.

#### <a name="resolution"></a>Megoldás:
Lásd: [Mi történt a redis adataimat?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) a lehetséges okokért és megoldásokért.

## <a name="server-side-troubleshooting"></a>Kiszolgáló oldalán hibáinak elhárítása
Ez a szakasz ismerteti a gyorsítótár-kiszolgálón feltétel miatt előforduló problémák elhárítása.

* [Rendelkezésre álló memória mennyisége a kiszolgálón](#memory-pressure-on-the-server)
* [Magas CPU-használat / kiszolgáló terhelése](#high-cpu-usage-server-load)
* [Túllépte a kiszolgáló oldalán sávszélesség](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Rendelkezésre álló memória mennyisége a kiszolgálón
#### <a name="problem"></a>Probléma
Rendelkezésre álló memória mennyisége a kiszolgáló oldalán teljesítményproblémák késleltetheti-e a feldolgozási kérelmek különféle vezet. Találatok száma a rendelkezésre álló memória mennyisége, amikor a rendszer általában rendelkezik lap adatokat a fizikai memória a virtuális memória, amely a lemezen van megvalósítva. Ez *lap hibát okozó* jelentősen lelassíthatja a rendszer. Van a rendelkezésre álló memória mennyisége lehetséges okai: 

1. A gyorsítótár teljes kapacitás megadta az adatokat. 
2. A redis kapja a magas memória töredezettségét – általában az okozza, nagy objektumok tárolásával (Redis optimalizált egy kis méretű objektumok – Lásd: a [Mi az az ideális érték mérettartománya redis? Túl nagy érték 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) közzététele részletekért). 

#### <a name="measurement"></a>Mérés
A redis mutatja a két mérőszám, amely segít azonosítani a probléma. Az első `used_memory` és más `used_memory_rss`. [Ezek a metrikák](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) érhetők el az Azure Portalon vagy az a [Redis INFO](https://redis.io/commands/info) parancsot.

#### <a name="resolution"></a>Megoldás:
Van több lehetséges hogy végzett módosítások is annak érdekében, hogy kifogástalan memóriahasználat:

1. [A memória-házirend konfigurálása](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) és a kulcsok lejárati idejének beállítására. Ez a konfiguráció nem minden esetben, ha töredezettsége.
2. [A maxmemory-szolgáltatás számára fenntartott érték](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ez elég nagy legyen a memória töredezettségét kompenzálja.
3. Bontsa fel a nagy gyorsítótárazott objektumot kisebb kapcsolódó objektumok.
4. [Méretezési csoport](cache-how-to-scale.md) egy nagyobb gyorsítótár méretét.
5. Ha használ olyan [prémium szintű gyorsítótár engedélyezve van a Redis-fürttel](cache-how-to-premium-clustering.md), is [szegmensei számának növelése](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Magas CPU-használat / kiszolgáló terhelése
#### <a name="problem"></a>Probléma
Magas CPU-használat is jelenti, hogy az ügyfél oldalán Redis válaszára időben feldolgozni, annak ellenére, hogy a Redis gyorsan a választ küldött sikertelen lehet.

#### <a name="measurement"></a>Mérés
A rendszer széles processzorhasználat figyelése az Azure Portal vagy a kapcsolódó teljesítményszámlálóhoz. Ügyeljen arra, hogy nem figyelheti *folyamat* mivel egyetlen folyamat CPU-használat alacsony egyszerre ideje, hogy a rendszer általános CPU Processzor magas lehet. Tekintse meg a CPU-használat, amelyek megfelelnek az időtúllépések kiugrások.

#### <a name="resolution"></a>Megoldás:
* Tekintse át a javaslatát, és riasztásokat az említett a [Azure Cache redis Cache Advisor](cache-configure.md#azure-cache-for-redis-advisor).
* Emellett tekintse át az ebben a témakörben a többi javaslat és [ajánlott eljárások az Azure redis Cache](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) megtekintheti, ha Ön rendelkezik alkalmazott tovább optimalizálhatja a gyorsítótár és az ügyfél az összes beállítás. 
* Tekintse át a [Azure Cache a Redis-teljesítmény](cache-faq.md#azure-cache-for-redis-performance) diagramok és láthatja, ha lehetséges, hogy a jelenlegi csomag a felső küszöbértékek közelében. Ha szükséges, [méretezési](cache-how-to-scale.md) további CPU-kapacitás nagyobb gyorsítótár szintre. A prémium szintű már használ, előfordulhat, hogy szeretné-e a [horizontális felskálázás a fürtszolgáltatás](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Túllépte a kiszolgáló oldalán sávszélesség
#### <a name="problem"></a>Probléma
A cache-példány méretétől függően lehet a korlátozások a mekkora hálózati sávszélességre van érhető el. Ha a kiszolgáló meghaladja a rendelkezésre álló sávszélességet, majd nem történik adatküldés gyorsan, az ügyfél számára. Ez a helyzet időtúllépéseket eredményezhet.

#### <a name="measurement"></a>Mérés
Nyomon követheti a `Cache Read` mérőszám, amely adatok mennyisége (MB) a gyorsítótárból a megadott jelentési időszak során (MB/s) másodpercenként olvasása. Ez az érték megfelel a gyorsítótár által használt hálózati sávszélesség. Ha szeretné a kiszolgálóoldali hálózati sávszélesség korlátja riasztásokat állíthat be, létrehozhatja őket ez `Cache Read` számlálót. Az értékek az értékek összehasonlítása [Ez a táblázat](cache-faq.md#cache-performance) tarifacsomagok és méretek különböző gyorsítótár megfigyelt sávszélesség korlátait.

#### <a name="resolution"></a>Megoldás:
Megközelítette egységesen a megfigyelt maximális sávszélesség a az árképzési szint és a gyorsítótár méretét, érdemes lehet [skálázás](cache-how-to-scale.md) tarifacsomag vagy méret, amely rendelkezik a nagyobb hálózati sávszélességet, hogy szereplő értékek használatával [Ez a táblázat](cache-faq.md#cache-performance)alapján.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis időtúllépési kivételek
StackExchange.Redis konfigurációs beállítás nevesített használ `synctimeout` a szinkron műveletek, amelyek rendelkeznek 1000 ms, az alapértelmezett érték. Ha egy synchronního volání nem fejezi be a meghatározott idő alatt, a StackExchange.Redis ügyfél jelez az alábbi példához hasonló időtúllépési hiba:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Ez a hibaüzenet, amely segíthet a pont, a probléma okát és lehetséges megoldási metrikákat tartalmazza. Az alábbi táblázat tartalmazza a hiba üzenet metrikák részleteit.

| Hiba történt a üzenet metrika | Részletek |
| --- | --- |
| h |Az utolsó időszeletben: 0 parancsokat kiadva. |
| kezelője |A szoftvercsatorna-kezelő végez `socket.select`, ami azt jelenti, hogy az operációs rendszer jelzi, hogy; rendelkező szoftvercsatorna alapvetően: az olvasó nem aktív olvasó a hálózatról, mert ez nem úgy gondolja, hogy bármilyen formában van |
| üzenetsor |Nincsenek folyamatban lévő műveletek 73 összesen |
| l |a folyamatban lévő műveletek 6 az el nem küldött üzenetsorban, és még nem szerepelnek a kimenő hálózati |
| QS |67-es, a folyamatban lévő műveletek a kiszolgálóra küldött, de a választ még nem érhető el. A válasz lehet `Not yet sent by the server` vagy `sent by the server but not yet processed by the client.` |
| qc |a folyamatban lévő műveletek 0 válaszok láthatta, de van még nem jelölték meg vár a befejezési ciklus miatt befejezettként |
| wR |Van egy aktív írási (ami azt jelenti, a 6 el nem küldött kérelmek nem mellőzve) bájtok/activewriters |
| ebben: |Nincs aktív olvasó és nulla bájtot a hálózati bájtok/activereaders az olvasni kívánt érhető el |

### <a name="steps-to-investigate"></a>Vizsgálja meg a lépéseket
1. Ajánlott eljárás, hogy, mert a következő mintát használ csatlakozhassanak, ha a StackExchange.Redis ügyfelet használja.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ````

    További információkért lásd: [csatlakozás a gyorsítótárhoz, használja a StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Győződjön meg arról, hogy az Azure Cache redis és az ügyfélalkalmazást Azure-ban ugyanabban a régióban. Például, akkor előfordulhat, hogy kell első időtúllépések, amennyiben a gyorsítótár az USA keleti régiójában, de az ügyfél az USA nyugati RÉGIÓJA és a kérés belül nem fejeződik be a `synctimeout` időköz, vagy előfordulhat, hogy lehet első időtúllépések helyi fejlesztői gépen a hibakeresés során. 
   
    Erősen javasoljuk, hogy rendelkezik a gyorsítótár és az ügyfél azonos Azure-régióban. Ha egy forgatókönyvet, amely tartalmazza a régiók közötti hívások, kell beállítania a `synctimeout` időköz nagyobb, mint az alapértelmezett 1000 ms gyakoriság együtt értékre egy `synctimeout` tulajdonság a kapcsolati karakterláncban. Az alábbi példa bemutatja a Redis kapcsolati karakterlánc kódrészlet az StackExchange.Azure gyorsítótár egy `synctimeout` 2000 MS.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Ügyeljen arra, hogy a legújabb verzióját használja a [StackExchange.Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/). Nincsenek a kódot, még robusztusabbá időtúllépések, ezért fontos a legújabb verzióra kellene folyamatosan javított hibák.
3. Ha sávszélesség-korlátozásokat a kiszolgálón vagy az ügyfél által első kötött kérelmeket, hosszabb ideig tart, végezze el, és ezáltal a időtúllépéseket okoz. Ha az időkorlát miatt a kiszolgáló hálózati sávszélesség van-e, olvassa el [túllépte a kiszolgáló oldalán sávszélesség](#server-side-bandwidth-exceeded). Az időtúllépési ügyfél a hálózati sávszélesség-e, olvassa el [túllépte az ügyféloldali sávszélesség](#client-side-bandwidth-exceeded).
4. Ön CPU első kötött a kiszolgálón vagy az ügyfél?
   
   * Ha Ön első nézve kötelezőnek ismeri CPU az ügyfélen, amelyek miatt a kérelem feldolgozása nem lehetséges belül ellenőrizzük a `synctimeout` időköz, ami az időtúllépés miatt. Egy ügyfél nagyobb méretű áthelyezése vagy elosztja a terhelést segítségével szabályozhatja a probléma. 
   * Ellenőrzés, ha a CPU-figyelési szolgáltatás által a kiszolgálón kötve. a `CPU` [gyorsítótárazzák a teljesítmény-mérőszám](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). CPU kötött pedig a Redis érkező kérések okozhat, ezeket a kérelmeket időkorlátja. Oldja meg ezt az állapotot, a prémium szintű gyorsítótár több szegmens között oszthatja el a terhelést, vagy váltson egy nagyobb méretű vagy tarifacsomagot. További információkért lásd: [kiszolgáló oldalán a sávszélesség túllépte](#server-side-bandwidth-exceeded).
5. Vannak-e a kiszolgálón feldolgozása hosszú időt vesz igénybe parancsok? Hosszú ideig futó parancsok, amelyek a redis-kiszolgáló feldolgozása hosszú ideig tart az időtúllépések okozhat. Néhány példa a hosszú ideig futó parancsokat `mget` kulcsok, nagy számú `keys *` vagy rosszul írt lua-szkriptek. Csatlakozás az Azure Cache a Redis-példányt a redis-cli ügyfél használatával, vagy használja a [Redis konzol](cache-configure.md#redis-console) , és futtassa a [SlowLog](https://redis.io/commands/slowlog) paranccsal tekintheti meg, hogy vannak-e kérések a vártnál tovább tart. Redis-kiszolgáló és a StackExchange.Redis kevesebb nagy kérelmeket, hanem sok kisebb kérelem vannak optimalizálva. Az adatok felosztása szeletekre javíthatja a dolgok itt. 
   
    Az Azure Cache Redis SSL-végpont használatával a redis-cli és a stunnel csatlakozásról információkért lásd: a [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis az előzetes kiadásban](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogbejegyzést. További információkért lásd: [SlowLog](https://redis.io/commands/slowlog).
6. Magas Redis-kiszolgáló terhelése okozhatja időtúllépések. A kiszolgáló terhelése figyelemmel figyelése a `Redis Server Load` [gyorsítótárazzák a teljesítmény-mérőszám](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). A kiszolgáló terhelését, 100 (maximális értéke) azt jelzi, hogy, a redis-kiszolgáló már foglalt, az üresjárati idővel, a kérelmek feldolgozásához. Ha bizonyos kérelmek igényelnek a kiszolgálói funkció az összes megtekintéséhez futtassa a SlowLog parancs az előző bekezdésben ismertetett módon. További információkért lásd: [magas CPU-használat / kiszolgáló terhelése](#high-cpu-usage-server-load).
7. Történt más esemény, amely egy hálózati blip-oka lehetett az ügyféloldalon? Ellenőrizze az ügyfélen (webes, feldolgozói szerepkör vagy egy IaaS-beli virtuális Gépen), ha olyan esemény, például az ügyfél-példányok számának méretezése felfelé vagy lefelé történt, vagy az ügyfél vagy az automatikus méretezés új verziójának telepítése engedélyezve van? A tesztelés található, az automatikus méretezés, vagy a vertikális felskálázásával, vagy le is OK kimenő hálózati kapcsolat elveszhet néhány másodpercig. StackExchange.Redis kódot képes legyen ellenállni a ilyen események és újracsatlakozik. Ebben az időszakban az újracsatlakozás bármilyen kérelmeket az üzenetsorban is időtúllépés.
8. Történt megelőző több kis kérést az Azure Cache redis túllépte az időkorlátot, big Data típusú kérelmet? A paraméter `qs` a hibás üzenet közli Önnel, hogy hány kérésnek az ügyfélről a kiszolgálónak küldött, de még nem dolgozott választ. Ezt az értéket is folyamatosan növekvő, mert StackExchange.Redis egyetlen TCP-kapcsolatot használ, és egyszerre csak egy választ csak olvasható. Annak ellenére, hogy az első művelet túllépte az időkorlátot, nem állítja le az adatokat küld a rendszer és-tárolókról a kiszolgálón, és más kérelmek le vannak tiltva, amíg a nagy méretű kérelem nem fejeződött időtúllépéssel okozza. Egy megoldás, hogy időtúllépések esélyét annak biztosítása, hogy a gyorsítótár mérete elegendő a számítási feladatok és a nagy értékek felosztása szeletekre. Egy másik lehetséges megoldás az, hogy a készlet használata `ConnectionMultiplexer` az ügyfél az objektumok, és válassza a legkevésbé betöltött `ConnectionMultiplexer` új kérelem küldése során. Ezzel akadályozhatja meg egyetlen időtúllépés küldött egyéb kérések számára is időtúllépés miatt.
9. Ha használ `RedisSessionStateProvider`, ellenőrizze az újrapróbálkozási időkorlátnak az újrapróbálkozási megfelelően. `retryTimeoutInMilliseconds` érdemes lehet magasabb, mint `operationTimeoutInMilliseconds`, ellenkező esetben fordulhat elő, nem az újrapróbálkozásokat. Az alábbi példában `retryTimeoutInMilliseconds` 3000 értékre van állítva. További információkért lásd: [ASP.NET munkamenetállapot-szolgáltatóját Azure Cache redis](cache-aspnet-session-state-provider.md) és [munkamenetállapot-szolgáltató és a kimeneti gyorsítótár-szolgáltatóját a konfigurációs paraméterek használata](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. Ellenőrizze az Azure Cache a Redis-kiszolgáló által a memóriahasználat [figyelési](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` és `Used Memory`. Ha egy kiürítési szabályzatot van beállítva, a Redis elindul kizárásának kulcsokat mikor `Used_Memory` eléri a gyorsítótár méretét. Ideális esetben `Used Memory RSS` kell lennie csak valamivel nagyobb `Used memory`. A nagy különbség az jelenti, hogy a memória töredezettségét (belső vagy külső). Amikor `Used Memory RSS` van kevesebb, mint `Used Memory`, azt jelenti, hogy a gyorsítótár-memória része a felcserélés van lett az operációs rendszer által. A csere történik, ha várhatóan néhány jelentős késéseket. Mivel a Redis nem szabályozhatják, hogyan vannak leképezve a hozzárendelések memórialapok, magas `Used Memory RSS` Ez gyakran a memóriahasználat ugrásszerű eredménye. Ha Redis felszabadítja a memória, a memóriát kap vissza a foglaló, és előfordulhat, hogy a foglaló, vagy előfordulhat, hogy nem biztosít a memória vissza a rendszer. Előfordulhat, hogy egy között eltérés van a `Used Memory` érték és a memória-felhasználás az operációs rendszer által jelentett módon. Ez lehet amiatt memória használt és megtörtént, amely a Redis által, de nem adott vissza a rendszer. Memóriahiba csökkentése érdekében hajtsa végre az alábbi lépéseket:
   
   * Frissítse a gyorsítótár egy nagyobb méretű, hogy nem futtat személyzetet memóriahasználatra vonatkozó korlátozásai a rendszer.
   * A kulcsok lejárati idejének beállítására, hogy a régebbi értékek proaktív módon ki vannak zárva.
   * A figyelő a `used_memory_rss` metrika gyorsítótárazni. Ha ez az érték megközelíti a gyorsítótár méretét, akkor valószínűleg azokat a teljesítménnyel kapcsolatos problémák. Ha prémium szintű gyorsítótár használata, vagy frissítsen egy nagyobb méretű gyorsítótár mérete, ossza el az adatokat több szegmens között.
   
   További információkért lásd: [rendelkezésre álló memória mennyisége a kiszolgálón](#memory-pressure-on-the-server).

## <a name="additional-information"></a>További információ
* [Milyen Azure Cache a Redis-ajánlatot és -méretet használjam?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Hogyan lehet becslésére és a gyorsítótár teljesítményének tesztelése?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Hogyan futtathatok Redis parancsok?](cache-faq.md#how-can-i-run-redis-commands)
* [A Redis Azure Cache figyelése](cache-how-to-monitor.md)

