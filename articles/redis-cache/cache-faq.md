---
title: "Azure Redis Cache – gyakori kérdések |} Microsoft Docs"
description: "A kérdésekre adott válaszokat gyakori kérdésekre, mintákat és ajánlott eljárások Azure Redis Cache megismerése"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: sdanie
ms.openlocfilehash: dcabdb789489af1996276d8838afde410473738d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-redis-cache-faq"></a>Azure Redis Cache – Gyakori kérdések
Azure Redis Cache a gyakori kérdéseket, a mintákat és ajánlott eljárások a válaszok megismerése.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha a fentiekben itt nem választ?
Ha kérdését itt nem látható, ossza meg velünk, és segítünk talált választ.

* Fel kérdést a megjegyzéseket, ez a GYIK végén, és az Azure Cache csoportját, és a Közösség más tagjaival kapcsolatos cikkben bevonásához.
* Egy szélesebb körű célközönség eléréséhez a tehet fel kérdést a [Azure Cache MSDN fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) és az Azure Cache csoportját, és a Közösség más tagjai bevonásához.
* Ha engedélyezni szeretné egy szolgáltatás kérése, elküldheti a kérelmek és ötleteket a [Azure Redis Cache User Voice](https://feedback.azure.com/forums/169382-cache).
* E-mailt is elküldhet a nekünk az [Azure Cache külső visszajelzés](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Azure Redis Cache alapjai
Az ebben a szakaszban – gyakori kérdések néhány Azure Redis Cache alapjait fedik le.

* [Mi az Azure Redis Cache?](#what-is-azure-redis-cache)
* [Hogyan lehet kezdjem el az Azure Redis Cache?](#how-can-i-get-started-with-azure-redis-cache)

A következő gyakori kérdések főbb fogalmait és kifejezéseit és az Azure Redis Cache kapcsolatos kérdéseket, és az egyéb gyakran ismételt kérdések szakaszokban válaszok.

* [Melyik Redis Cache-ajánlatot és -méretet használjam?](#what-redis-cache-offering-and-size-should-i-use)
* [Melyik Redis gyorsítótár-ügyfelek használható?](#what-redis-cache-clients-can-i-use)
* [Az Azure Redis Cache egy helyi emulátor van?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hogyan figyelhetek állapotának és teljesítményének a gyorsítótár?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Tervezési – gyakori kérdések
* [Melyik Redis Cache-ajánlatot és -méretet használjam?](#what-redis-cache-offering-and-size-should-i-use)
* [Azure Redis Cache-teljesítmény](#azure-redis-cache-performance)
* [Milyen régióban I, keresse meg a gyorsítótár?](#in-what-region-should-i-locate-my-cache)
* [Hogyan vagyok fizetni az Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
* [Azure Redis Cache használható Azure Government felhő, Azure Kína felhő vagy a Microsoft Azure Németország?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Fejlesztési – gyakori kérdések
* [Mit hajtsa végre a StackExchange.Redis-konfigurációs beállítások?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Melyik Redis gyorsítótár-ügyfelek használható?](#what-redis-cache-clients-can-i-use)
* [Az Azure Redis Cache egy helyi emulátor van?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hogyan futtathatom Redis parancsok?](#how-can-i-run-redis-commands)
* [Miért nem rendelkezik Azure Redis Cache az MSDN osztály kódtár – dokumentáció néhányat a többi Azure-szolgáltatásokhoz hasonlóan?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Használhatok Azure Redis Cache PHP munkamenet-gyorsítótár?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Mik azok a Redis-adatbázisok?](#what-are-redis-databases)

## <a name="security-faqs"></a>Biztonság – gyakori kérdések
* [Ha engedélyezze a nem SSL port való csatlakozáshoz?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Éles – gyakori kérdések
* [Mik azok a termelési tanácsokat?](#what-are-some-production-best-practices)
* [Melyek azok a szempontok közös Redis-parancsok használata esetén?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hogyan elvégez egy teljesítménytesztet és a gyorsítótár teljesítményének a tesztelésére?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Fontos tudnivalók a szálkészlet növekedési](#important-details-about-threadpool-growth)
* [Engedélyezze a kiszolgáló GC StackExchange.Redis használata esetén további átviteli sebesség eléréséhez az ügyfélen](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Kapcsolatok körül teljesítménnyel kapcsolatos szempontok](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Figyelés és hibaelhárítás – gyakori kérdések
Ebben a szakaszban a gyakran ismételt kérdéseket fedik le a közös figyeléshez és hibaelhárításhoz kérdéseket. Figyelés és hibaelhárítás céljából az Azure Redis Cache példány kapcsolatos további információkért lásd: [figyelése Azure Redis Cache](cache-how-to-monitor.md) és [hibaelhárítása az Azure Redis Cache](cache-how-to-troubleshoot.md).

* [Hogyan figyelhetek állapotának és teljesítményének a gyorsítótár?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Miért jelenik meg időtúllépések?](#why-am-i-seeing-timeouts)
* [Miért megszakadt az ügyfél a gyorsítótár?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Előzetes Cache-ajánlatot – gyakori kérdések
* [Mely Azure Cache-ajánlatot az megfelelő a számomra?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>Mi az az Azure Redis Cache?
Azure Redis Cache alapul a népszerű nyílt forráskódú [Redis gyorsítótár](http://redis.io). Ez hozzáférést biztosít a biztonságos, dedikált Redis gyorsítótár, a Microsoft által felügyelt, és elérhető bármely alkalmazásból az Azure-ban. Részletesebb áttekintéséért lásd: a [Azure Redis Cache](https://azure.microsoft.com/services/cache/) az Azure.com-on a termék oldalát.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Hogyan lehet kezdjem el az Azure Redis Cache?
Ismerkedés az Azure Redis Cache számos módja van.

* Megtekintheti az elérhető oktatóprogramok valamelyikét [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), és [Python](cache-python-get-started.md).
* Figyelheti az [Build nagy teljesítményű alkalmazások használata a Microsoft Azure Redis Cache hogyan](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Megtekintheti az ügyfelek, amelyek megfelelnek a fejlesztési nyelvét a használata a Redis-projektet az ügyfél dokumentációjában olvashatók. Nincsenek sok használható az Azure Redis Cache Redis-ügyfelek. Egy Redis-ügyfelek listája, [http://redis.io/clients](http://redis.io/clients).

Ha még nem rendelkezik Azure-fiókot, akkor a következőket teheti:

* [Nyisson egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a keretét el is használta, továbbra is megtarthatja a fiókot, és használhatja az ingyenes szolgáltatásokat és lehetőségeket.
* [Aktiválja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Melyik Redis Cache-ajánlatot és -méretet használjam?
Minden Azure Redis Cache-ajánlatot biztosít a különböző szintű **mérete**, **sávszélesség**, **magas rendelkezésre állású**, és **SLA** beállítások.

A Cache-ajánlatot kiválasztására vonatkozó szempontok a következők:

* **Memória**: az alapszintű és a Standard rétegek ajánlat 250 MB-ra – 53 GB. A prémium szintű 530 GB kínál. További információkért lásd: [Azure Redis Cache szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/cache/).
* **Hálózati teljesítményt**: Ha egy munkaterhelés nagy teljesítményt igénylő, a prémium szintű Standard vagy Basic képest nagyobb sávszélességet kínál. Minden egyes réteg belül nagyobb méretű gyorsítótárak is nagyobb sávszélességet az alapul szolgáló virtuális gép, amelyen a gyorsítótár miatt. Tekintse meg a [tábla következő](#cache-performance) további információt.
* **Átviteli sebesség**: A prémium szintű kínál, a rendelkezésre álló maximális átviteli sebesség. Ha a gyorsítótár-kiszolgáló vagy az ügyfél eléri a sávszélesség korlátja, ügyféloldali időtúllépések jelenhet meg. További információkért lásd az alábbi táblázatot.
* **Magas rendelkezésre állás/SLA**: Azure Redis Cache garantálja, hogy Standard vagy prémium gyorsítótár rendelkezésre áll legalább 99,9 %-ában. Az SLA-t kapcsolatos további információkért lásd: [Azure Redis Cache szolgáltatás díjszabása](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Az SLA-t és a gyorsítótár végpontok vonatkozik. A szolgáltatói szerződés nem biztosít védelmet az adatvesztéssel szemben. Azt javasoljuk, hogy a prémium csomagban a Redis-adatok megőrzését szolgáltatás használatával növelje az adatvesztéssel szembeni hibatűrést.
* **Redis-adatmegőrzés**: A prémium csomagban lehetővé teszi a gyorsítótárazott adatokat az Azure Storage-fiók megőrzéséhez. A Basic vagy Standard-gyorsítótár összes adatot csak memóriában van tárolva. Ha nincsenek alapul szolgáló infrastrukturális problémára nincs esetleges adatvesztés lehet. Azt javasoljuk, hogy a prémium csomagban a Redis-adatok megőrzését szolgáltatás használatával növelje az adatvesztéssel szembeni hibatűrést. Azure Redis Cache Rekordadatbázis és AOF (hamarosan elérhető) lehetőséget kínál a Redis-adatmegőrzés. További információkért lásd: [konfigurálása prémium szintű Azure Redis Cache megőrzését](cache-how-to-premium-persistence.md).
* **Redis-fürt**: létrehozásához gyorsítótárazza a 53 GB-nál nagyobb, vagy részekre bonthatók az adatok több Redis-csomópont között, fürtözéssel Redis, elérhető, a prémium tarifacsomagra. Minden csomópont a magas rendelkezésre állású gyorsítótár elsődleges vagy replika párból áll. További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-clustering.md) (Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
* **Fokozott biztonsági és hálózati elkülönítési**: Azure virtuális hálózatot (VNET) központi telepítés biztosít magasabb védelmet és elszigeteltséget az Azure Redis Cache, valamint a alhálózatok, hozzáférés-vezérlési házirendeket, és további egyéb szolgáltatások elérésének korlátozása. További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-vnet.md) (Virtuális hálózat támogatásának konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
* **Konfigurálja a Redis**: a Standard és a prémium rétegek, konfigurálhatja a Redis kulcstérértesítések használatával az értesítésekhez.
* **Ügyfélkapcsolatok maximális számát**: A prémium csomagban kínál, amely csatlakozni tudna a Redis, a nagyobb méretű gyorsítótárak esetében kapcsolatok magasabb azonosítószámot ügyfelek maximális számát. További információkért lásd: [Azure Redis Cache árképzési](https://azure.microsoft.com/pricing/details/cache/).
* **A Redis Server Core dedikált**: a prémium szinten lévő összes gyorsítótár méretének rendelkezik egy dedikált core Redis. A Basic vagy Standard rétegek, a C1 méretét és az újabb rendelkezik egy dedikált core Redis-kiszolgáló.
* **A redis az egyszálas** , kettőnél több maggal rendelkező nem biztosít további előnye képest csak két maggal rendelkező, de nagyobb Virtuálisgép-méretek általában rendelkeznek nagyobb sávszélesség-nál kisebb méretű. Ha a gyorsítótár-kiszolgáló vagy az ügyfél eléri a sávszélesség korlátja, majd kapni az ügyféloldalon időtúllépések.
* **Teljesítménnyel kapcsolatos fejlesztések**: gyorsítótárakat a prémium tarifacsomagra vannak telepítve, amely gyorsabb processzorral rendelkezik hardver Basic vagy Standard csomagra képest nagyobb teljesítményt nyújtó. Prémium szintű réteghez gyorsítótárainak rendelkezik nagyobb átviteli teljesítményt és kisebb késések fordulnak elő.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Azure Redis Cache-teljesítmény
Az alábbi táblázat a maximális sávszélesség értéke különböző méretű standard tesztelése során, és prémium gyorsítótárazza a használatával `redis-benchmark.exe` egy infrastruktúra-szolgáltatási virtuális gépről az Azure Redis Cache végpont ellen. 

>[!NOTE] 
>Ezeket az értékeket nem garantált, és ezek nem SLA-számok, de az tipikus van. Be kell tölteni a saját állapítsa meg a megfelelő gyorsítótár méretét, az alkalmazás-alkalmazás tesztelése.
>
>

Ebből a táblázatból a következő következtetéseket is azt:

* A gyorsítótárak, amelyek azonos méretűnek adatátviteli sebességét értéke magasabb a prémium tarifacsomagra képest Standard csomagra. Például egy 6 GB-os gyorsítótárral, P1 átviteli sebességgel esetén 180 000 RPS a C3 49,000 képest.
* A redis gyorsítótárral fürtszolgáltatás átviteli növekszik lineárisan a szilánkok (csomópontok) a fürt számának növeléséhez. Például, ha a 10 szilánkok P4 fürt létrehozása, a rendelkezésre álló átviteli akkor 400000 * 10 = 4 millió RPS.
* A kulcs mérete nagyobb átviteli sebesség értéke magasabb a prémium tarifacsomagra képest Standard csomagra.

| Tarifacsomag | Méret | Processzormagok | Rendelkezésre álló sávszélesség | 1 KB-os méret |
| --- | --- | --- | --- | --- |
| **Standard gyorsítótár mérete** | | |**Megabit / másodperc (Mb/s) vagy megabájt / másodperc (MB/s)** |**Kérelmek / másodperc (RPS)** |
| C0 |250 MB |Közös |5 / 0.625 |600 |
| C1 |1 GB |1 |100 / 12.5 |12,200 |
| C2 |2.5 GB |2 |200 / 25 |24,000 |
| C3 |6 GB |4 |400 / 50 |49,000 |
| C4 |13 GB |2 |500 / 62.5 |61,000 |
| C5 |26 GB |4 |1,000 / 125 |115,000 |
| C6 |53 GB |8 |2,000 / 250 |150,000 |
| **Prémium szintű gyorsítótár mérete** | |**CPU-magokat shard száma** | **Megabit / másodperc (Mb/s) vagy megabájt / másodperc (MB/s)** |**Kérelmek / másodperc (RPS) shard száma** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |
| P2 |13 GB |4 |3,000 / 375 |360,000 |
| P3 |26 GB |4 |3,000 / 375 |360,000 |
| P4 |53 GB |8 |6,000 / 750 |400,000 |

A Redis-eszközök például letöltésével kapcsolatban `redis-benchmark.exe`, tekintse meg a [hogyan futtathatom Redis parancsok?](#cache-commands) szakasz.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Milyen régióban I, keresse meg a gyorsítótár?
A legjobb teljesítmény és a legkisebb mértékű késleltetést keresse meg az Azure Redis Cache az ügyfélalkalmazás ugyanabban a régióban.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Hogyan vagyok fizetni az Azure Redis Cache?
Az Azure Redis Cache árképzési [Itt](https://azure.microsoft.com/pricing/details/cache/). A tarifákat tartalmazó oldalt sorolja fel, mint egy óradíjat árképzési. Gyorsítótárak számlázása, hogy a gyorsítótár jön létre, amíg a gyorsítótár törölnek perc alapon. Nincs lehetőség az leállítása és a gyorsítótár számlázási felfüggesztése.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Azure Redis Cache használható Azure Government felhő, Azure Kína felhő vagy a Microsoft Azure Németország?
Igen, Azure Redis Cache nem áll rendelkezésre Azure Government felhő, az Azure Kína felhő és a Microsoft Azure Németország. Az URL-címek elérésére, és az Azure Redis Cache kezelése az Azure nyilvános Felhőjében összehasonlítja a felhők különböznek. 

| Felhő   | A Redis DNS-utótagja            |
|---------|---------------------------------|
| Nyilvános  | *. redis.cache.windows.net       |
| USA-beli államigazgatás  | *. redis.cache.usgovcloudapi.net |
| Németország | *. redis.cache.cloudapi.de       |
| Kína   | *. redis.cache.chinacloudapi.cn  |

Az Azure Redis Cache használatának és egyéb szempontjai további információkért tekintse meg a következőket.

- [Az Azure Government adatbázisok - Azure Redis gyorsítótár](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Az Azure Kína felhő - Azure Redis gyorsítótár](https://www.azure.cn/documentation/services/redis-cache/)
- [A Microsoft Azure-Németország](https://azure.microsoft.com/overview/clouds/germany/)

Az Azure Redis Cache használata Azure Government felhő, az Azure Kína felhőben és a Microsoft Azure Németország PowerShell információkért lásd: [és a többi felhőből - az Azure Redis Cache PowerShell összekapcsolása](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Mit hajtsa végre a StackExchange.Redis-konfigurációs beállítások?
StackExchange.Redis számos lehetőség van. Ez a szakasz az általános beállítások némelyike beszél. Részletesebb információ a StackExchange.Redis lehetőségekről, lásd: [StackExchange.Redis konfigurációs](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Leírás | Ajánlás |
| --- | --- | --- |
| AbortOnConnectFail |Ha true értéke esetén a kapcsolat nem csatlakozik újra egy hálózati hiba után. |Állítsa hamis értékre, és lehetővé teszik a StackExchange.Redis újra automatikusan. |
| ConnectRetry |Ismételje meg a kapcsolódási kísérletek során kezdeti ennyiszer csatlakozzon. |Lásd a következő útmutatást. |
| ConnectTimeout |Időkorlátot adja meg az ms connect műveletek. |Lásd a következő útmutatást. |

Az alapértelmezett értékeket, az ügyfél általában elegendő. A beállítások alapján a munkaterhelés úgy finomhangolhatja.

* **Újbóli próbálkozások**
  * ConnectRetry és ConnectTimeout az általános útmutatást, hogy gyors sikertelen, és próbálja megismételni a műveletet. Ez az útmutató a számítási feladatok és mennyi idő átlagos azt az ügyfél a Redis-parancsot, és választ kapnak vesz alapul.
  * Lehetővé teszik a StackExchange.Redis automatikusan újra kapcsolat állapotának ellenőrzése és újracsatlakozás saját maga helyett. **Kerülje a ConnectionMultiplexer.IsConnected tulajdonság**.
  * Snowballing - néha futtathatja, ha vannak újrapróbálkozás és a próbálkozások snowball problémát, és soha nem helyreáll. Snowballing esetben érdemes, exponenciális leállítási újrapróbálkozási algoritmust használ, a [ismételje meg az általános útmutatást](../best-practices-retry-general.md) közzétett Microsoft Patterns & eljárások csoport.
* **Időkorlát-értékei**
  * Érdemes lehet a munkaterhelés, és ennek megfelelően állíthatja be azokat az értékeket. Ha nagy értékeket tárolja, állítsa be az időtúllépés értéke.
  * Állítsa be `AbortOnConnectFail` hamis értéket, és lehetővé StackExchange.Redis meg újra.
  * Használjon egyetlen ConnectionMultiplexer példányt az alkalmazáshoz. Egy LazyConnection hozhat létre a Connection tulajdonság által visszaadott egyetlen példányt, ahogy az [csatlakozás a gyorsítótárhoz, a ConnectionMultiplexer osztály használatával](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Állítsa be a `ConnectionMultiplexer.ClientName` app egyedi példányevet diagnosztikai célokra tulajdonságot.
  * Használjon több `ConnectionMultiplexer` egyéni munkaterhelések példányait.
      * Ha az alkalmazás különböző terhelés kövesse ezt a modellt. Példa:
      * Egy multiplexer nagy kulcsok kezelésére is.
      * Egy kis kulcsok kapcsolatos multiplexer lehet.
      * Kapcsolat időtúllépése különböző értékeinek beállítása, és minden ConnectionMultiplexer, amelyekkel az újrapróbálkozási logika.
      * Állítsa be a `ClientName` minden egyes tulajdonság multiplexer diagnosztika számára.
      * Ez az útmutató azt eredményezheti, hogy több / késés zökkenőmentes `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Melyik Redis gyorsítótár-ügyfelek használható?
Redis kapcsolatos kiváló dolog, hogy vannak-e számos különböző fejlesztési nyelveket támogató ügyfelek számát. Az ügyfelek aktuális listájának megtekintéséhez lásd: [Redis-ügyfelek](http://redis.io/clients). Oktatóprogramot kínál, amelyek több különböző nyelv és ügyfelek, lásd: [Azure Redis Cache használata](cache-dotnet-how-to-use-azure-redis-cache.md) , és kattintson a kívánt nyelvet a a nyelvi kapcsoló, a cikk tetején.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Az Azure Redis Cache egy helyi emulátor van?
Az Azure Redis Cache nem helyi emulátor van, de a redis-server.exe MSOpenTech verzióját is futtathatja a [parancssori eszközök Redis](https://github.com/MSOpenTech/redis/releases/) a helyi számítógéphez, és csatlakozni, és máris beolvashatja a hasonló felhasználói élményt nyújtja a helyi gyorsítótár emulátorának az alábbi példa szerint:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Konfigurálhatja a [redis.conf dokumentációját](http://redis.io/topics/config) fájlt, hogy jobban megfeleljen a [alapértelmezett gyorsítótár beállításai](cache-configure.md#default-redis-server-configuration) számára az online Azure Redis Cache igény.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hogyan futtathatom Redis parancsok?
Akkor használhatja a parancsok megtalálható a [parancsok Redis](http://redis.io/commands#) megtalálható a parancsok kivételével [parancsok nem támogatott az Azure Redis Cache Redis](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Több lehetőség közül választhat a Redis-parancsok futtatásához.

* Ha egy Standard vagy prémium gyorsítótár, a Redis-parancsok segítségével futtathatja a [Redis konzol](cache-configure.md#redis-console). A Redis-konzol segítségével egy biztonságos Redis-parancsok futtatása az Azure portálon.
* A Redis parancssori eszközöket használhatja. Is használhatja őket, hajtsa végre a következő lépéseket:
* Töltse le a [parancssori eszközök Redis](https://github.com/MSOpenTech/redis/releases/).
* Kapcsolódás a gyorsítótár `redis-cli.exe`. Adjon át a gyorsítótár-végpontot a a -h kapcsoló és a kulcs - a a következő példában látható módon:
* `redis-cli -h <your cache="" name="">
  .redis.cache.windows.net -a <key>
  `

> [!NOTE]
> A Redis parancssori eszközök sem működik együtt az SSL-port, de egy segédprogramot használhatja például a `stunnel` való biztonságos kapcsolódás az eszközök az SSL-port a riasztásban megjelenő utasításokat követve a [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis Preview Kiadás](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogbejegyzést.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Miért nem rendelkezik Azure Redis Cache az MSDN osztály kódtár – dokumentáció néhányat a többi Azure-szolgáltatásokhoz hasonlóan?
Microsoft Azure Redis Cache Redis Cache népszerű nyílt forráskódú alapul, és számos elérhető legyen [Redis-ügyfelek](http://redis.io/clients) számos programozási nyelv. Minden ügyfél rendelkezik saját API-t, hogy a Redis cache példány történő hív [parancsok Redis](http://redis.io/commands).

Mivel egyes ügyfelek különböző, nem egy központosított osztályhivatkozást az MSDN webhelyen van, és minden ügyfél fenn a saját ismertető dokumentációjában. A referenciadokumentációt kívül számos oktatóprogram bemutatja, hogyan Ismerkedés az Azure Redis Cache segítségével több különböző nyelvet és a gyorsítótárazási ügyfelek számára. Ezek az oktatóanyagok szeretne használni, lásd: [Azure Redis Cache használata](cache-dotnet-how-to-use-azure-redis-cache.md) , és kattintson a kívánt nyelvet a a nyelvi kapcsoló, a cikk tetején.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Használhatok Azure Redis Cache PHP munkamenet-gyorsítótár?
Igen, a PHP munkamenet-gyorsítótár Azure Redis Cache használatához adja meg a kapcsolati karakterláncot az Azure Redis Cache példány `session.save_path`.

> [!IMPORTANT]
> Ha a Azure Redis Cache-t egy PHP munkamenet-gyorsítótár, a URL-címet kell kódolni a biztonsági kulcs használatával kapcsolódik a gyorsítótár, a következő példában látható módon:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Ha a kulcs nem kódolt URL-cím, egy üzenet, például a kivétel jelenhet meg:`Failed to parse session.save_path`
>
>

Redis gyorsítótár használatával, a PHP munkamenet-gyorsítótár PhpRedis-ügyféllel kapcsolatos további információkért lásd: [PHP munkamenet kezelő](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Mik azok a Redis-adatbázisok?

A redis-adatbázisok a következők csak logikai elkülönítése belül a Redis-példányt. A gyorsítótár-memória megosztott az adatbázisok és a tényleges memória egy adott adatbázisnak fogyasztásának függ, hogy az adatbázis tárolt kulcsok/értékek között. Egy C6 gyorsítótár például 53 GB memóriával rendelkezik. Választhatja az összes 53 GB üzembe egy adatbázis, vagy feloszthatja az több adatbázis közötti. 

> [!NOTE]
> Prémium szintű Azure Redis Cache engedélyezve fürtözési használatakor csak adatbázis 0 érhető el. Ez a korlátozás egy belső Redis korlátozás, és nem csak az Azure Redis Cache. További információkért lásd: [van saját ügyfélalkalmazás fürtszolgáltatással használandó végezze el a módosításokat?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Ha engedélyezze a nem SSL port való csatlakozáshoz?
Redis kiszolgáló nem támogatja natív módon SSL azonban Azure Redis Cache nem. Ha az Azure Redis Cache csatlakozik, és az ügyfél támogatja az SSL, például a StackExchange.Redis, akkor SSL kell használnia.

>[!NOTE]
>A nem SSL port az új Azure Redis Cache példány alapértelmezés szerint le van tiltva. Ha az ügyfél nem támogatja az SSL, akkor a nem SSL port a riasztásban megjelenő utasításokat követve engedélyeznie kell a [hozzáférési portok](cache-configure.md#access-ports) szakasza a [gyorsítótár konfigurálása az Azure Redis Cache](cache-configure.md) cikk.
>
>

Eszközök például a redis `redis-cli` sem működik együtt az SSL-port, de egy segédprogramot használhatja például a `stunnel` való biztonságos kapcsolódás az eszközök az SSL-port a riasztásban megjelenő utasításokat követve a [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis Előzetes kiadás](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogbejegyzést.

A Redis eszközök letöltése, lásd: a [hogyan futtathatom Redis parancsok?](#cache-commands) szakasz.

### <a name="what-are-some-production-best-practices"></a>Mik azok a termelési tanácsokat?
* [StackExchange.Redis gyakorlati tanácsok](#stackexchangeredis-best-practices)
* [Konfigurációs és fogalmak](#configuration-and-concepts)
* [Teljesítménytesztelés](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis gyakorlati tanácsok
* Állítsa be `AbortConnect` hamis, majd lehetővé teszik az automatikus újracsatlakozás ConnectionMultiplexer. [Itt talál információt](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Felhasználhatja a ConnectionMultiplexer – ne hozzon létre egy új egyes kérésekre vonatkozóan. A `Lazy<ConnectionMultiplexer>` mintát [itt látható](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) ajánlott.
* Redis works legjobban a kisebb értékek, ezért fontolja meg több kulcsokból nagyobb adatok aprító. A [Redis ismertető](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb nagy tekinthető. Olvasási [Ez a cikk](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) egy példa probléma, amely nagy értékek okozhatja.
* Konfigurálja a [ThreadPool beállítások](#important-details-about-threadpool-growth) időtúllépések elkerülése érdekében.
* Legalább 5 másodperces az alapértelmezett connectTimeout használni. Ezt az időközt ad StackExchange.Redis elegendő időt a kapcsolat esetén a hálózati blip újbóli létrehozásához.
* Vegye figyelembe a különböző műveletek futtatja társított teljesítmény költségek. Például a `KEYS` parancs O(n) művelet, és el kell kerülni. A [redis.io hely](http://redis.io/commands/) idő összetettségét minden művelethez, hogy az támogatja-e körül részleteket tartalmaz. Kattintson az egyes parancsot minden művelethez összetettségét.

#### <a name="configuration-and-concepts"></a>Konfigurációs és fogalmak
* Használja a Standard vagy prémium csomagban éles rendszerek esetén. Az alapszintű rétegben egycsomópontos rendszer nincs adatreplikáció és nem az SLA-t. Ezenkívül legalább egy C1-gyorsítótárazás használatára. C0 gyorsítótárak az egyszerű szolgáltatásának fejlesztési/tesztelési általában használják.
* Ne feledje, hogy a Redis egy **memórián belüli** adattár. Olvasási [Ez a cikk](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , hogy tisztában lehet a esetben adatvesztés következhet be.
* A rendszer fejlesztése úgy, hogy a kapcsolat blips is képes kezelni [javítását és a feladatátvétel miatt](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teljesítménytesztelés
* A Start `redis-benchmark.exe` való betekintést nyerhet abba, lehetséges átviteli saját telj írása előtt ellenőrzi. Mivel `redis-benchmark` nem támogatja az SSL, meg kell [az Azure portálon keresztül a nem SSL port engedélyezéséhez](cache-configure.md#access-ports) a vizsgálat futtatása előtt. Tekintse meg a [hogyan elvégez egy teljesítménytesztet és a gyorsítótár teljesítményének a tesztelésére?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Az ügyfél teszteléshez használt virtuális gép és a Redis cache példány ugyanabban a régióban kell lennie.
* Azt javasoljuk, Dv2 Virtuálisgép-sorozat a ügyfél még jobban hardverrel rendelkezik, és adjon a legjobb eredmények elérése érdekében.
* Ellenőrizze, hogy az ügyfél úgy dönt, virtuális gép rendelkezik legalább annyi számítási és sávszélesség-funkció, a gyorsítótár teszteli.
* Ha a Windows engedélyezi a VRSS az ügyfélszámítógépen. [Itt talál információt](https://technet.microsoft.com/library/dn383582.aspx).
* Mert jobb hardveren futtatnak a Processzor- és hálózati jobban rendelkezik prémium szint Redis példányok hálózati késés és a teljesítményt.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Melyek azok a szempontok közös Redis-parancsok használata esetén?
* Ne futtassa a bizonyos Redis-parancsok egy hosszú ideig tarthat, anélkül, hogy ezek a parancsok hatásának ismertetése.
  * Például ne futtassa a [kulcsok](http://redis.io/commands/keys) parancs éles környezetben, attól függően, hogy a kulcsok száma vissza hosszú ideig eltarthat. A redis egy egyszálas kiszolgáló és egy időben feldolgozza a parancsokat. Ha más parancsok után kulcsok, akkor nem fogja feldolgozni amíg Redis feldolgozza a kulcsok parancsot. A [redis.io hely](http://redis.io/commands/) idő összetettségét minden művelethez, hogy az támogatja-e körül részleteket tartalmaz. Kattintson az egyes parancsot minden művelethez összetettségét.
* Kulcsméretekkel - érdemes használni kis kulcs/érték vagy nagy kulcs/érték? Általában a forgatókönyv függ. Adott esetben szükség van a nagyobb kulcsok, ha a ConnectionTimeout értékének beállítása és próbálja meg újra az értékeket, és állítsa be az újrapróbálkozási logika. A Redis-kiszolgáló szempontjából a kisebb értékek megfigyelhető jobb teljesítményt.
* Ezeket a szempontokat nem jelenti azt, hogy nem tárolja a nagyobb értékek a Redis; az alábbiakat is tisztában kell lennie. Késések nagyobb lesz. Ha egy adatkészletet, amely nagyobb, a másik kisebb, ConnectionMultiplexer több példányt is használhat, minden más-más időkorlát és újrapróbálási érték leírtak szerint konfigurálta az előző [a StackExchange.Redis mire konfigurációs beállítások tegye](#cache-configuration) szakasz.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hogyan elvégez egy teljesítménytesztet és a gyorsítótár teljesítményének a tesztelésére?
* [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát. A mérőszámokat megtekintheti az Azure Portalon, illetve többféle eszközzel is [letöltheti és áttekintheti](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) őket.
* Használhatja a redis-benchmark.exe teszt betöltése a Redis-kiszolgáló.
* Győződjön meg arról, hogy a terhelés tesztelése az ügyfél és a Redis gyorsítótárt ugyanabban a régióban.
* A redis-cli.exe használja, és figyelje a gyorsítótár az információ paranccsal.
* Ha a terhelést okoz nagy a memória töredezettségét, a gyorsítótár méretének meg kell növelheti.
* A Redis eszközök letöltése, lásd: a [hogyan futtathatom Redis parancsok?](#cache-commands) szakasz.

A következő parancsok példát redis-benchmark.exe használatával kell megadni. A pontos eredményeket futtassa az alábbi parancsokat egy virtuális gépről a gyorsítótárat ugyanabban a régióban.

* Egy 1 KB-os hasznos használó teszt futószalagos beállítása kérések

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teszt futószalagos GET kérelmek használatával egy 1 KB-os hasznos.
  Megjegyzés: A készlet tesztelése először gyorsítótár adatokkal való feltöltése a fent látható

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Fontos tudnivalók a szálkészlet növekedési
A CLR-beli ThreadPool kétféle szálak - "Munkavégző" és "I/O végrehajtási Port" (más néven i/o végrehajtási port Megnyitásakor.%n) szálak.

* Munkavégző szál használható többek között a feldolgozás a `Task.Run(…)` vagy `ThreadPool.QueueUserWorkItem(…)` módszerek. Ezek a szálak is használata a CLR különböző összetevői, amikor a háttérszálon a munkahelyi kell elvégezni.
* I/o végrehajtási port Megnyitásakor.%n szálak aszinkron IO történik (pl. beolvasása a hálózati) használja.

A szálkészlet új munkaszálat vagy i/o-végrehajtási szálak az igény szerinti (nélkül biztosít bármely szabályozás) nem éri a "Minimális" beállítást az egyes szál. Alapértelmezés szerint a minimális szálak értékre van állítva a rendszer processzorainak számát.

Ha a meglévő (foglalt) szálak száma találatok a "minimális" szálak számát, a szálkészlet a sebesség, amellyel új szál a egy szál / 500 ezredmásodperc esetében fog szabályozás. Általában Ha a rendszer lekérdezi az i/o végrehajtási port Megnyitásakor.%n szál kellene munka továbbítás, fogja feldolgozni munka nagyon gyorsan. Azonban ha munka kapacitásnövelés több, mint a beállított "Minimális" beállítást, lesz némi késedelemmel feldolgozásakor egy részét, a szálkészlet megvárja-e a két dolog megtörténjen-e.

1. Egy meglévő szál lesz a munkahelyi feldolgozását.
2. Nem létező szál válik 500ms, a szabad, egy új szál jön létre.

Alapvetően azt jelenti, hogy ha foglalt szálak száma nagyobb, mint a minimális szálak, valószínűleg fizet 500ms késleltetés az alkalmazás hálózati forgalom feldolgozása előtt. Azt is fontos megjegyezni, hogy ha egy meglévő szál (mi emlékszem alapján) 15 másodpercnél hosszabb ideig inaktív marad, akkor törlődnek, és ez a ciklus növekedési ütemet, és zsugorodás megismétlésével.

Ha úgy tekintünk, például hibaüzenetet a StackExchange.Redis (build 1.0.450 vagy újabb), látni fogja, hogy most jelenít ThreadPool statisztika (i/o végrehajtási port Megnyitásakor.%n és MUNKAVÉGZŐ részleteit lásd alább).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Az előző példában láthatja, hogy az i/o végrehajtási port Megnyitásakor.%n szál 6 foglalt szálak és a rendszer 4 minimális szálak engedélyezésére van konfigurálva. Ebben az esetben az ügyfél akkor valószínűleg láthatta két 500 ms késések mert 6 > 4.

Vegye figyelembe, hogy StackExchange.Redis időtúllépések is találati, ha az i/o végrehajtási port Megnyitásakor.%n vagy feldolgozó szálak növekedési halmozódni lekérdezi.

### <a name="recommendation"></a>Ajánlás
Ezt az információt kap, Határozottan javasoljuk, hogy az ügyfelek beállítása i/o végrehajtási port Megnyitásakor.%n és MUNKAVÉGZŐ szál minimális értéke nagyobb, mint az alapértelmezett érték. Felállításához nem létezik univerzális útmutatást milyen ezt az értéket kell, mert az adott alkalmazáshoz megfelelő érték túl nagy vagy kis egy másik alkalmazás lesz. Ezt a beállítást is hatással lehet összetett alkalmazásokon, egyéb részeinek teljesítményére, minden ügyfél kell ezt a beállítást, konkrét igényeiknek finomhangolásához. Jó kiindulópontot 200-as vagy 300-at, majd tesztelése, és szükség szerint módosíthatja.

Hogyan kell konfigurálnia a beállítást:

* Az ASP.NET, használja a ["minIoThreads" konfigurációs beállítás] [ "minIoThreads" configuration setting] alatt a `<processModel>` konfigurációs elem a Web.config fájlban. Ha Azure Websitesra belül futtat, akkor ezt a beállítást nincs közzétéve, az a konfigurációs beállításokat. Azonban továbbra is konfigurálhatja ezt a beállítást programozott módon kell (lásd alább) a Application_Start metódus a global.asax.cs.

  > [!NOTE] 
  > A konfigurációs elemben megadott érték egy *-core* beállítást. Például ha egy 4 processzormagos számítógép, és a minIOThreads beállítást kell lennie 200 futásidőben, használhatja `<processModel minIoThreads="50"/>`.
  >

* Az ASP.NET kívül használja a [ThreadPool.SetMinThreads(…)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Engedélyezze a kiszolgáló GC StackExchange.Redis használata esetén további átviteli sebesség eléréséhez az ügyfélen
Kiszolgáló GC engedélyezése az ügyfél optimalizálása, és adja meg a jobb teljesítmény és átviteli StackExchange.Redis használatakor. A GC-kiszolgálóra és az engedélyezéshez további információkért lásd: a következő cikkeket:

* [A globális Katalógus kiszolgáló engedélyezése](https://msdn.microsoft.com/library/ms229357.aspx)
* [A szemétgyűjtés alapjai](https://msdn.microsoft.com/library/ee787088.aspx)
* [A szemétgyűjtés és teljesítmény](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Kapcsolatok körül teljesítménnyel kapcsolatos szempontok

Minden tarifacsomag különböző korlátai ügyfélkapcsolatokat, a memória és a sávszélesség rendelkezik. Miközben lehetővé teszi, hogy minden egyes gyorsítótár méretének *legfeljebb* bizonyos számú kapcsolatok, minden egyes való kapcsolat rendelkezik terhet társítva. Erre példa ilyen terheléssel lehet Processzor- és memóriahasználatról miatt a TLS/SSL-titkosítást. A megadott gyorsítótárméret maximális kapcsolódási korlátja azt feltételezi, hogy egy némileg betöltött gyorsítótár. Ha tölthető be a kapcsolat terhelés *plus* az Ügyfélműveletek terhelése meghaladja a rendszer a kapacitás, a gyorsítótár is kapacitás problémákba ütközhetnek, még akkor is, ha nem lépik túl a gyorsítótár jelenlegi mérete a kapcsolathoz megadott korlátot.

Az egyes rétegekhez különböző kapcsolatok korlátairól további információt lásd: [Azure Redis Cache árképzési](https://azure.microsoft.com/pricing/details/cache/). Kapcsolatok és más alapértelmezett konfigurációkkal kapcsolatos további információkért lásd: [kiszolgálókonfiguráció alapértelmezett Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hogyan figyelhetek állapotának és teljesítményének a gyorsítótár?
A Microsoft Azure Redis Cache példány figyelhető a [Azure-portálon](https://portal.azure.com). Is megtekinthető metrikákat, rögzítheti a kezdőpulton metrikák diagramok, testre szabhatja a dátum és idő tartomány diagramok figyelési, hozzáadása és metrikák eltávolítása a diagramok, és állítson be riasztásokat, bizonyos feltételek teljesülése esetén. További információkért lásd: [figyelő Azure Redis Cache](cache-how-to-monitor.md).

A Redis Cache **erőforrás menü** figyelés és hibaelhárítás céljából a gyorsítótárak eszközöket is tartalmazza.

* **Hibáinak diagnosztizálásához és elhárításához** tájékoztatást ad azokról a gyakori problémák és stratégiák kapcsolatos őket.
* **Erőforrás állapota** az erőforrás figyeli, és jelzi, hogy ha a várt módon fut. Az Azure Resource health szolgáltatással kapcsolatos további információkért lásd: [Azure-erőforrás állapotának áttekintése](../resource-health/resource-health-overview.md).
* **Új támogatási kérelem** lehetőségeket a gyorsítótárhoz támogatási kérést nyithat.

Ezek az eszközök lehetővé teszik az Azure Redis Cache példány állapotának figyelésére, és a gyorsítótárazási alkalmazásokat kezeléséhez nyújt segítséget. További információkért lásd: "Támogatási és hibaelhárítási beállításai" szakaszának [konfigurálása az Azure Redis Cache](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Miért jelenik meg időtúllépések?
Az ügyfél felvegye a Redis használó időtúllépések fordulhat elő. A parancs történő küldésekor a Redis-kiszolgáló, a parancs sorba van, és Redis-kiszolgáló végül szerzi be a parancsot, és végrehajtja a azt. Azonban az ügyfél képes időtúllépés történt a folyamat során, és ha az kivétel nem jelenik meg a hívó oldalon. Időtúllépés problémák elhárításával kapcsolatos további információkért lásd: [ügyféloldali hibaelhárítási](cache-how-to-troubleshoot.md#client-side-troubleshooting) és [StackExchange.Redis időtúllépési kivétel](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Miért megszakadt az ügyfél a gyorsítótár?
Az alábbiakban néhány általános oka a gyorsítótár kapcsolata megszakad.

* Ügyféloldali okok
  * Az ügyfélalkalmazás lett újratelepítése.
  * Az ügyfélalkalmazás egy skálázási műveletet végrehajtani.
    * Cloud Services vagy a Web Apps esetén ezt okozhatja az automatikus méretezés.
  * A hálózati réteg ügyféloldali megváltozott.
  * Átmeneti hiba történt az ügyfél vagy a hálózati csomópontok az ügyfél és a kiszolgáló között.
  * A sávszélesség küszöbérték-határnak születtek.
  * CPU kötött műveletek végrehajtása túl sokáig tartott.
* Kiszolgálóoldali okok
  * A szabványos gyorsítótár kínál az Azure Redis Cache service kezdeményezett feladatátvétel az elsődleges csomópont a másodlagos csomópontra.
  * Azure lett javítását a példány, ha a gyorsítótár telepítették
    * A Redis-kiszolgáló frissítések vagy általános virtuális gép karbantartási is lehet.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Melyik Azure Gyorsítótárat használjam?
> [!IMPORTANT]
> Előző év adott [közlemény](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), Azure Managed Cache Service és az Azure szerepköralapú gyorsítótár szolgáltatás **rendelkezik visszavontuk** 2016 November 30. Azt javasoljuk, hogy használjon [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Áttelepítésével kapcsolatban további információkért lásd: [áttelepítése Managed Cache Service az Azure Redis Cache](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Azure Redis Cache
Azure Redis Cache általánosan elérhető az 53 GB méretű és szolgáltatásiszint-szerződésben garantált 99,9 %-os rendelkezésre állással. Az új [prémium csomagban](cache-premium-tier-intro.md) mérete legfeljebb kínál 530 GB és a fürtszolgáltatás, a virtuális hálózat és az adatmegőrzést, a 99,9 %-os SLA-t.

Azure Redis Cache segítségével a felhasználók biztonságos, dedikált Redis gyorsítótárat használnak, a Microsoft kezeli. Az ajánlat kap a gazdag szolgáltatáskészlet és az ökoszisztémákhoz Redis, megbízható üzemeltetéséhez kezelése és a Microsoft által biztosított ki.

Hagyományos gyorsítótárak csak foglalkozó kulcs-érték párok, ellentétben a Redis a magas performant adattípusok esetén. Redis is atomi műveletek, például a rendszer fűzi hozzá egy karakterlánc; ezek a típusok futtatást támogatja az értéket egy kivonatot; növekvő kérdez le, hogy egy lista; számítási set metszetének, a union és a különbség; vagy a tag egy rendezett készlet legmagasabb prioritású. Egyéb szolgáltatások közé tartozik a tranzakciók, pub/sub, parancsfájlok, korlátozott kulcsok idő élettartamát, Lua és konfigurációs beállításokat, hogy több viselkednek a hagyományos gyorsítótár Redis támogatása.

Egy másik fő Redis sikeres célja a kifogástalan, felfedezésre vár nyílt forráskódú ökoszisztémájának azt épül. Ez több nyelv megjelenik a rendelkezésre álló Redis-ügyfelek különböző készletében. A ökoszisztémától és az ügyfelek széles köre engedélyezése az Azure Redis Cache szinte bármilyen munkaterhelést Azure belül lenne készít által használható.

Ismerkedés az Azure Redis Cache szolgáltatással kapcsolatos további információkért lásd: [használata Azure Redis Cache hogyan](cache-dotnet-how-to-use-azure-redis-cache.md) és [Azure Redis Cache dokumentáció](index.md).

### <a name="managed-cache-service"></a>Felügyelt gyorsítótár-szolgáltatás
[Managed Cache service lett visszavonva November 30 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Archivált dokumentáció megtekintéséhez lásd: [archivált felügyelt gyorsítótár szolgáltatás dokumentációja](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Szerepköralapú gyorsítótár
[Szerepköralapú gyorsítótár November 30 2016 lett visszavonva.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Archivált dokumentáció megtekintéséhez lásd: [archivált szerepköralapú gyorsítótár dokumentáció](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
