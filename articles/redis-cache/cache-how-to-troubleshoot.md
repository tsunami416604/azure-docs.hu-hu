---
title: "Azure Redis Cache hibaelhárítása |} Microsoft Docs"
description: "Útmutató az Azure Redis Cache gyakori problémák megoldásában."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: e5f6f423697d90e889ebde2cd203891e34278b3c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Azure Redis Cache hibaelhárítása
Ez a cikk a következő kategóriákba tartozó Azure Redis Cache problémák iránymutatásokat tartalmaz.

* [Ügyféloldali hibaelhárítási](#client-side-troubleshooting) – Ez a szakasz útmutatást nyújt azonosító és az alkalmazás csatlakoztatása az Azure Redis Cache által okozott problémák elhárításához.
* [Kiszolgálóoldali hibaelhárítási](#server-side-troubleshooting) – Ez a szakasz útmutatást nyújt azonosító és az Azure Redis Cache kiszolgálóoldalon okozott problémák megoldása.
* [Időtúllépési kivételek StackExchange.Redis](#stackexchangeredis-timeout-exceptions) – Ez a szakasz tájékoztatást nyújt a StackExchange.Redis-ügyféllel kapcsolatos hibák elhárítása.

> [!NOTE]
> Ez az útmutató a hibaelhárítási lépéseket számos tartalmaz a Redis-parancsok futtathatja és figyelheti a teljesítménymutatók vonatkozó utasításokat. További információt és útmutatást lásd: a cikkek a [további információt](#additional-information) szakasz.
> 
> 

## <a name="client-side-troubleshooting"></a>Ügyféloldali hibaelhárítása
Ez a szakasz ismerteti, amelyek a hibaelhárítási problémák, amelyek miatt az ügyfélalkalmazás vonatkozó feltétellel.

* [Memóriaprobléma az ügyfélen](#memory-pressure-on-the-client)
* [A forgalom kapacitásnövelés](#burst-of-traffic)
* [Magas ügyfél CPU-használat](#high-client-cpu-usage)
* [Client-Side Bandwidth Exceeded](#client-side-bandwidth-exceeded)
* [Nagy méretű kérelem/válasz mérete](#large-requestresponse-size)
* [Mi történt a Redis-adataimhoz?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Memóriaprobléma az ügyfélen
#### <a name="problem"></a>Probléma
Az ügyfélszámítógépen Memóriaterhelést teljesítménnyel kapcsolatos problémákat, amelyek késleltetheti-e a Redis-példány késedelem nélkül által küldött adatok feldolgozása különböző vezet. Memóriaprobléma találatok, amikor a rendszer általában ki lap adatokat a fizikai memória, a virtuális memória, amely a lemezen. Ez *lap hibás* jelentősen lassítsa le a rendszer.

#### <a name="measurement"></a>Mérési
1. Győződjön meg arról, hogy azt nem haladja meg a rendelkezésre álló memória gép figyelése. 
2. A figyelő a `Page Faults/Sec` teljesítményszámláló. Rendszerek többsége néhány laphibák normál működés során, akkor is, a teljesítményt a lapon hibák teljesítményszámláló időtúllépések, amely így figyelendő.

#### <a name="resolution"></a>Megoldás:
Az ügyfél frissítése a Virtuálisgép-méretet több memória nagyobb ügyfél számára, vagy a memória-felhasználás csökkentése érdekében a memória használati minták című szakasszal.

### <a name="burst-of-traffic"></a>A forgalom kapacitásnövelés
#### <a name="problem"></a>Probléma
A forgalom felszakadásáig gyenge együtt `ThreadPool` beállítások adatfeldolgozás már a Redis-kiszolgáló által küldött, de még nem használt ügyféloldali késést okozhat.

#### <a name="measurement"></a>Mérési
A figyelő hogyan a `ThreadPool` statisztika kóddal időbeli változását [, például a](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Is megtalálhatja a `TimeoutException` StackExchange.Redis üzenetét. Például:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

A fenti üzenet van több problémákra, amelyek a érdekes:

1. Figyelje meg, hogy az a `IOCP` szakasz és a `WORKER` szakasz rendelkezik egy `Busy` érték, amely nagyobb, mint a `Min` érték. Ezt a különbséget azt jelenti, hogy a `ThreadPool` beállításait kell hangolását.
2. Azt is láthatja, `in: 64221`. Ez azt jelzi, hogy 64,211 bájt a kernel szoftvercsatorna-réteg nem érkezett, de az alkalmazás (például StackExchange.Redis) még nem még olvasni. Ezt a különbséget általában azt jelenti, hogy az alkalmazás nem adatok beolvasása a hálózati gyorsan, ahogy az a kiszolgáló küld az Ön számára.

#### <a name="resolution"></a>Megoldás:
Konfigurálja a [ThreadPool beállítások](https://gist.github.com/JonCole/e65411214030f0d823cb) győződjön meg arról, hogy a szálkészlet rendkívül gyorsan a kapacitásnövelés a forgatókönyveket.

### <a name="high-client-cpu-usage"></a>Magas ügyfél CPU-használat
#### <a name="problem"></a>Probléma
Az ügyfél magas CPU-használat utal, hogy a rendszer nem tud lépést tartani, amelyek elvégzéséhez kérték munkájába. Ez a helyzet következménye, hogy az ügyfél Redis válaszára időben feldolgozni, annak ellenére, hogy a Redis gyorsan a választ küldött.

#### <a name="measurement"></a>Mérési
A rendszer nagy CPU-használat a társított teljesítményszámláló vagy az Azure portálon keresztül figyelheti. Ügyeljen arra, hogy figyelje *folyamat* mivel egyetlen folyamat CPU-használat alacsony lehet egy időben, hogy a rendszer általános CPU Processzor magas lehet. A CPU-használat teljesítményt, amelyek megfelelnek a időtúllépések figyelje. Magas CPU miatt is találkozhat magas `in: XXX` értékei `TimeoutException` hibaüzenet leírtak szerint a [forgalom kapacitásnövelés](#burst-of-traffic) szakasz.

> [!NOTE]
> StackExchange.Redis 1.1.603, és később tartalmazza a `local-cpu` a metrika `TimeoutException` hibaüzenetek. Győződjön meg arról, hogy a legújabb verzióját használja a [StackExchange.Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/). Nincsenek abba, hogy sokkal hatékonyabban időtúllépések számára a legújabb verzióra, akkor fontos, a kódban folyamatosan javított hibák.
> 
> 

#### <a name="resolution"></a>Megoldás:
Használjon további CPU-kapacitás nagyobb Virtuálisgép-méretet, vagy vizsgálja meg, mi okozza a CPU teljesítményt. 

### <a name="client-side-bandwidth-exceeded"></a>Túllépte az ügyféloldali sávszélesség
#### <a name="problem"></a>Probléma
Attól függően, hogy az ügyfélgépek architektúrája, hogy korlátozások mekkora hálózati sávszélességre gyakorolt rendelkeznek érhető el. Ha az ügyfél által túl van terhelve hálózati kapacitás hossza meghaladja a rendelkezésre álló sávszélességet, majd adatok nem lett feldolgozva ügyféloldali gyorsan, ahogy az a kiszolgáló küld. Ez a helyzet időtúllépések vezethet.

#### <a name="measurement"></a>Mérési
Figyelje meg, hogyan a sávszélesség-használat idővel kód használatával módosítani [, például a](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ez a kód futása nem sikeresen bizonyos (például az Azure webhelyek) korlátozott engedélyekkel rendelkező környezetekben.

#### <a name="resolution"></a>Megoldás:
Növelje az ügyfél virtuális gép méretét, vagy csökkentse a hálózati sávszélességet.

### <a name="large-requestresponse-size"></a>Nagy méretű kérelem/válasz mérete
#### <a name="problem"></a>Probléma
A nagy méretű kérelem/válasz időtúllépések okozhat. Tegyük fel tegyük fel, hogy az időkorlát az ügyfélhez konfigurált értéke 1 másodperc. Az alkalmazás kéri a két kulcs (például "A" és "B") egyszerre (a azonos fizikai hálózati kapcsolaton keresztül). A legtöbb ügyfelek támogatják a "Pipelining" kérelmek, úgy, hogy mindkét kérelmek "A" és "B" nélkül küldi el a keresztülhaladnak a hálózaton a kiszolgálóra, egy a Várakozás a válaszra. A kiszolgáló elküldi a válaszok vissza ugyanabban a sorrendben. Ha "A" válasz elég nagy, akkor is keleti-afrikai leggyakrabban kérelmeknél időtúllépés. 

A következő példa bemutatja, ebben a forgatókönyvben. Ebben a forgatókönyvben gyorsan küldött kérelem "A" és "B", a kiszolgáló elkezdi gyors küldése válaszok "A" és "B", de miatt adatátviteli idők, "B" elakadnak kérelem és időtúllépést mögött annak ellenére, hogy a kiszolgáló válasza gyorsan.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mérési
Ez a kérelem/válasz, egy bonyolult egy mérését. Alapvetően kell állíthatnak be az Ügyfélkód nyomon követéséhez nagy kérelmeit és válaszait. 

#### <a name="resolution"></a>Megoldás:
1. A redis nagyszámú kis értékek ahelyett, hogy néhány nagy értékek van optimalizálva. Az előnyben részesített megoldás, hogy a kapcsolódó a kisebb értékek adatimportáláshoz feloszthatja. Tekintse meg a [Mi az az ideális méretét értéktartománya a redis? Túl nagy érték 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) utáni körül Miért ajánlott a kisebb értékek részleteiről.
2. (Az ügyfél és a Redis gyorsítótár-kiszolgáló), a virtuális gép méretének növelése nagyobb sávszélesség lehetőségek, csökkenti a nagyobb válaszok adatátviteli idők elérése. További sávszélesség csak a kiszolgálón vagy csak az első az ügyfél nem lehet elegendő. A sávszélesség mérését, és hasonlítsa össze azokat a funkciókat a virtuális gép jelenleg a mérete.
3. Az értéknek a növelésével `ConnectionMultiplexer` meg különböző kapcsolatokon keresztül objektumokat használatát és ciklikus multiplexelés kérelmeket.

### <a name="what-happened-to-my-data-in-redis"></a>Mi történt a Redis-adataimhoz?
#### <a name="problem"></a>Probléma
Bizonyos adatok a saját Azure Redis Cache példányt várt, de nem tűnik, hogy létezik.

#### <a name="resolution"></a>Megoldás:
Lásd: [Mi történt a Redis-adataimhoz?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) a lehetséges okokért és megoldásokért.

## <a name="server-side-troubleshooting"></a>Kiszolgáló oldalán hibaelhárítása
Ez a szakasz ismerteti, amelyek a hibaelhárítási problémák, amelyek miatt egy feltétel, a gyorsítótár-kiszolgálón.

* [A kiszolgáló Memóriaterhelése](#memory-pressure-on-the-server)
* [Magas CPU-használatot / Server betöltése](#high-cpu-usage-server-load)
* [Túllépte a kiszolgáló ügyféloldali sávszélesség](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>A kiszolgáló Memóriaterhelése
#### <a name="problem"></a>Probléma
A kiszolgáló oldalán Memóriaterhelést teljesítménnyel kapcsolatos problémákat, amelyek késleltetheti-e a kérelmek feldolgozásának különböző vezet. Memóriaprobléma találatok, amikor a rendszer általában ki lap adatokat a fizikai memória, a virtuális memória, amely a lemezen. Ez *lap hibás* jelentősen lassítsa le a rendszer. Nincsenek a memóriaterhelése lehetséges okai: 

1. A gyorsítótár teljes kapacitás töltötte adatokkal. 
2. A redis kapja a nagy a memória töredezettségét - leggyakrabban okozta nagy objektumok tárolására (Redis úgy optimalizálták, hogy egy kis objektumok – tekintse meg a [Mi az az ideális méretét értéktartománya a redis? Túl nagy érték 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) részletes post). 

#### <a name="measurement"></a>Mérési
Redis mutatja meg, amelyek segítenek azonosítani azokat a probléma két metrikákat. Az első `used_memory` , a másik `used_memory_rss`. [A metrikák](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) az Azure portál vagy azon keresztül elérhető a [Redis INFO](http://redis.io/commands/info) parancsot.

#### <a name="resolution"></a>Megoldás:
Van több lehetséges módosítások, amelyek biztosíthatják a memóriahasználat kifogástalan tehet:

1. [Memória-házirendet konfigurálhat](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) és a kulcsok lejárati idejének beállítására. Ez a konfiguráció nem lehet elegendő, ha töredezettsége rendelkezik.
2. [Maxmemory fenntartott érték](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , hogy elég nagy a memória töredezettségét kártalanítja.
3. Szakítsa meg a nagy a gyorsítótárazott objektumok kisebb kapcsolódó objektumot.
4. [Skála](cache-how-to-scale.md) a gyorsítótár méretének.
5. Használata egy [prémium gyorsítótár engedélyezve van a Redis-fürt](cache-how-to-premium-clustering.md), akkor [szilánkok számának növelése](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Magas CPU-használatot / Server betöltése
#### <a name="problem"></a>Probléma
Magas CPU-használatot azt, hogy az ügyféloldali meghiúsulhat, ha a Redis válaszára időben feldolgozni, annak ellenére, hogy a Redis gyorsan a választ küldött.

#### <a name="measurement"></a>Mérési
A rendszer nagy CPU-használat a társított teljesítményszámláló vagy az Azure portálon keresztül figyelheti. Ügyeljen arra, hogy figyelje *folyamat* mivel egyetlen folyamat CPU-használat alacsony lehet egy időben, hogy a rendszer általános CPU Processzor magas lehet. A CPU-használat teljesítményt, amelyek megfelelnek a időtúllépések figyelje.

#### <a name="resolution"></a>Megoldás:
* Tekintse át az összes és a riasztások szerepel a [Redis gyorsítótár Advisor](cache-configure.md#redis-cache-advisor).
* Emellett nézze át az ebben a témakörben a többi javaslat és [ajánlott eljárások az Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) Ha Ön rendelkezik alkalmazott tovább optimalizálhatja a gyorsítótárat és az ügyfél az összes beállítás megjelenítéséhez. 
* Tekintse át a [Azure Redis Cache teljesítmény](cache-faq.md#azure-redis-cache-performance) diagramok és ellenőrizze, hogy ha előfordulhat, hogy a jelenlegi rétegtől felső küszöbértékeit közelében. Ha szükséges, [méretezési](cache-how-to-scale.md) további CPU-kapacitás nagyobb gyorsítótár réteghez. Ha prémium csomagban már használ, érdemes [fürtözési kiterjesztése](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Túllépte a kiszolgáló ügyféloldali sávszélesség
#### <a name="problem"></a>Probléma
Attól függően, hogy a gyorsítótár-példányok méretét, hogy korlátozások mekkora hálózati sávszélességre gyakorolt rendelkeznek érhető el. Ha a kiszolgáló meghaladja a rendelkezésre álló sávszélességet, majd nem történik adatküldés gyorsan, az ügyfélnek. Ez a helyzet időtúllépések vezethet.

#### <a name="measurement"></a>Mérési
Figyelheti a `Cache Read` metrika, amely adatok mennyisége gyorsítótárából olvasta be a megabájt / másodperc (MB/s) a megadott jelentési időszak során. Ennek az értéknek felel meg a gyorsítótár által használt hálózati sávszélesség. Ha azt szeretné, a kiszolgálóoldali hálózati sávszélesség korlátja riasztások beállítása, létrehozhat azokat a `Cache Read` számláló. Hasonlítsa össze az értékek a értékkel rendelkező [ezt a táblázatot](cache-faq.md#cache-performance) különböző árképzési szinteket, és méretű gyorsítótár megfigyelt sávszélesség korlátait.

#### <a name="resolution"></a>Megoldás:
Ha következetesen közelében a megfigyelt maximális sávszélesség a árképzési szint és a gyorsítótár méretét, fontolja meg [skálázás](cache-how-to-scale.md) árképzési szint és mérete nagyobb hálózati sávszélesség álljon, az értékekkel a [ezt a táblázatot](cache-faq.md#cache-performance)útmutatóként.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis időtúllépési kivétel
StackExchange.Redis nevű konfigurációs beállítást használja `synctimeout` szinkron műveletek végrehajtását, amelyek rendelkeznek egy alapértelmezett érték 1000 MS. Ha egy aszinkron hívás nem fejezi be a meghatározott időben, a StackExchange.Redis ügyfél jelez az alábbi példához hasonló időtúllépési hiba:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Ez a hibaüzenet, amely segíthet a probléma okát és lehetséges megoldás, mutasson metrikák tartalmazza. A következő táblázat tartalmazza a hiba üzenet metrikák részleteit.

| Hiba történt a üzenet metrika | Részletek |
| --- | --- |
| INST |Az utolsó időszelet: 0 parancsok kiállították. |
| mgr |A szoftvercsatorna-kezelő hajt végre `socket.select`, ami azt jelenti, hogy azt kéri, az operációs rendszer annak jelzésére, amelynek valami; ehhez szoftvercsatorna alapvetően: az olvasó értéke nem aktívan olvasása a hálózatról, mert az nem gondolja bármilyen formában |
| Várólista |73 összes folyamatban lévő műveletek |
| qu |a folyamatban lévő műveletek 6 az el nem küldött várólistájuk, és még nem írt a kimenő hálózati |
| QS |a folyamatban lévő műveletek 67 elküldése a kiszolgálóhoz, de a választ még nem érhető el. A válasz lehet `Not yet sent by the server` vagy`sent by the server but not yet processed by the client.` |
| qc |a folyamatban lévő műveletek 0 választ láthatta, de rendelkezik nincs megjelölve, Várakozás a befejezési hurok miatt befejeződött |
| wR |Nincs egy aktív írási (ami azt jelenti, a 6 el nem küldött kérelmek nem lesznek mellőzve) bájt/activewriters |
| ebben: |Nincs aktív olvasó és elérhető legyen a hálózati bájtok/activereaders nulla bájt |

### <a name="steps-to-investigate"></a>Vizsgálja meg lépései
1. Gondoskodjon arról, hogy a legjobb, a következő mintát segítségével csatlakozás, ha a StackExchange.Redis-ügyfélprogrammal.

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

    További információkért lásd: [csatlakozás a gyorsítótárhoz StackExchange.Redis használatával](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Ellenőrizze, hogy az Azure Redis Cache és az ügyfélalkalmazás az Azure-ban ugyanabban a régióban. Például akkor lehet, hogy lehet első időtúllépések Ha a gyorsítótár USA keleti régiója, de az ügyfél csatlakozik az USA nyugati régiója és a kérelem nem fejezi be belül a `synctimeout` időköz, vagy előfordulhat, hogy lehet első időtúllépések Ha meg vannak hibakereséshez a következőből: a helyi fejlesztési számítógépén. 
   
    Rendelkezik a gyorsítótár rendelkezik erősen ajánlott és az ügyfél azonos Azure-régióban. Ha olyan forgatókönyvekben, amelyek több régióban hívások tartalmaz, célszerű a `synctimeout` időköz értéke nagyobb, mint az alapértelmezett 1000-ms-ot egy `synctimeout` tulajdonság a kapcsolati karakterláncban. A következő példa bemutatja a StackExchange.Redis gyorsítótár kapcsolati karakterlánc részlet rendelkező egy `synctimeout` 2000 MS.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Győződjön meg arról, hogy a legújabb verzióját használja a [StackExchange.Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/). Nincsenek abba, hogy sokkal hatékonyabban időtúllépések számára a legújabb verzióra, akkor fontos, a kódban folyamatosan javított hibák.
3. Ha nincs sávszélességgel kapcsolatos korlátozásai a kiszolgálón vagy az ügyfél által első kötött, hosszabb ideig tart, hogy végezze el, és ekkor időtúllépések. Ha az időtúllépés okozza-e a kiszolgálón a hálózati sávszélesség, olvassa el [túllépte a kiszolgáló ügyféloldali sávszélesség](#server-side-bandwidth-exceeded). Ha az időtúllépés okozza-e ügyfél hálózati sávszélesség, olvassa el [túllépte az ügyféloldali sávszélesség](#client-side-bandwidth-exceeded).
4. Ön az első CPU kötött a kiszolgálón, vagy az ügyfél?
   
   * Ellenőrizze, hogy ha Ön első kötött processzor az ügyfélen, amelyek miatt a kérelem feldolgozása nem lehetséges belül a `synctimeout` időköz, ami az időtúllépés miatt. Ügyfél nagyobb méretű áthelyezése vagy a terhelés elosztása segítségével probléma szabályozására. 
   * Figyeli a kiszolgálón kötött jelölőnégyzetet, ha a Processzor a `CPU` [teljesítmény metrika gyorsítótár](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Amíg Redis kötött Processzor várható okozhatják időtúllépési ezeket a kérelmeket. A probléma megoldása érdekében a terhelés szétosztását a prémium szintű gyorsítótár több szilánkok, vagy váltson egy nagyobb méretű vagy tarifacsomagot. További információkért lásd: [Server ügyféloldali sávszélesség túllépését](#server-side-bandwidth-exceeded).
5. Vannak-e a kiszolgálón feldolgozása hosszú ideig tart parancsok? Hosszú ideig futó parancsokat, amelyek a redis-kiszolgáló feldolgozása hosszú ideig tart, időtúllépések okozhat. Néhány példa a hosszú ideig futó parancsok `mget` kulcsok, ha sok felhasználóval rendelkező `keys *` vagy rosszul írt lua parancsfájlok. Csatlakozás az Azure Redis Cache példány a redis-cli ügyfélprogrammal, vagy használja a [Redis konzol](cache-configure.md#redis-console) , és futtassa a [SlowLog](http://redis.io/commands/slowlog) parancs, ha van-e kérelmeket a vártnál tovább tart. A redis-kiszolgáló és a StackExchange.Redis kevesebb nagy kérelmek helyett a sok kisméretű kérelem vannak optimalizálva. Az adatok felosztása kisebb adattömbökbe javíthatja a dolgok itt. 
   
    A redis-cli és stunnel Azure Redis Cache SSL végpontjához csatlakozó információkért lásd: a [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis előzetes](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogbejegyzést. További információkért lásd: [SlowLog](http://redis.io/commands/slowlog).
6. Magas Redis-kiszolgáló terhelése okozhatja időtúllépések. A kiszolgáló terhelését figyeli a figyelheti a `Redis Server Load` [teljesítmény metrika gyorsítótár](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). A kiszolgáló terhelését, 100 (maximális érték) azt jelzi, hogy a, a redis-kiszolgáló már foglalt, nem üresjárati idő a kérelmek feldolgozásához. Ha bizonyos kérések a képesség másolatot tart megtekintéséhez futtassa a SlowLog parancsot az előző bekezdésben ismertetett módon. További információkért lásd: [magas CPU-használat / Server betöltési](#high-cpu-usage-server-load).
7. Történt más esemény, amely egy hálózati blip okozhatta ügyféloldali? Ha például az ügyfél-példányok száma skálázás felfelé vagy lefelé esemény történt, vagy az ügyfél vagy automatikus skálázása új verziójának telepítése nincs engedélyezve, ellenőrizze az ügyfélen (web-, feldolgozói szerepkör vagy egy infrastruktúra-szolgáltatási virtuális gép)? A tesztelés során, találtunk, amelyeknek, hogy az automatikus skálázás vagy vertikális felskálázásával/le OK kimenő hálózati kapcsolatot is elveszhet néhány másodpercig. StackExchange.Redis kódot is lehetséges, hogy ezek az események és újracsatlakozik-e. Ebben az időszakban az újracsatlakozás a várólistán lévő minden kérést is túllépi az időkorlátot.
8. A Redis gyorsítótár, amely túllépte az időkorlátot a több kis kérelmek megelőző nagy kérés történt? A paraméter `qs` a hibás üzenet közli, hogy hány kérésnek az ügyféltől a kiszolgáló küldtek, de még nem dolgozott választ. Ez az érték lehet egyre több adatra mert StackExchange.Redis egyetlen TCP-kapcsolatot használ, és csak olvasható egy válasz egyszerre. Annak ellenére, hogy az első művelet túllépte az időkorlátot, nem állítja le a küldött adatok mennyisége belőle a kiszolgáló, és más kérelmek le vannak tiltva, amíg a nagy méretű kérelem nem fejeződött, ami időtúllépések. Egy megoldás, időtúllépések a valószínűsége annak biztosítása, hogy a gyorsítótár mérete elegendő a számítási feladatok és a nagy értékek felosztása kisebb adattömbökbe minimalizálása érdekében. Egy másik lehetséges megoldás, hogy készletét használja `ConnectionMultiplexer` az ügyfél objektumokat, és válassza a legalább betöltött `ConnectionMultiplexer` új kérelem küldésekor. Emiatt érdemes egyetlen időtúllépés küldött egyéb kérések számára is időtúllépés miatt.
9. Ha használ `RedisSessionStateprovider`, győződjön meg arról, az újrapróbálkozási időtúllépés helyesen van beállítva. `retrytimeoutInMilliseconds`nagyobbnak kell lennie `operationTimeoutinMilliseonds`, ellenkező esetben nem próbálkozások. Az alábbi példában `retrytimeoutInMilliseconds` 3000 értékre van állítva. További információkért lásd: [ASP.NET munkamenetállapot-szolgáltatóját az Azure Redis Cache](cache-aspnet-session-state-provider.md) és [a konfigurációs paraméterek munkamenetállapot-szolgáltatóját, és a kimeneti gyorsítótár-szolgáltató használatával](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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


1. Ellenőrizze a memóriahasználat az Azure Redis gyorsítótár-kiszolgáló a [figyelési](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` és `Used Memory`. Egy kiürítés házirend van beállítva, ha kezdődik-e a Redis való kulcsok mikor `Used_Memory` eléri a gyorsítótár méretét. Ideális esetben `Used Memory RSS` csak kell valamivel nagyobb, mint `Used memory`. A nagy különbség jelenti, hogy a memória töredezettségét (belső vagy külső. Ha `Used Memory RSS` értéke kisebb, mint `Used Memory`, az azt jelenti, hogy a gyorsítótár-memória részét rendelkezik lett cserélve az operációs rendszer. A csere akkor fordul elő, ha várhatóan néhány jelentős késések fordulnak elő. A Redis nincs szabályozhatják, hogyan a hozzárendelések vannak leképezve memórialapokat, magas `Used Memory RSS` oka általában egy csúcs az igények memória használata. Amikor Redis felszabadítja a memória, a memória rendelkezésére áll vissza a foglaló és a foglaló is, vagy előfordulhat, hogy nem biztosítják a memória vissza a rendszer. Előfordulhat, hogy közötti ellentmondás a `Used Memory` érték és a memória-felhasználás az operációs rendszer által jelentett módon. Lehet, hogy az a tény miatt memória használt és megtörtént, amely a Redis, de nem adott vissza, a rendszer. Memória problémák mérséklése érdekében a következőket végezheti el:
   
   * A gyorsítótár frissítése nagyobb méretűre, hogy nem használ találkoznak memóriahasználatra vonatkozó korlátozásai a rendszer.
   * A kulcsok lejárati idejének beállítására, hogy a régebbi értékek proaktív ki vannak zárva.
   * A figyelő a `used_memory_rss` metrika gyorsítótárazza. Ha ez az érték megközelíti a gyorsítótár méretét, akkor valószínűleg teljesítményproblémák kezdenek el. Ha a prémium szintű gyorsítótárazással, vagy váltson a gyorsítótár méretének az adatok szét több szegmensben osztják.
   
   További információkért lásd: [a kiszolgáló Memóriaterhelése](#memory-pressure-on-the-server).

## <a name="additional-information"></a>További információ
* [Melyik Redis Cache-ajánlatot és -méretet használjam?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [Hogyan elvégez egy teljesítménytesztet és a gyorsítótár teljesítményének a tesztelésére?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Hogyan futtathatom Redis parancsok?](cache-faq.md#how-can-i-run-redis-commands)
* [Azure Redis Cache figyelése](cache-how-to-monitor.md)

