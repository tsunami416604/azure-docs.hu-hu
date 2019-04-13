---
title: Hogyan háríthatók el az Azure Cache redis |} A Microsoft Docs
description: Ismerje meg az Azure Cache kapcsolatos gyakori problémák megoldása a redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527166"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Hogyan háríthatók el az Azure Cache redis

Ez a cikk segít a különböző kategóriák problémák léphetnek fel az Azure Cache Redis-példány a csatlakozáskor hibaelhárítása.

- [Ügyféloldali hibaelhárítási](#client-side-troubleshooting) segít azonosítani, és az alkalmazás a cache szolgáltatáshoz való csatlakozáskor problémák megoldására.
- [Kiszolgálóoldali hibaelhárítási](#server-side-troubleshooting) segít azonosítani, és az Azure Cache Redis oldal előforduló hibák elhárítása.
- [Adatveszteség hibaelhárítási](#data-loss-troubleshooting) azonosítása és megoldása, ahol az kulcsok várt, de nem található a gyorsítótárban.
- [StackExchange.Redis időtúllépési kivételeket](#stackexchangeredis-timeout-exceptions) konkrét útmutatást nyújt a StackExchange.Redis Library kapcsolatos hibák elhárítása.

> [!NOTE]
> A hibaelhárítási lépéseket ebben az útmutatóban számos Redis parancsok futtatása és figyelése a korábbi teljesítménymutatók utasításokat tartalmaz. További információk és útmutatás, lásd: a cikkek a [további információkat](#additional-information) szakaszban.
>
>

## <a name="client-side-troubleshooting"></a>Ügyféloldali hibáinak elhárítása

Ez a szakasz bemutatja az ügyfélalkalmazásban feltétel miatt előforduló problémák elhárítása.

- [Rendelkezésre álló memória mennyisége az ügyfélen](#memory-pressure-on-the-client)
- [Az adatforgalom hirtelen](#burst-of-traffic)
- [Nagyszámú ügyféllel CPU-használat](#high-client-cpu-usage)
- [Ügyféloldali sávszélesség túllépve](#client-side-bandwidth-exceeded)
- [Nagy méretű kérelem/válasz mérete](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Rendelkezésre álló memória mennyisége az ügyfélen

Rendelkezésre álló memória mennyisége az ügyfélszámítógépen késleltetheti-e a gyorsítótárból válaszok feldolgozásával teljesítményproblémák különféle vezet. Találatok száma a rendelkezésre álló memória mennyisége, amikor a rendszer az adatokat a lemezre előfordulhat, hogy lapon. Ez _lap hibát okozó_ jelentősen lelassíthatja a rendszer.

Észleli a rendelkezésre álló memória mennyisége az ügyfélen:

- Gépen, győződjön meg arról, hogy azt nem haladhatja meg a rendelkezésre álló memória, a memóriahasználat figyelésére.
- Az ügyfél figyelni `Page Faults/Sec` teljesítményadatait mutatja. Normál működés során a legtöbb rendszer egyes oldalhibák rendelkezik. A kérelem időtúllépése megfelelő oldalhibák kiugrások adhatja meg a rendelkezésre álló memória mennyisége.

Az ügyfél nagy memóriaterhelés többféleképpen enyhíthető:

- Merüljön a memória használati minták csökkentése érdekében az ügyfél memóriahasználatát.
- Frissítse az ügyfél virtuális gép több memóriát és nagyobb méretet.

### <a name="burst-of-traffic"></a>Az adatforgalom hirtelen

A forgalom adatlöketekkel kombinálva gyenge `ThreadPool` beállításai már a Redis-kiszolgáló által küldött, de még nem használják az ügyféloldali adatok feldolgozása az késleltetések eredményezhet.

A figyelő hogyan a `ThreadPool` statisztika, mint az idő módosítása [például `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Használhat `TimeoutException` StackExchange.Redis üzeneteit további vizsgálat céljából alábbi módon:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Az előző kivétel van néhány érdekes problémák:

- Figyelje meg, hogy az a `IOCP` szakaszban, és a `WORKER` szakaszban van egy `Busy` tulajdonság értéke nagyobb, mint a `Min` értéket. Ez a különbség azt jelenti, hogy a `ThreadPool` beállításait kell módosítani.
- Emellett megtekintheti `in: 64221`. Ez azt jelzi, hogy 64,211 bájtok száma az ügyfél kernel szoftvercsatorna-réteg nem érkezett, de még nem lett az alkalmazás beolvassa. Ez a különbség általában azt jelenti, hogy az alkalmazás (például a StackExchange.Redis) nem adatok olvasása a hálózatról gyorsan a kiszolgálón van elküldené azt.

Is [konfigurálása a `ThreadPool` beállítások](https://gist.github.com/JonCole/e65411214030f0d823cb) győződjön meg arról, hogy a szálkészlet felskálázással alatt gyorsan átmenetileg megnövelhető a forgatókönyveket.

### <a name="high-client-cpu-usage"></a>Nagyszámú ügyféllel CPU-használat

Nagyszámú ügyféllel CPU-használat azt jelzi, hogy a rendszer nem tud lépést tartani a munkahelyi ehhez befolyással van. Annak ellenére, hogy a gyorsítótár gyorsan küldi el a választ, az ügyfél nem sikerült feldolgozni a választ időben.

Az ügyfél rendszerszintű processzorhasználat elérhető mérőszámok segítségével, az Azure Portalon vagy a gépen teljesítményszámlálók figyelése. Ügyeljen arra, hogy nem figyelheti *folyamat* CPU, mert egyetlen folyamat CPU-használat alacsony rendelkezhet, de a rendszerszintű Processzor magas lehet. Tekintse meg a CPU-használat, amelyek megfelelnek az időtúllépések kiugrások. Magas Processzor okozhat nagy `in: XXX` lévő értékeknek `TimeoutException` hibaüzenetek leírtak szerint a [az adatforgalom hirtelen](#burst-of-traffic) szakaszban.

> [!NOTE]
> StackExchange.Redis 1.1.603, és később tartalmazza a `local-cpu` a metrika `TimeoutException` hibaüzenetek. Ügyeljen arra, hogy a legújabb verzióját használja a [StackExchange.Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/). Nincsenek a kódot, még robusztusabbá időtúllépések, ezért fontos a legújabb verzióra kellene folyamatosan javított hibák.
>
>

Az ügyfél magas CPU-használat csökkentése érdekében:

- Vizsgálja meg, mi okozza a CPU adatforgalmi csúcsokhoz.
- Frissítse az ügyfél egy nagyobb méretű virtuális gép több Processzor kapacitása.

### <a name="client-side-bandwidth-exceeded"></a>Ügyféloldali sávszélesség túllépve

Az ügyfélgépek architektúrájának függően lehet a korlátozások a mekkora hálózati sávszélességre van érhető el. Ha az ügyfél hálózati kapacitás túlterhelés által meghaladja a rendelkezésre álló sávszélességet, majd adatot nem dolgoz fel az ügyféloldalon gyorsan, ahogy az a kiszolgáló küld. Ez a helyzet időtúllépéseket eredményezhet.

Figyelheti, hogyan változik a sávszélesség-használat többön [például `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ez a kód nem lehetséges, hogy sikeresen lefutott bizonyos környezetekben (például az Azure-webhelyeken) a korlátozott engedélyekkel.

Csökkentése érdekében, hálózati sávszélesség-használat csökkentésére, vagy növelje az ügyfél egy további hálózati kapacitás a virtuális gép méretét.

### <a name="large-requestresponse-size"></a>Nagy méretű kérelem/válasz mérete

Egy nagy kérés/válasz időtúllépések okozhat. Például tegyük fel, hogy az időkorlát az ügyfélen beállított értéke 1 másodperc. Az alkalmazás két kulcs (például "A" és "B") kérelmek egyszerre (ugyanazt a fizikai hálózati kapcsolatot használja). A legtöbb ügyfél támogatja a kérelem "az adatcsatornás feldolgozás", ahol mindkét "A" és "B" küld egymás után nem várja meg a választ. A kiszolgáló a válaszok küld vissza az ugyanabban a sorrendben. Ha "A" válasz túl nagy, azt is keleti-afrikai leggyakrabban kéréseket az időkorlátot.

A következő példában kérelem "A" és "B" kapnak gyorsan a kiszolgálón. A kiszolgáló elkezdi gyors válaszokat "A" és "B" küldése. Miatt adatátviteli idők "B" response "A" mögött kell várnia, válasz időkorlátja annak ellenére, hogy a kiszolgáló válasza gyorsan.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Ez a kérelem/válasz, adott nehéz méréséhez. Nagyméretű kérelmek és válaszok nyomon követéséhez az Ügyfélkód sikerült alakítsa ki.

Válasz nagy méretű megoldásuk különböző, de tartalmazza:

1. Az alkalmazás optimalizálása a nagy számú kis-értékek ahelyett, hogy néhány nagy értékeket.
    - Az előnyben részesített megoldás, ha a kapcsolódó a kisebb értékek az adatokat.
    - Tekintse meg a bejegyzését [Mi az az ideális érték mérettartománya redis? Túl nagy érték 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) részleteket arról, hogy miért a kisebb értékek használata ajánlott.
1. Nagyobb sávszélesség-képességek beolvasása a virtuális gép méretének növelése
    - Az ügyfél vagy kiszolgáló virtuális gép több sávszélességet csökkentheti az adatátviteli idők nagyobb válaszok.
    - Hasonlítsa össze az aktuális virtuális gép méretét a határértékeket mindkét gépen a jelenlegi hálózat használatát. További sávszélesség csak a kiszolgáló vagy csak az ügyfél nem feltétlenül elegendő.
1. Növelje az alkalmazás használ a kapcsolat objektumot.
    - Használja a ciklikus időszeletelés módszerével kéréseit különböző kapcsolatobjektumok keresztül.

## <a name="server-side-troubleshooting"></a>Kiszolgálóoldali hibáinak elhárítása

Ez a szakasz ismerteti a gyorsítótár-kiszolgálón feltétel miatt előforduló problémák elhárítása.

- [Rendelkezésre álló memória mennyisége a kiszolgálón](#memory-pressure-on-the-server)
- [Magas CPU-használat / kiszolgáló terhelése](#high-cpu-usage--server-load)
- [Server Side Bandwidth Exceeded](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Rendelkezésre álló memória mennyisége a kiszolgálón

Rendelkezésre álló memória mennyisége a kiszolgáló oldalán teljesítményproblémák késleltetheti-e a feldolgozási kérelmek különféle vezet. Találatok száma a rendelkezésre álló memória mennyisége, amikor a rendszer az adatokat a lemezre előfordulhat, hogy lapon. Ez _lap hibát okozó_ jelentősen lelassíthatja a rendszer. Van a rendelkezésre álló memória mennyisége lehetséges okai:

- A gyorsítótár ki van töltve, a maximális kapacitáshoz közeli adatokkal.
- A redis magas a memória töredezettségét jelenjenek meg. A töredezettség leggyakrabban okozzák nagy objektumok tárolására, mivel Redis kis objektumok van optimalizálva.

A redis két stats keresztül elérhetővé teszi a [INFO](https://redis.io/commands/info) parancsot, amely segíthet a probléma azonosítása: "used_memory" és "used_memory_rss". Is [metrikák megtekintéséhez](cache-how-to-monitor.md#view-metrics-with-azure-monitor) a portál használatával.

Van több lehetséges végzett módosítások is annak érdekében, hogy kifogástalan memóriahasználat:

- [A memória-házirend konfigurálása](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) és a kulcsok lejárati idejének beállítására. Ezt a házirendet nem lehet elegendő, ha töredezettsége rendelkezik.
- [A maxmemory-szolgáltatás számára fenntartott érték](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ez elég nagy legyen a memória töredezettségét kompenzálja. További információkért tekintse meg a további [memóriafoglalások szempontjai](#considerations-for-memory-reservations) alatt.
- Bontsa fel a nagy gyorsítótárazott objektumot kisebb kapcsolódó objektumok.
- [Riasztások létrehozása](cache-how-to-monitor.md#alerts) mérőszámokat, például a használt memória korai lehetséges hatások kapcsolatos értesítést.
- [Méretezési csoport](cache-how-to-scale.md) további memória-kapacitás nagyobb gyorsítótár mérete.

#### <a name="considerations-for-memory-reservations"></a>Memóriafoglalások szempontjai

Memória foglalás értékek frissítése, például a maxmemory-szolgáltatás számára fenntartott, hatással lehet a gyorsítótár teljesítményére. Tegyük fel, amely ki van töltve, 49 GB adatot tartalmazó 53 GB-os gyorsítótár. A maximális rendelkezésre álló rendszermemória 45 GB a Foglalás érték módosítása 8 GB-ra csökken. Ha _used_memory_ vagy _used_memory_rss_ 45 GB-nál nagyobb értékek, a rendszer lehetséges, hogy kizárják az adatokat amíg _used_memory_ és _used_memory_rss_ 45 GB alatt. A kizárási növelheti a kiszolgáló terhelését és a memória töredezettség.

### <a name="high-cpu-usage--server-load"></a>Magas CPU-használat / kiszolgáló terhelése

Egy magas kiszolgáló terhelését, vagy a CPU-használat jelenti, hogy a kiszolgáló nem tudja feldolgozni az egy időben feldolgozza a kérelmeket. Előfordulhat, hogy a kiszolgáló lassan válaszol, és nem lehet kérelemarányok tartani.

[Figyelheti a mérőszámokat](cache-how-to-monitor.md#view-metrics-with-azure-monitor) például a Processzort vagy a kiszolgáló-terhelés. Tekintse meg a CPU-használat, amelyek megfelelnek az időtúllépések kiugrások.

Van néhány módosításokat végezhet, magas kiszolgáló terhelésének csökkentése érdekében:

- Vizsgálja meg, mi okozza a CPU-használatnak például a futó [igényesebb parancsokat](#expensive-commands) vagy lap hibát okozó magas memóriaprobléma miatt.
- [Riasztások létrehozása](cache-how-to-monitor.md#alerts) például a Processzor- vagy a Betöltés a korai értesülni lehetséges hatások metrikákra vonatkozóan.
- [Méretezési csoport](cache-how-to-scale.md) egy nagyobb gyorsítótár mérete nagyobb CPU-kapacitással rendelkező.

#### <a name="expensive-commands"></a>Igényesebb parancsokat

Nem minden Redis parancsok egyaránt jönnek létre – néhány drágább, mint mások. A [Redis parancsok dokumentációja](https://redis.io/commands) minden parancs összetettségétől ideje látható. Tekintse át az ezen parancsok a teljesítményre gyakorolt hatás megértéséhez a gyorsítótár-ban futtatott parancsok ajánlott. Például a [kulcsok](https://redis.io/commands/keys) parancs gyakran használják, hogy-e egy O(N) művelet ismerete nélkül. KULCSOK használatával elkerülheti [vizsgálata](https://redis.io/commands/scan) csökkenteni a Processzor hirtelen megugró kihasználtság.

Használatával a [SLOWLOG](https://redis.io/commands/slowlog) parancsot, akkor mérni tudja költséges parancsok végrehajtása a kiszolgálón.

### <a name="server-side-bandwidth-exceeded"></a>Server-Side Bandwidth Exceeded

Különböző gyorsítótár méreteknél különböző hálózati sávszélesség-kapacitást. Ha a kiszolgáló meghaladja a rendelkezésre álló sávszélességet, majd adatok nem küldi el leggyorsabban az ügyfél. Ügyfelek kérelmek időtúllépési sikerült, mert a kiszolgáló nem adatok leküldése az ügyfél elég gyorsan.

A "Gyorsítótár-Olvasás" és "Gyorsítótár Write" mérőszámok segítségével tekintse meg, mennyi kiszolgálóoldali sávszélességet használja. Is [metrikák megtekintéséhez](cache-how-to-monitor.md#view-metrics-with-azure-monitor) a portálon.

Olyan helyzetekben, ahol a hálózati sávszélesség használatának csökkentése érdekében zárja be, a maximális kapacitás:

- Hívás ügyfélviselkedést csökkentése érdekében a hálózati igény szerint módosíthatja.
- [Riasztások létrehozása](cache-how-to-monitor.md#alerts) a metrikák, mint a gyorsítótár-Olvasás vagy a gyorsítótár-írás korai lehetséges hatások kapcsolatos értesítést.
- [Méretezési csoport](cache-how-to-scale.md) , egy nagyobb méretű gyorsítótár mérete a további hálózati sávszélesség-kapacitást.

## <a name="data-loss-troubleshooting"></a>Adatveszteség hibaelhárítás

Az egyes várt az adatokat a Redis-példányt az Azure Cache-ben, nem verziónk, de nincs.

Lásd: [Mi történt a redis adataimat?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) a lehetséges okokért és megoldásokért.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis időtúllépési kivételek

StackExchange.Redis konfigurációs beállítás nevesített használ `synctimeout` az alapértelmezett érték 1000 ms, a szinkron műveletek számára. Ebbe a synchronního volání nem fejeződik be, ha a StackExchange.Redis ügyfél jelez az alábbi példához hasonló időtúllépési hiba:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Ez a hibaüzenet, amely segíthet a pont, a probléma okát és lehetséges megoldási metrikákat tartalmazza. Az alábbi táblázat tartalmazza a hiba üzenet metrikák részleteit.

| Hiba történt a üzenet metrika | Részletek |
| --- | --- |
| h |Az utolsó időszeletben: 0 parancsokat kiadva. |
| mgr |A szoftvercsatorna-kezelő tesz a `socket.select`, ami azt jelenti, hogy azt kéri, az operációs rendszer, amely rendelkezik, hogy egy szoftvercsatorna jelzi. Az olvasó olvasása a hálózatról aktívan nem, mert ez nem úgy gondolja, hogy bármilyen formában van |
| üzenetsor |Nincsenek folyamatban lévő műveletek 73 összesen |
| l |a folyamatban lévő műveletek 6 az el nem küldött üzenetsorban, és még nem írt, a kimenő hálózati |
| QS |67-es, a folyamatban lévő műveletek a kiszolgálóra küldött, de a választ még nem érhető el. A válasz lehet `Not yet sent by the server` vagy `sent by the server but not yet processed by the client.` |
| qc |a folyamatban lévő műveletek 0 válaszok láthatta, de még nem még lett megjelölve befejezettként, mert arra várnak, hogy az a befejezési hurok |
| wR |Van egy aktív írási (ami azt jelenti, a 6 el nem küldött kérelmek nem hagyja figyelmen kívül) bájtok/activewriters |
| ebben: |Nincs aktív olvasó és nulla bájtot a hálózati bájtok/activereaders az olvasni kívánt érhető el |

### <a name="steps-to-investigate"></a>Vizsgálja meg a lépéseket

1. Ajánlott eljárásként győződjön meg arról, hogy csatlakozhassanak, ha a StackExchange.Redis ügyfelet használja a következő mintát használja.

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
    ```

    További információkért lásd: [csatlakozás a gyorsítótárhoz, használja a StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazás az Azure-ban ugyanabban a régióban. Például, akkor előfordulhat, hogy kell első időtúllépések, amennyiben a gyorsítótár az USA keleti régiójában, de az ügyfél az USA nyugati RÉGIÓJA és a kérés belül nem fejeződik be a `synctimeout` időköz, vagy előfordulhat, hogy lehet első időtúllépések helyi fejlesztői gépen a hibakeresés során. 

    Erősen javasoljuk, hogy rendelkezik a gyorsítótár és az ügyfél azonos Azure-régióban. Ha egy forgatókönyvet, amely tartalmazza a régiók közötti hívások, kell beállítania a `synctimeout` időköz nagyobb, mint az alapértelmezett 1000 ms gyakoriság együtt értékre egy `synctimeout` tulajdonság a kapcsolati karakterláncban. Az alábbi példa bemutatja egy kódrészletet egy kapcsolati karakterláncot a redis-gyorsítótár az Azure által biztosított StackExchange.Redis egy `synctimeout` 2000 MS.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Ügyeljen arra, hogy a legújabb verzióját használja a [StackExchange.Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/). Nincsenek a kódot, még robusztusabbá időtúllépések, ezért fontos a legújabb verzióra kellene folyamatosan javított hibák.
1. Ha a kérelmek által a kiszolgálón vagy az ügyfelek sávszélesség-korlátozások vannak kötve, tovább tart, hogy végezze el, és időtúllépéseket is okozhatnak. Ha az időkorlát miatt a kiszolgáló hálózati sávszélesség van-e, olvassa el [túllépte a kiszolgálóoldali sávszélesség](#server-side-bandwidth-exceeded). Ha az időkorlát miatt ügyfél hálózati sávszélesség van-e, olvassa el [túllépte az ügyféloldali sávszélesség](#client-side-bandwidth-exceeded).
1. Ön CPU első kötött a kiszolgálón vagy az ügyfél?

   - Ellenőrizze, hogy ha Ön még első kötelezőnek CPU az ügyfélen. Magas Processzor hibát okozhat a kérelem feldolgozása nem lehetséges belül a `synctimeout` intervallum és a egy kérelem OK. Egy ügyfél nagyobb méretű áthelyezése vagy elosztja a terhelést segítségével szabályozhatja a probléma.
   - Annak ellenőrzése, hogy a Processzor kap kötve, a kiszolgáló-figyelési szolgáltatás által a `CPU` [gyorsítótárazzák a teljesítmény-mérőszám](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). CPU kötött pedig a Redis érkező kérések okozhat, ezeket a kérelmeket, ideje. Oldja meg ezt az állapotot, a prémium szintű gyorsítótár több szegmens között oszthatja el a terhelést, vagy váltson egy nagyobb méretű vagy tarifacsomagot. További információkért lásd: [kiszolgáló oldalán a sávszélesség túllépte](#server-side-bandwidth-exceeded).
1. Vannak-e a kiszolgálón feldolgozása hosszú időt vesz igénybe parancsok? Hosszan futó parancsok, amelyek a redis-kiszolgáló feldolgozása hosszú ideig tart időtúllépések okozhat. Hosszú ideig futó parancsokkal kapcsolatos további információkért lásd: [igényesebb parancsokat](#expensive-commands). Az Azure Cache a Redis-példányt a redis-cli ügyfél használatával kapcsolódhat, vagy a [Redis konzol](cache-configure.md#redis-console). Ezután futtassa a [SLOWLOG](https://redis.io/commands/slowlog) paranccsal megtekintheti, hogy van-e a vártnál lassabban kér. Redis-kiszolgáló és a StackExchange.Redis kevesebb nagy kérelmeket, hanem sok kisebb kérelem vannak optimalizálva. Az adatok felosztása szeletekre javíthatja a dolgok itt.

    A gyorsítótár SSL-végpont redis-cli és a stunnel használatával történő csatlakozásról információkért lásd: a következő blogbejegyzésben: [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis az előzetes kiadásban](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Magas Redis-kiszolgáló terhelése okozhatja időtúllépések. A kiszolgáló terhelése figyelemmel figyelése a `Redis Server Load` [gyorsítótárazzák a teljesítmény-mérőszám](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). A kiszolgáló terhelését, 100 (maximális értéke) azt jelzi, hogy, a redis-kiszolgáló már foglalt, az üresjárati idővel, a kérelmek feldolgozásához. Ha bizonyos kérelmek igényelnek a kiszolgálói funkció az összes megtekintéséhez futtassa a SlowLog parancs az előző bekezdésben ismertetett módon. További információkért tekintse meg a magas CPU-használat / kiszolgáló terhelése.
1. Történt más esemény, amely egy hálózati blip-oka lehetett az ügyféloldalon? Leggyakoribb események tartalmazzák: ügyfél-példányok számának vertikális felskálázása vagy leskálázást, üzembe helyezése egy új verziója, az ügyfél vagy az automatikus méretezés engedélyezve van. A tesztelés található, hogy az automatikus méretezés, vagy méretezése felfelé és lefelé vezethet a kimenő hálózati kapcsolat elvész néhány másodpercig. StackExchange.Redis kódot képes legyen ellenállni a ilyen események és újracsatlakozik. Miközben újracsatlakozás, bármilyen kérelmeket az üzenetsorban is időtúllépéssel fejeződött be.
1. Több kis méretű kérések megelőző a gyorsítótárhoz, amely lejárt az idő nagy kérés történt? A paraméter `qs` a hibás üzenet közli Önnel, hogy hány kérésnek az ügyfélről a kiszolgálónak küldött, de még nem feldolgozott választ. Ezt az értéket is folyamatosan növekvő, mert StackExchange.Redis egyetlen TCP-kapcsolatot használ, és egyszerre csak egy választ csak olvasható. Annak ellenére, hogy az első művelet túllépte az időkorlátot, akkor több adatot küld vagy a kiszolgáló nem leállítása. Más kérelmek le lesz tiltva, amíg a nagy kérés befejezése, és időtúllépéssel okozhat. Egy megoldás, hogy időtúllépések esélyét annak biztosítása, hogy a gyorsítótár mérete elegendő a számítási feladatok és a nagy értékek felosztása szeletekre. Egy másik lehetséges megoldás az, hogy a készlet használata `ConnectionMultiplexer` az ügyfél az objektumok, és válassza a legkevésbé betöltött `ConnectionMultiplexer` új kérelem küldése során. Több kapcsolat objektumra betöltése akadályozhatja meg egyetlen időtúllépés küldött egyéb kérések számára is időtúllépés miatt.
1. Ha használ `RedisSessionStateProvider`, ellenőrizze az újrapróbálkozási időkorlátnak az újrapróbálkozási megfelelően. `retryTimeoutInMilliseconds` érdemes lehet magasabb, mint `operationTimeoutInMilliseconds`, ellenkező esetben fordulhat elő, nem az újrapróbálkozásokat. Az alábbi példában `retryTimeoutInMilliseconds` 3000 értékre van állítva. További információkért lásd: [ASP.NET munkamenetállapot-szolgáltatóját Azure Cache redis](cache-aspnet-session-state-provider.md) és [munkamenetállapot-szolgáltató és a kimeneti gyorsítótár-szolgáltatóját a konfigurációs paraméterek használata](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
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
    ```

1. Ellenőrizze az Azure Cache a Redis-kiszolgáló által a memóriahasználat [figyelési](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` és `Used Memory`. Ha egy kiürítési szabályzatot van beállítva, a Redis elindul kizárásának kulcsokat mikor `Used_Memory` eléri a gyorsítótár méretét. Ideális esetben `Used Memory RSS` kell lennie csak valamivel nagyobb `Used memory`. A nagy különbség az jelenti, hogy a memória töredezettségét (belső vagy külső). Amikor `Used Memory RSS` van kevesebb, mint `Used Memory`, azt jelenti, hogy a gyorsítótár-memória része a felcserélés van lett az operációs rendszer által. A csere történik, ha várhatóan néhány jelentős késéseket. Mivel a Redis nem szabályozhatják, hogyan vannak leképezve a hozzárendelések memórialapok, magas `Used Memory RSS` Ez gyakran a memóriahasználat ugrásszerű eredménye. Ha a Redis-kiszolgáló felszabadítja a memória, a foglaló vesz igénybe, a memória, de előfordulhat, hogy vagy előfordulhat, hogy nem biztosít a memória vissza a rendszer. Előfordulhat, hogy egy között eltérés van a `Used Memory` érték és a memória-felhasználás az operációs rendszer által jelentett módon. Előfordulhat, hogy memória használt és a Redis által, de nem adott vissza a rendszer. Memóriahiba csökkentése érdekében a következőket teheti:

   - Frissítse a gyorsítótár egy nagyobb méretű, hogy nem futnak memóriahasználatra vonatkozó korlátozásai ellen a rendszeren.
   - A kulcsok lejárati idejének beállítására, hogy a régebbi értékek proaktív módon ki vannak zárva.
   - A figyelő a `used_memory_rss` metrika gyorsítótárazni. Ez az érték megközelíti a gyorsítótár méretét, amikor Ön valószínűleg azokat a teljesítménnyel kapcsolatos problémák. Ha prémium szintű gyorsítótár használata, vagy frissítsen egy nagyobb méretű gyorsítótár mérete, ossza el az adatokat több szegmens között.

   További információkért lásd: [rendelkezésre álló memória mennyisége a kiszolgálón](#memory-pressure-on-the-server).

## <a name="additional-information"></a>További információ

- [Milyen Azure Cache a Redis-ajánlatot és -méretet használjam?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Hogyan lehet becslésére és a gyorsítótár teljesítményének tesztelése?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Hogyan futtathatok Redis parancsok?](cache-faq.md#how-can-i-run-redis-commands)
- [A Redis Azure Cache figyelése](cache-how-to-monitor.md)
