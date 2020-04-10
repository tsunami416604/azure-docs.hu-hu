---
title: Azure Cache for Redis-időtúllépések hibaelhárítása
description: Megtudhatja, hogyan oldhatja meg a Redis-gyorsítótárban az Azure Cache for Redis gyakori időmegadási problémáit, például a redis kiszolgálójavítást és a StackExchange.Redis időbeli kivételeket.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4301a55e3f5ea5b445ef1540ee59d1b5c28ca0ed
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010817"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Azure Cache for Redis-időtúllépések hibaelhárítása

Ez a szakasz ismerteti a redis-i Azure Cache-hez való csatlakozáskor felmerülő időbeli elállási problémák hibaelhárítását.

- [Redis szerver javítás](#redis-server-patching)
- [StackExchange.Redis időout-kivételek](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Ebben az útmutatóban a hibaelhárítási lépések közé tartozik a Redis-parancsok futtatására és a különböző teljesítménymutatók figyelésére vonatkozó utasítások. További információt és útmutatást a [További információk](#additional-information) című részben található cikkben talál.
>

## <a name="redis-server-patching"></a>Redis szerver javítás

A Redis Azure Cache for Redis rendszeresen frissíti a kiszolgálószoftverét az általa biztosított felügyelt szolgáltatásfunkció részeként. Ez [a folttevékenység](cache-failover.md) nagyrészt a színfalak mögött zajlik. A feladatátvételek során, amikor a Redis kiszolgálócsomópontok javítása, Redis ügyfelek csatlakozik ezekhez a csomópontokhoz előfordulhatnak ideiglenes időtúlszállások kapcsolatok között ezek a csomópontok között. Lásd: [Hogyan befolyásolja a feladatátvétel az ügyfélalkalmazásomat,](cache-failover.md#how-does-a-failover-affect-my-client-application) ha többet szeretne tudni arról, hogy milyen mellékhatásai lehetnek az alkalmazáson, és hogyan javíthatja a javítási események kezelését.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis időout-kivételek

A StackExchange.Redis az `synctimeout` 1000 ms alapértelmezett értékkel rendelkező szinkron műveletekhez elnevezett konfigurációs beállítást használja. Ha egy szinkron hívás ez idő alatt nem fejeződik be, a StackExchange.Redis ügyfél a következő példához hasonló időtúlhasználati hibát okoz:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Ez a hibaüzenet olyan mérőszámokat tartalmaz, amelyek segíthetnek a probléma okára és lehetséges megoldására. Az alábbi táblázat a hibaüzenet-mérőszámokkal kapcsolatos részleteket tartalmazza.

| Hibaüzenet-metrika | Részletek |
| --- | --- |
| Inst |Az utolsó alkalommal szelet: 0 parancsokat adtak ki |
| Mgr |A socket manager `socket.select`csinál , ami azt jelenti, hogy arra kéri az operációs rendszert, hogy jelezze a foglalat, hogy van valami köze. Az olvasó nem aktívan olvas a hálózatról, mert nem hiszem, hogy van mit tenni |
| üzenetsor |Összesen 73 folyamatban lévő művelet |
| Qu |A folyamatban lévő műveletek közül 6 az el nem küldött várólistában van, és még nem lett megírva a kimenő hálózatra |
| Qs |A folyamatban lévő műveletek közül 6-ot elküldtek a kiszolgálónak, de a válasz még nem áll rendelkezésre. A válasz `Not yet sent by the server` lehet vagy`sent by the server but not yet processed by the client.` |
| Qc |0 a folyamatban lévő műveletek látták válaszokat, de még nem jelölték meg a teljes, mert várnak a befejezési hurok |
| Wr |Van egy aktív író (ami azt jelenti, hogy a 6 el nem küldött kérelmek nem lesznek figyelmen kívül hagyva) bájt/ activewriters |
| in |Nincsenek aktív olvasók, és nulla bájt érhető el a hálózati adapter bájtján/activereadersén |

A következő lépésekkel megvizsgálhatja a lehetséges kiváltó okokat.

1. Ajánlott eljárásként győződjön meg arról, hogy a stackexchange.redis ügyfél használatakor az alábbi mintát használja a csatlakozáshoz.

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

    További információ: [Csatlakozás a gyorsítótárhoz a StackExchange.Redis használatával](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)című témakörben talál.

1. Győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazás ugyanabban a régióban az Azure-ban. Előfordulhat például, hogy időtúljár, ha a gyorsítótár usa keleti részén van, de az ügyfél `synctimeout` az USA nyugati részén van, és a kérés nem fejeződik be az időtartamon belül, vagy előfordulhat, hogy időtúljár, amikor a helyi fejlesztői gépről hibakeresést végez. 

    Erősen ajánlott, hogy a gyorsítótár és az ügyfél ugyanabban az Azure-régióban. Ha olyan eset van, amely régiók közötti `synctimeout` hívásokat tartalmaz, az intervallumot az alapértelmezett 1000 ms-es időköznél magasabb értékre kell állítania egy `synctimeout` tulajdonságnak a kapcsolati karakterláncba való belefoglalásával. A következő példa a StackExchange.Redis kapcsolati karakterláncának egy 2000 `synctimeout` ms-os, A Redis-hez biztosított kapcsolati karakterláncának egy részletét mutatja be.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Győződjön meg arról, hogy a [StackExchange.Redis NuGet csomag](https://www.nuget.org/packages/StackExchange.Redis/)legújabb verzióját használja. Vannak hibák folyamatosan rögzítik a kódot, hogy ez robusztusabb időtúlállások, így miután a legújabb verzió fontos.
1. Ha a kérelmeket a kiszolgálón vagy az ügyfélen lévő sávszélesség-korlátozások kötik, hosszabb időt vesz igénybe, és időtúllépésre tehet. Ha meg szeretné tudni, hogy az időkorlát a kiszolgálón lévő hálózati sávszélesség miatt van-e, olvassa el a [Kiszolgálóoldali sávszélesség korlátozása című témakört.](cache-troubleshoot-server.md#server-side-bandwidth-limitation) Ha meg szeretné tudni, hogy az ügyfélhálózati sávszélesség miatt időkorlát van-e, olvassa el [az Ügyféloldali sávszélesség korlátozása](cache-troubleshoot-client.md#client-side-bandwidth-limitation)., ha meg szeretné tudni, hogy az időkorlát az ügyfélhálózati sávszélesség miatt van-e, olvassa el az Ügyféloldali sávszélesség korlátozása ., lásd: Ügyféloldali sávszélesség korlátozása
1. A processzort a kiszolgálón vagy az ügyfélen köti össze?

   - Ellenőrizze, hogy az ügyfél processzora köti-e össze. A magas processzor oka az, hogy `synctimeout` a kérelem nem kerül feldolgozásra az időtartamon belül, és időtúlterhelést okozhat. A probléma megoldásához nagyobb ügyfélméretre való áttérés vagy a terhelés elosztása segíthet.
   - Ellenőrizze, hogy a processzor tágana-e a kiszolgálón a PROCESSZOR [gyorsítótárteljesítmény-mérőszámának figyelésével.](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) Kérelmek érkezik, miközben Redis a CPU kötött okozhat a kérelmek időout. A feltétel kezelése érdekében a terhelést egy prémium szintű gyorsítótárban több szegmens között is eloszthatja, vagy nagyobb méretűre vagy tarifacsomagra frissíthet. További információt a [kiszolgálóoldali sávszélesség korlátozása](cache-troubleshoot-server.md#server-side-bandwidth-limitation)című témakörben talál.
1. Vannak-e olyan parancsok, amelyek feldolgozása hosszú időt vesz igénybe a kiszolgálón? A redis-serveren hosszú ideig futó parancsok időhosszabbításokat okozhatnak. A hosszú ideig futó parancsokról a [Hosszan futó parancsok](cache-troubleshoot-server.md#long-running-commands)című témakörben talál további információt. A Redis-cli ügyféllel vagy a [Redis console](cache-configure.md#redis-console)használatával csatlakozhat az Azure Cache for Redis példányhoz. Ezután futtassa a [SLOWLOG](https://redis.io/commands/slowlog) parancsot, és nézze meg, hogy vannak-e a vártnál lassabb kérelmek. A Redis Server és a StackExchange.Redis sok kis kérésre van optimalizálva, nem pedig kevesebb nagy kérésre. Ha az adatokat kisebb részekre osztja, az javíthatja a dolgokat.

    A gyorsítótár TLS/SSL-végpontjához redis-cli és stunnel használatával történő csatlakozásról a [Announceing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)című blogbejegyzésben talál további információt.
1. A Redis-kiszolgáló nagy terhelése időtúlterhelést okozhat. A kiszolgáló terhelését a `Redis Server Load` [gyorsítótár teljesítménymutatójának](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)figyelésével figyelheti. A 100-as kiszolgálóterhelés (maximális érték) azt jelzi, hogy a redis kiszolgáló foglalt volt, tétlen idő nélkül, a kérelmek feldolgozása. Ha meg szeretné tudni, hogy bizonyos kérelmek az összes kiszolgálói funkciót igénybe veszik-e, futtassa a SlowLog parancsot az előző bekezdésben leírtak szerint. További információ: Magas CPU-használat / Kiszolgálóbetöltés.
1. Volt-e más olyan esemény az ügyféloldalon, ami hálózati blip-et okozhatott volna? A gyakori események közé tartozik: az ügyfélpéldányok számának felfelé vagy lefelé méretezése, az ügyfél új verziójának üzembe helyezése vagy az automatikus skálázás engedélyezve. A tesztelés során azt találtuk, hogy az automatikus skálázás vagy a felskálázás vagy a fel- és leskálázás néhány másodpercre elveszhet a kimenő hálózati kapcsolat. StackExchange.Redis kód rugalmas az ilyen események és újracsatlakozik. Az újracsatlakozás során a várólistában lévő kérelmek időout-el.
1. Volt-e egy nagy kérés megelőző több kis kéréseket a gyorsítótárba, hogy az időtúljelentkezés? A `qs` hibaüzenetben szereplő paraméter megmutatja, hogy hány kérelmet küldött az ügyfél a kiszolgálónak, de még nem dolgozta fel a választ. Ez az érték folyamatosan növekszik, mert a StackExchange.Redis egyetlen TCP-kapcsolatot használ, és egyszerre csak egy választ tud olvasni. Annak ellenére, hogy az első művelet időtúlodott, nem akadályozza meg, hogy több adatot küldjenek a kiszolgálóra vagy a kiszolgálóról. Más kérelmek blokkolva lesz, amíg a nagy kérés befejeződött, és időtúlokra vezethet. Az egyik megoldás az időmegosztások esélyének minimalizálása annak biztosításával, hogy a gyorsítótár elég nagy a számítási feladatokhoz, és a nagy értékeket kisebb adattömbökre osztja fel. Egy másik lehetséges megoldás `ConnectionMultiplexer` az, hogy egy objektumkészlet et `ConnectionMultiplexer` használ az ügyfélben, és válassza ki a legkevésbé betöltött új kérelem küldésekor. Több kapcsolatobjektum közötti betöltésnek meg kell akadályoznia, hogy egyetlen időtúllépés más kérelmek et is időtúllépésre késztetjen.
1. A használata esetén `RedisSessionStateProvider`győződjön meg arról, hogy helyesen állította be az újrapróbálkozási időtúlot. `retryTimeoutInMilliseconds`magasabbnak kell `operationTimeoutInMilliseconds`lennie, mint , ellenkező esetben nem történik újrapróbálkozás. A következő `retryTimeoutInMilliseconds` példában 3000.In the following example is set to 3000. További információt [a ASP.NET Azure-gyorsítótár redis-hez](cache-aspnet-session-state-provider.md) és [a munkamenetállapot-szolgáltató és a kimeneti gyorsítótár-szolgáltató konfigurációs paramétereinek használata című témakörben talál.](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)

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

1. Ellenőrizze a memóriahasználatot az Azure Cache `Used Memory`for Redis kiszolgálón [figyeléssel](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` és . Ha egy kilakoltatási házirend van érvényben, a `Used_Memory` Redis elindítja a kulcsok kizárását, amikor eléri a gyorsítótár méretét. Ideális esetben `Used Memory RSS` csak valamivel `Used memory`magasabblehet, mint . A nagy különbség azt jelenti, hogy a memória töredezettsége (belső vagy külső). Ha `Used Memory RSS` kevesebb, `Used Memory`mint , az azt jelenti, hogy a gyorsítótár memóriájának egy részét az operációs rendszer felcserélte. Ha ez a csere történik, akkor számíthat néhány jelentős késések. Mivel a Redis nem szabályozhatja, hogy a lefoglalások hogyan `Used Memory RSS` vannak leképezve a memórialapokra, a magas gyakran a memóriahasználat kiugrásának eredménye. Amikor a Redis kiszolgáló memóriát szabadít fel, a helyosztó átveszi a memóriát, de lehet, hogy visszaadja a memóriát a rendszernek. Lehet, hogy eltérés van `Used Memory` az operációs rendszer által jelentett érték és memóriafelhasználás között. Lehet, hogy a Redis használta és kiadta a memóriát, de nem adta vissza a rendszernek. A memóriaproblémák csökkentése érdekében az alábbi lépéseket teheti meg:

   - Frissítse a gyorsítótárat nagyobb méretre, hogy ne fusson a rendszer memóriakorlátai ellen.
   - Állítsa be a lejárati időt a kulcsokon, hogy a régebbi értékek proaktív módon kilegyenek zárva.
   - A `used_memory_rss` gyorsítótár-metrika figyelése. Amikor ez az érték megközelíti a gyorsítótár méretét, valószínűleg teljesítményproblémákat fog látni. Az adatok elosztása több szegmensek között, ha prémium szintű gyorsítótárat használ, vagy nagyobb gyorsítótár-méretre frissít.

   További információ: [Memory pressure on Redis server](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis ügyféloldali hibáinak elhárítása](cache-troubleshoot-client.md)
- [Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása](cache-troubleshoot-server.md)
- [Hogyan tesztelhetem és tesztelhetem a gyorsítótár teljesítményét?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Az Azure-gyorsítótár figyelése a Redis számára](cache-how-to-monitor.md)
