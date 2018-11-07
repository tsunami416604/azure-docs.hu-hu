---
title: Az Azure Redis Cache – gyakori kérdések |} A Microsoft Docs
description: Ismerje meg, a válaszok a gyakori kérdésekre, minták és ajánlott eljárások az Azure Redis Cache-gyorsítótárhoz
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: 21b5050996428328bfda314b2f2242ed2a766e74
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239763"
---
# <a name="azure-redis-cache-faq"></a>Azure Redis Cache – Gyakori kérdések
Ismerje meg a válaszok a gyakori kérdésekre, minták és ajánlott eljárások az Azure Redis Cache-gyorsítótárhoz.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha kérdésem itt nem választ?
Ha a kérdés nem szerepel a listán, ossza meg velünk, és választ találjon nyújtunk segítséget.

* Új kérdést tenne fel, ez a GYIK a végén a közzé, és kapcsolatba léphet a az Azure Cache csapat és a Közösség többi tagjával, ez a cikk kapcsolatban.
* Szeretné elérni, egy szélesebb közönség számára, hogy felteheti a kérdését a a [Azure Cache MSDN-fórumában](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) és kapcsolatba léphet a az Azure Cache csapat és a Közösség más tagjai.
* Ha szeretne valamilyen funkcióra vonatkozó kérést, elküldheti a kérelmek és ötlet segíthet megtenni [Azure Redis Cache User Voice](https://feedback.azure.com/forums/169382-cache).
* E-mail küldése a következő címre: [Azure Cache külső visszajelzési](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Az Azure Redis Cache alapjai
Ebben a szakaszban a gyakori kérdések az Azure Redis Cache alapjait némelyike terjed ki.

* [Mi az Azure Redis Cache?](#what-is-azure-redis-cache)
* [Hogyan lehet az első lépések az Azure Redis Cache?](#how-can-i-get-started-with-azure-redis-cache)

A következő gyakori kérdések alapvető fogalmait és az Azure Redis Cache kapcsolatos kérdések és válaszok a többi – gyakori kérdések szakasz valamelyikében.

* [Melyik Redis Cache-ajánlatot és -méretet használjam?](#what-redis-cache-offering-and-size-should-i-use)
* [Melyik Redis cache-ügyfelek használhatok?](#what-redis-cache-clients-can-i-use)
* [Az Azure Redis Cache helyi emulátort van?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hogyan követhetem figyelemmel az állapotának és teljesítményének a gyorsítótár?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Tervezési – gyakori kérdések
* [Melyik Redis Cache-ajánlatot és -méretet használjam?](#what-redis-cache-offering-and-size-should-i-use)
* [Az Azure Redis Cache-teljesítmény](#azure-redis-cache-performance)
* [Milyen a régióban kell találom meg a gyorsítótár?](#in-what-region-should-i-locate-my-cache)
* [Hogyan történik a számlázás az Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
* [Használható az Azure Government Cloud, Azure China Cloud vagy a Microsoft Azure Germany Azure Redis Cache?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Fejlesztés – gyakori kérdések
* [Mit ehhez a StackExchange.Redis konfigurációs beállításokat?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Melyik Redis cache-ügyfelek használhatok?](#what-redis-cache-clients-can-i-use)
* [Az Azure Redis Cache helyi emulátort van?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hogyan futtathatok Redis parancsok?](#how-can-i-run-redis-commands)
* [Miért nem rendelkezik az MSDN osztálytár-referenciát néhány a más Azure-szolgáltatásokhoz hasonlóan az Azure Redis Cache?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Használható az Azure Redis Cache egy PHP-munkamenet gyorsítótáraként?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Mik azok a Redis-adatbázisok?](#what-are-redis-databases)

## <a name="security-faqs"></a>Biztonság – gyakori kérdések
* [Mikor kell engedélyezni a Kapcsolódás a Redis a nem SSL port?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Gyártás – gyakori kérdések
* [Mik az egyes éles környezetben ajánlott eljárások?](#what-are-some-production-best-practices)
* [Mik a szempontok gyakori Redis parancsok használatakor?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hogyan lehet becslésére és a gyorsítótár teljesítményének tesztelése?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Szálkészlet növekedési tényezővel kapcsolatos fontos információkat.](#important-details-about-threadpool-growth)
* [További átviteli sebesség lekérdezése az ügyfélen, StackExchange.Redis használata esetén a kiszolgáló Szemétgyűjtési engedélyezése](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Teljesítménnyel kapcsolatos megfontolások körül kapcsolatok](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Figyelés és hibaelhárítás – gyakori kérdések
Ebben a szakaszban a gyakori kérdések a leggyakoribb monitorozási és hibaelhárítási kérdések terjed ki. Figyelése és hibaelhárítása az Azure Redis Cache-példányokban kapcsolatos további információkért lásd: [figyelése az Azure Redis Cache](cache-how-to-monitor.md) és [hibaelhárítása az Azure Redis Cache](cache-how-to-troubleshoot.md).

* [Hogyan követhetem figyelemmel az állapotának és teljesítményének a gyorsítótár?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Miért látok időtúllépések?](#why-am-i-seeing-timeouts)
* [Miért megszakadt az ügyfél gyorsítótárából?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Előzetes gyorsítótárat használjam – gyakori kérdések
* [Melyik Azure gyorsítótárat használjam a megfelelő a számomra?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>Mi az az Azure Redis Cache?
Az Azure Redis Cache a népszerű, nyílt forráskódú [Redis Cache](http://redis.io)-re épül. Amely hozzáférést nyújt egy biztonságos, dedikált redis Cache gyorsítótárhoz, a Microsoft által felügyelt, és elérhető Azure-on belül bármely alkalmazásból. Részletes ismertetőt talál a [Azure Redis Cache](https://azure.microsoft.com/services/cache/) termékoldalán az Azure.com webhelyen.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Hogyan lehet az első lépések az Azure Redis Cache?
Többféleképpen is megkezdheti az Azure Redis Cache.

* Az elérhető oktatóanyagokban majd megtekinthet [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), és [Python](cache-python-get-started.md).
* Megtekinthet [hogyan hozhat létre nagy teljesítményű alkalmazások használata a Microsoft Azure Redis Cache](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Megtekinthet az ügyfelek, amelyek megfelelnek a fejlesztői nyelvek a projekthez, hogy hogyan használhatja a Redis-ügyfél dokumentációját. Nincsenek használható az Azure Redis Cache számos Redis-ügyfelek. A Redis-ügyfelek listáját lásd: [ http://redis.io/clients ](http://redis.io/clients).

Ha még nem rendelkezik Azure-fiók, akkor:

* [Nyisson egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a keretét el is használta, továbbra is megtarthatja a fiókot, és használhatja az ingyenes szolgáltatásokat és lehetőségeket.
* [Aktiválja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Melyik Redis Cache-ajánlatot és -méretet használjam?
Minden egyes Azure Redis Cache-ajánlatot biztosít különböző szintjei **mérete**, **sávszélesség**, **magas rendelkezésre állású**, és **SLA** beállítások.

Az alábbiakban egy Cache-ajánlatot kiválasztására vonatkozó szempontok.

* **Memória**: az alap és Standard szinthez ajánlat 250 MB – 53 GB. A prémium szint 530 GB-os kínál. További információkért lásd: [Azure Redis Cache szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/cache/).
* **Hálózati teljesítményt**: Ha egy számítási feladat, amely a nagy adatátviteli kapacitást igényel, a prémium szint biztosít a Standard vagy Basic képest nagyobb sávszélességet. Az egyes szinteken belül nagyobb méretű gyorsítótárak is nagyobb sávszélességet a mögöttes virtuális gép, amelyen a gyorsítótár miatt. Tekintse meg a [az alábbi táblázat](#cache-performance) további információt.
* **Átviteli sebesség**: A prémium szintű kapacitást biztosít a maximális érhető el. Ha a gyorsítótár-kiszolgáló vagy ügyfél eléri a sávszélesség-korlátozások, időtúllépések, az ügyfél oldalán kaphat. További információkért lásd az alábbi táblázatot.
* **Magas rendelkezésre állás/SLA**: az Azure Redis Cache garantálja, hogy az egy Standard vagy prémium szintű gyorsítótár rendelkezésre legalább 99,9 %-ában. SZERZŐDÉSÜNK kapcsolatos további információkért lásd: [Azure Redis Cache szolgáltatás díjszabása](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). A szolgáltatói szerződés csak a Cache-végpontokra irányuló kapcsolatokra érvényes. A szolgáltatói szerződés nem biztosít védelmet az adatvesztéssel szemben. Azt javasoljuk, hogy a Redis adatmegőrzési funkció használatát a prémium szintű az adatvesztéssel szembeni ellenálló-képesség növelésére.
* **Redis-adatmegőrzés**: A prémium szintű csomag segítségével megőrizheti a gyorsítótárazza az adatokat egy Azure Storage-fiókot. Egy alapszintű és Standard gyorsítótár, a csak a memóriában tárolt összes adatot. Van-e hiba a mögöttes infrastruktúra problémák lehetséges adatvesztéssel is lehet. Azt javasoljuk, hogy a Redis adatmegőrzési funkció használatát a prémium szintű az adatvesztéssel szembeni ellenálló-képesség növelésére. Az Azure Redis Cache RDB-fájlba való és (hamarosan) AOF lehetőséget kínál a Redis megőrzési funkciója. További információkért lásd: [prémium szintű Azure Redis Cache-gyorsítótárhoz adatmegőrzés konfigurálása](cache-how-to-premium-persistence.md).
* **Redis-fürt**: létrehozása gyorsítótárak 53 GB-nál nagyobb vagy bonthatók az adatok több Redis-csomóponton, használhatja a Redis-fürtözés, azaz prémium tarifacsomagban érhető el. Minden egyes csomópont a magas rendelkezésre állás érdekében két elsődleges/replika gyorsítótár áll. További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-clustering.md) (Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
* **Fokozott biztonság és hálózati elkülönítési**: Azure Virtual Network (VNET) központi telepítés biztosítja a fokozott biztonságot és elszigeteltséget az Azure Redis Cache, valamint a alhálózatokkal, hozzáférés-vezérlési szabályzatokkal és további más szolgáltatások elérésének korlátozása. További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-vnet.md) (Virtuális hálózat támogatásának konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
* **A Redis konfigurálása**: A Standard és Premium rétegekben konfigurálhatja a Redis kulcstérértesítések.
* **Ügyfélkapcsolatok maximális száma**: A prémium szintű kínál a Redishez, nagyobb méretű gyorsítótárak esetében kapcsolatok magasabb azonosítószámot csatlakozó ügyfelek maximális számát. Fürtszolgáltatás nem növeli a fürtözött Cache-gyorsítótárhoz elérhető kapcsolatok száma. További információkért lásd: [Azure Redis Cache díjszabás](https://azure.microsoft.com/pricing/details/cache/).
* **Core dedikált Redis-kiszolgáló**: a prémium szintű méretek gyorsítótár egy dedikált Processzormagok rendelkezik redis. Az alapszintű/Standard szinteken, C1 csomag mérete, és a fent kell egy dedikált Processzormagok a Redis-kiszolgáló.
* **A redis az egyszálas** így kettőnél több maggal rendelkező nem biztosít további előnye keresztül csupán két maggal rendelkező, de nagyobb Virtuálisgép-méretek általában rendelkeznek a nagyobb sávszélesség-nál kisebb méretű. Ha a gyorsítótár-kiszolgáló vagy ügyfél eléri a sávszélesség-korlátozások, majd kap időtúllépések, az ügyfél oldalán.
* **Teljesítménnyel kapcsolatos fejlesztések**: a prémium szintű gyorsítótárak telepítve vannak, hardver, amely rendelkezik a gyorsabb processzorokkal rendelkeznek, az alapszintű vagy Standard csomag képest jobb teljesítményt nyújtó. Prémium szintű gyorsítótárak magasabb átviteli sebesség és a kisebb a késésük rendelkezik.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Az Azure Redis Cache-teljesítmény
Az alábbi táblázat a különböző méretű standard szintű csomag tesztelése során maximális sávszélesség értéke és a prémium szintű használatával gyorsítótáraz `redis-benchmark.exe` Iaas virtuális gépről az Azure Redis Cache-végponton. Az SSL átviteli sebesség a redis-teljesítményteszt csatlakozni az Azure Redis Cache-végpont szerepel a stunnel.

>[!NOTE] 
>Ezek az értékek nem garantált, és nincs a ezekre nem vonatkozik garantált szolgáltatási számok, de a tipikus kell lennie. Be kell tölteni a tesztelje alkalmazását, az alkalmazás a megfelelő gyorsítótár mérete határozza meg.
>Ezek a számok előfordulhat, hogy módosítani, mert újabb eredményeket rendszeres időközönként írtunk.
>

Ebből a táblázatból a következő következtetéseket is azt:

* A gyorsítótárak esetében, amelyek csak azonos méretű átviteli sebesség nagyobb a prémium szintű, mint a Standard szint a korábban megszokott. Például egy 6 GB-os gyorsítótár, a P1 180 000 RPS mint 100 000 C3 csomag esetében a korábban megszokott.
* A Redis-fürtözés átviteli sebesség növeli a költségráfordításokkal egyenes arányban a fürtben (csomópontok) szegmensei számának növelésével. Például, ha 10 szegmens-P4 szintű fürtöt hoz létre, majd az elérhető átviteli sebesség 400000 * 10 = 4 millió RPS.
* Nagyobb méretű kulcs méretek átviteli sebesség nagyobb a prémium szintű, mint a Standard szint a korábban megszokott.

| Tarifacsomag | Méret | Processzormagok | Rendelkezésre álló sávszélesség | 1 KB-os méret | 1 KB-os méret |
| --- | --- | --- | --- | --- | --- |
| **Standard szintű gyorsítótár mérete** | | |**Megabit / mp (Mb/s) vagy megabájt / másodperc (MB/s)** |**Második (RPS) a nem SSL-kérelemből** |**A kérelmek száma a második (RPS) SSL** |
| C0 |250 MB |Közös |100 / 12.5 |15 000 |7500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37,000 |
| C3 |6 GB |4 |1000 / 125 |100 000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102,000 |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Prémium szintű gyorsítótár mérete** | |**CPU-magok száma a szegmensben** | **Megabit / mp (Mb/s) vagy megabájt / másodperc (MB/s)** |**A kérelmek száma a második (RPS) a nem SSL-, partíciónkénti** |**A kérelmek száma (RPS) második SSL, partíciónkénti** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400,000 |373,000 |

Útmutatás a stunnel beállításához, vagy a Redis-eszközök például letöltése `redis-benchmark.exe`, tekintse meg a [hogyan futtathatok Redis parancsok?](#cache-commands) szakaszban.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Milyen a régióban kell találom meg a gyorsítótár?
A legjobb teljesítmény és legkisebb késés keresse meg az Azure Redis Cache és a gyorsítótár ügyfélalkalmazás ugyanabban a régióban.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Hogyan történik a számlázás az Azure Redis Cache?
Azure Redis Cache díjszabása [Itt](https://azure.microsoft.com/pricing/details/cache/). A díjszabási lap felsorolja az óránkénti, díjszabás. Gyorsítótárak kezdve, amely a gyorsítótár jön létre, amely egy törlődik, amíg percalapú elszámolással számlázzuk. Nincs a leállítása és a gyorsítótár számlázását felfüggesztése lehetőség.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Használható az Azure Government Cloud, Azure China Cloud vagy a Microsoft Azure Germany Azure Redis Cache?
Igen, az Azure Redis Cache érhető el az Azure Government Cloud, Azure China Cloud és a Microsoft Azure Germany. Ezek a felhők összehasonlítása az Azure nyilvános felhő a elérése és kezelése az Azure Redis Cache URL-eltérőek. 

| Felhő   | A Redis DNS-utótag            |
|---------|---------------------------------|
| Nyilvános  | *.redis.cache.windows.net       |
| USA-beli államigazgatás  | *.redis.cache.usgovcloudapi.net |
| Németország | *.redis.cache.cloudapi.de       |
| Kína   | *.redis.cache.chinacloudapi.cn  |

Más felhőkben az Azure Redis Cache használatánál megfontolandó szempontokról további információért tekintse meg a következő hivatkozásokat.

- [Az Azure Government-adatbázisok – az Azure Redis Cache](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Az Azure China Cloud – az Azure Redis Cache](https://www.azure.cn/documentation/services/redis-cache/)
- [A Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

A PowerShell-lel az Azure Government Cloud, Azure China Cloud és a Microsoft Azure Germany Azure Redis Cache használatával kapcsolatos információkért lásd: [kapcsolódás más felhőkben – az Azure Redis Cache PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Mit ehhez a StackExchange.Redis konfigurációs beállításokat?
StackExchange.Redis számos lehetőség áll. Ez a szakasz néhány általános beállításait ismerteti. További részletes információ a StackExchange.Redis lehetőségekről, tekintse meg a [StackExchange.Redis konfigurációs](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Leírás | Ajánlás |
| --- | --- | --- |
| AbortOnConnectFail |Ha igaz értékű, a kapcsolat nem csatlakozik újra hálózati hiba után. |"False" értékűre, és bízza a StackExchange.Redis újra automatikusan. |
| ConnectRetry |Ismételje meg a kapcsolódási kísérletek során kezdeti száma a kapcsolatot. |Tekintse meg a következő útmutatókat. |
| ConnectTimeout |A csatlakozási műveletek időtúllépési az MS-os. |Tekintse meg a következő útmutatókat. |

Az alapértelmezett értékeket, az ügyfél általában elegendő. Finomhangolhatja a beállításokat, a számítási feladatok alapján.

* **Újrapróbálkozások**
  * ConnectRetry és ConnectTimeout az általános útmutatást, hogy gyors, majd próbálkozzon újra. Ez az útmutató a számítási feladatok, és hogy mennyi idő átlagos azt vesz igénybe az ügyfél egy Redis parancsot, és a válasz fogadására alapul.
  * Lehetővé teszik a StackExchange.Redis automatikusan újra a kapcsolat állapotának ellenőrzése és újracsatlakozás saját maga helyett. **Kerülje a ConnectionMultiplexer.IsConnected tulajdonság**.
  * Snowballing - futhat, néha az a probléma, akkor újból próbálkozunk a próbálkozások snowball, és soha nem helyreáll. Ha snowballing történik, akkor érdemes megfontolni egy exponenciális leállítási újrapróbálkozási algoritmussal leírtak szerint [Újrapróbálkozásokra vonatkozó általános útmutató](../best-practices-retry-general.md) a Microsoft Patterns és gyakorlatok csoport által közzétett.
* **Időtúllépési értékek**
  * Vegye figyelembe a számítási feladatok, és ennek megfelelően állítsa az értékeket. Ha nagy értékeket tárolja, állítsa be az időtúllépés értéke.
  * Állítsa be `AbortOnConnectFail` StackExchange.Redis false (hamis), és tegye lehetővé, hogy csatlakoztassa újra az Ön számára.
  * Az alkalmazás ConnectionMultiplexer egyetlen példányát használja. Egy LazyConnection segítségével hozzon létre egy kapcsolati tulajdonság által visszaadott egyetlen példányt, ahogyan [csatlakozás a gyorsítótárhoz, a ConnectionMultiplexer osztállyal](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Állítsa be a `ConnectionMultiplexer.ClientName` tulajdonságát az alkalmazás példány egyedi nevét diagnosztikai célokra.
  * Több `ConnectionMultiplexer` példányok egyéni számítási feladatokhoz.
      * Ez a modell is követheti, ha az alkalmazás változó terhelés. Példa:
      * Az egyik nagy kulcsok kezelésére vonatkozó multiplexer rendelkezhet.
      * Egy kis kulcsok kezelésére vonatkozó multiplexer rendelkezhet.
      * Állítson be eltérő értékeket kapcsolat időtúllépésének, és az újrapróbálkozási logika az egyes ConnectionMultiplexer használt.
      * Állítsa be a `ClientName` az egyes multiplexer diagnosztikai kiküszöbölni.
      * Ez az útmutató további eredményezhet hatékonyabbá műveletenként késést `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Melyik Redis cache-ügyfelek használhatok?
A Redis kapcsolatos nagyszerű dolog, hogy nincsenek-e számos különböző programozási nyelvet támogató ügyfelek számát. Az ügyfelek aktuális listáját lásd: [Redis ügyfelek](http://redis.io/clients). Mind a több különböző nyelv és az ügyfelek számára oktatóanyagokkal, lásd: [használata az Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) , és kattintson a kívánt nyelvet, a nyelv váltó a cikk elején.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Az Azure Redis Cache helyi emulátort van?
Az Azure Redis Cache nem helyi emulátor van, de a redis-server.exe MSOpenTech verziójának futtatása a [parancssori eszközök Redis](https://github.com/MSOpenTech/redis/releases/) a helyi gép, és csatlakozni hozzá azonos beolvasásához a helyi gyorsítótár-emulátorban, ahogy az alábbi példában látható:

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


Igény szerint konfigurálható egy [redis.conf](http://redis.io/topics/config) fájlt, hogy jobban illeszkedjenek a [alapértelmezett gyorsítótárazási beállításai](cache-configure.md#default-redis-server-configuration) az online az Azure Redis Cache-gyorsítótárhoz Ha szükséges.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hogyan futtathatok Redis parancsok?
A webhelyen felsorolt parancsok bármelyikét használhatja [Redis parancsok](http://redis.io/commands#) kivételével a webhelyen felsorolt parancsok [parancsok nem támogatott az Azure Redis Cache Redis](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Redis parancsok futtatásához több lehetősége van.

* Ha egy Standard vagy prémium szintű gyorsítótár, a Redis-parancsok használatával futtathatja a [Redis konzol](cache-configure.md#redis-console). A Redis-konzol biztonságos megoldást nyújt a Redis-parancsok futtatásához az Azure Portalon.
* A Redis parancssori eszközöket is használhat. Használja őket, hajtsa végre az alábbi lépéseket:
* Töltse le a [parancssori eszközök Redis](https://github.com/MSOpenTech/redis/releases/).
* A gyorsítótár használatával csatlakozhat `redis-cli.exe`. Adja át a gyorsítótár-végpontot a a -h váltson, és a kulcs használatával – az alábbi példában látható módon:
* `redis-cli -h <redis cache name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> A Redis parancssori eszközök sem működik együtt az SSL-port, de egy segédprogramot használhatja például `stunnel` való biztonságos csatlakozáshoz az eszközök az SSL-port utasításait követve a [bejelentése ASP.NET munkamenetállapot-szolgáltatóját Redis előzetes verzió Kiadási](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogbejegyzést.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Miért nem rendelkezik az MSDN osztálytár-referenciát néhány a más Azure-szolgáltatásokhoz hasonlóan az Azure Redis Cache?
A Microsoft Azure Redis Cache a népszerű, nyílt forráskódú Redis Cache alapul, és számos különböző hozzáférhető [Redis ügyfelek](http://redis.io/clients) számos programozási nyelvet. Minden ügyfél rendelkezik a saját API-t, a Redis cache példány történő-hívást hajt végre [Redis parancsok](http://redis.io/commands).

Mivel az egyes ügyfelek különböző, az MSDN-en nem egy központosított osztályhivatkozása van, és minden egyes ügyfél kezeli a saját dokumentációjában. A segédanyagok kívül számos oktatóprogram: Ismerkedés az Azure Redis Cache használatával különböző nyelv és a gyorsítótár-ügyfelek hogyan. Ezekben az oktatóanyagokban eléréséről, lásd a [Azure Redis Cache használata](cache-dotnet-how-to-use-azure-redis-cache.md) , és kattintson a kívánt nyelvet, a nyelv váltó a cikk elején.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Használható az Azure Redis Cache egy PHP-munkamenet gyorsítótáraként?
Igen, az Azure Redis Cache-t egy PHP munkamenet-gyorsítótár használja, adja meg a kapcsolati karakterláncot az Azure Redis Cache-példányhoz a `session.save_path`.

> [!IMPORTANT]
> Azure Redis Cache használata PHP munkamenet gyorsítótárként, URL-címet kell kódolása a biztonsági kulcs segítségével kapcsolódhat a gyorsítótárat, az alábbi példában látható módon:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Ha a kulcs nem URL-kódolású, egy üzenet, például a kivétel jelenhet meg: `Failed to parse session.save_path`
>
>

A PHP munkamenet-gyorsítótár a PhpRedis ügyféllel, Redis Cache használatával kapcsolatos további információkért lásd: [PHP munkamenet kezelő](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Mik azok a Redis-adatbázisok?

Redis-adatbázisok olyan adatokat a Redis-példányt belül csak egy logikai elkülönítése. A gyorsítótár-memória van osztva az adatbázisok és a tényleges memória használat egy adott adatbázis függ, hogy az adatbázis tárolt kulcsok/értékek között. Például egy C6 csomag gyorsítótár, 53 GB-nyi memóriát. Dönthet úgy, hogy minden 53 GB-os elhelyezi egy adatbázist, vagy akkor is feloszthatja az azt több adatbázis között. 

> [!NOTE]
> Ha egy prémium szintű Azure Redis Cache használata a fürtözés engedélyezve van, csak adatbázis 0 érhető el. Ez a korlátozás egy belső Redis korlátozás, és nem egyedi az Azure Redis Cache. További információkért lásd: [van a fürtszolgáltatás használandó ügyfélalkalmazásomnak semmilyen módosítást?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Mikor kell engedélyezni a Kapcsolódás a Redis a nem SSL port?
Redis Cache kiszolgáló nem támogatja natív módon az SSL, de az Azure Redis Cache nem. Ha az Azure Redis Cache csatlakozik, és az ügyfél támogatja az SSL-t, például a StackExchange.Redis, akkor SSL kell használnia.

>[!NOTE]
>A nem SSL port az új Azure Redis Cache-példányokban alapértelmezés szerint le van tiltva. Ha az ügyfél nem támogatja az SSL, akkor a nem SSL port utasításait követve engedélyeznie kell a [hozzáférési portok](cache-configure.md#access-ports) szakaszában a [gyorsítótár konfigurálása az Azure Redis Cache](cache-configure.md) cikk.
>
>

Eszközök például a redis `redis-cli` sem működik együtt az SSL-port, de egy segédprogramot használhatja például `stunnel` való biztonságos csatlakozáshoz az eszközök az SSL-port utasításait követve a [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis Előzetes verziójának](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogbejegyzést.

A Redis-eszközök letöltésével kapcsolatos utasításokért lásd: a [hogyan futtathatok Redis parancsok?](#cache-commands) szakaszban.

### <a name="what-are-some-production-best-practices"></a>Mik az egyes éles környezetben ajánlott eljárások?
* [StackExchange.Redis ajánlott eljárások](#stackexchangeredis-best-practices)
* [Konfigurációs és fogalmak](#configuration-and-concepts)
* [Teljesítménytesztelés](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis ajánlott eljárások
* Állítsa be `AbortConnect` FALSE, majd lehetővé teszik az automatikus újracsatlakozás ConnectionMultiplexer. [Részleteket itt talál](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Újból felhasználhatja a ConnectionMultiplexer – ne hozzon létre egy újat az egyes kérések. A `Lazy<ConnectionMultiplexer>` minta [itt látható](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) ajánlott.
* Működik legjobban a kisebb értékek a redis Cache, ezért érdemes a nagyobb méretű adatok több kulcsokból darabolás. A [Redis vitafórumban](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb nagy számít. Olvasási [Ez a cikk](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) egy példa a probléma, amely a nagy értékek okozhatja.
* Konfigurálja a [szálkészlet beállítások](#important-details-about-threadpool-growth) időtúllépések elkerülése érdekében.
* Használja legalább az alapértelmezett connectTimeout 5 másodperc. Ez az időtartam alatt StackExchange.Redis elegendő időt, így újra létrehozza a kapcsolatot a hálózati blip esetén tenné.
* Vegye figyelembe a különböző műveleteket futtat teljesítmény költségeket. Például a `KEYS` parancs O(n) művelet, és el kell kerülni. A [redis.io hely](http://redis.io/commands/) idő összetettségét, amely támogatja az egyes műveletek körüli részleteket is tartalmaz. Kattintson az egyes paranccsal megtekintheti az egyes műveletek bonyolultsága.

#### <a name="configuration-and-concepts"></a>Konfigurációs és fogalmak
* Használja a Standard vagy prémium szintű éles rendszerek esetén. Az alapszintű csomag egyetlen csomópont rendszer nincs adatreplikáció és nem biztosítunk szolgáltatói szerződést. Ezenkívül legalább C1 gyorsítótárak használatát. C0 csomag gyorsítótárak jellemzően egyszerű fejlesztési és tesztelési célra használják.
* Ne feledje, hogy a Redis- **memórián belüli** adattár. Olvasási [Ez a cikk](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , hogy Ön ismeri a forgatókönyvek, ahol előfordulhat adatvesztés.
* A rendszer fejlesztése, hogy a kapcsolat jelekből képes kezelni [javításait és a feladatátvétel miatt](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teljesítménytesztelés
* Indítsa el a `redis-benchmark.exe` betekintést nyerhet a teljesítmény a saját teljesítményoptimalizált írása előtt ellenőrzi. Mivel `redis-benchmark` nem támogatja az SSL-t, akkor kell [az Azure Portalon a nem SSL port engedélyezéséhez](cache-configure.md#access-ports) a teszt futtatása előtt. Példák: [hogyan becslésére és a gyorsítótár teljesítményének tesztelése?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Az ügyfél a teszteléshez használt virtuális gép és a Redis cache-példány ugyanabban a régióban kell lennie.
* Dv2 Virtuálisgép-sorozatok használatát az ügyfél, jobb hardvert, és adjon a legjobb eredmények elérése érdekében javasoljuk.
* Ellenőrizze, hogy az ügyfél úgy dönt, virtuális gép rendelkezik-e legalább mennyi számítási és a sávszélesség képességeket biztosítja, mint a gyorsítótár teszteli.
* A Windows engedélyezi a VRSS az ügyfélszámítógépen. [Részleteket itt talál](https://technet.microsoft.com/library/dn383582.aspx).
* Mert futtatnak Processzor és a hálózati jobb hardveren jobban rendelkezik prémium szintű Redis-példány hálózati teljesítmény és a késés.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Mik a szempontok gyakori Redis parancsok használatakor?
* Egyes Redis-parancsokat, időbe telik végrehajtásához, ezek a parancsok hatásának ismerete nélkül nem futhat.
  * Ha például nem futnak a [kulcsok](http://redis.io/commands/keys) parancs éles környezetben, mint egy hosszú időt adja vissza a kulcsokat számától függően eltarthat. A redis egy olyan egyszálas kiszolgáló és a egy időben feldolgozza a parancsokat egy. Ha más kulcsok után kiadott parancsok, akkor nem fogja feldolgozni mindaddig, amíg a Redis dolgozza fel a kulcsok parancsot. A [redis.io hely](http://redis.io/commands/) idő összetettségét, amely támogatja az egyes műveletek körüli részleteket is tartalmaz. Kattintson az egyes paranccsal megtekintheti az egyes műveletek bonyolultsága.
* Kulcsméretek - kell használnom kis kulcs/érték vagy nagy méretű kulcs/érték? Általában attól függ, a forgatókönyvet. A forgatókönyv nagyobb kulcsra van szükség, ha a ConnectionTimeout beállítása és ismételje meg az értékeket, és állítsa be az újrapróbálkozási logikája. A Redis-kiszolgáló szempontjából a jobb teljesítményt, a kisebb értékek jelennek meg.
* Ezeket a szempontokat nem jelenti azt, hogy a Redis; nem lehet tárolni a nagyobb értékek az alábbi szempontokat figyelembe kell lennie. Késések magasabb lesz. Ha egy adatkészletet, amely nagyobb, és a egy kisebb, ConnectionMultiplexer több példányt is használhat, minden más-más időtúllépési és az újrapróbálkozási értékek leírtak szerint konfigurálta az előző [a StackExchange.Redis mire konfigurációs beállítások tegye](#cache-configuration) szakaszban.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hogyan lehet becslésére és a gyorsítótár teljesítményének tesztelése?
* [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát. A mérőszámokat megtekintheti az Azure Portalon, illetve többféle eszközzel is [letöltheti és áttekintheti](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) őket.
* Segítségével a redis-benchmark.exe terheléses tesztelése a Redis-kiszolgáló.
* Győződjön meg arról, hogy a terhelésteszt ügyfél és a Redis gyorsítótárt ugyanabban a régióban.
* A redis-cli.exe, illetve figyelheti a gyorsítótár a INFO paranccsal.
* A betöltés okozza a magas memória töredezettségét, ha a gyorsítótár méretének meg kell vertikálisan.
* A Redis-eszközök letöltésével kapcsolatos utasításokért lásd: a [hogyan futtathatok Redis parancsok?](#cache-commands) szakaszban.

A következő parancsokat használja a redis-benchmark.exe példát kell megadni. A pontos eredmények érdekében futtassa ezeket a parancsokat a gyorsítótárat ugyanabban a régióban egy virtuális gépről.

* Használatával egy 1 KB-adattartalom tesztelési vagyok SET kérések

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teszt vagyok GET-kérések egy 1 KB hasznos használatával.
  Megjegyzés: A készlet teszteléséhez először a gyorsítótár feltöltése a fent látható

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Szálkészlet növekedési tényezővel kapcsolatos fontos információkat.
A CLR-beli szálkészlet rendelkezik szálak – "Dolgozó" és "Feladat:%ti/o-végrehajtási Port" (más néven Olvasóhoz) két típusú hozzászólásláncokat.

* Munkavégző szál használható a többek között a feldolgozás a `Task.Run(…)`, vagy `ThreadPool.QueueUserWorkItem(…)` módszereket. Ezek a szálak is használhatók a CLR különböző összetevői, amikor a munkahelyi szükségességéről a háttérbeli szálon.
* Ha aszinkron i/o történik (például a hálózati olvasása) Olvasóhoz szál használható.

A szálkészlet új munkavégző szálat vagy i/o-befejezési szálak igény szerinti (nélkül biztosít bármely szabályozás) a "Minimális" beállítás az egyes szál eléréséig. Alapértelmezés szerint a szálak minimális számát, a rendszer processzorainak számát van beállítva.

Ha a meglévő (foglalt) szálak száma eléri a "minimális" szálak számát, a szálkészlet a sebesség, amellyel új szálak száma 500 ezredmásodperc egy hozzászólásláncra kódtárba fog szabályozás. Általában a rendszer lekérdezi a munka egy Olvasóhoz szál kellene befejeződésével, ha fogja feldolgozni a munka nagyon gyorsan. Azonban ha a hirtelen munka, több, mint a beállított "Minimum" beállítás lesz némi késedelemmel feldolgozása közé, a szálkészlet megvárja, amíg végbe két dolog egyikét.

1. Egy meglévő hozzászóláslánc válik feldolgozását is a munkát.
2. Nincs meglévő hozzászóláslánc 500ms, ingyenesen válik, így létrehoz egy új szálat.

Alapvetően azt jelenti, hogy ha a foglalt szálak száma nagyobb, mint a minimális szálak, valószínűleg licencdíjat 500ms késleltetés az alkalmazás hálózati forgalom feldolgozása előtt. Azt is fontos megjegyezni, hogy ha egy meglévő hozzászóláslánc (a mi emlékszem alapján) 15 másodpercnél hosszabb ideig tétlen marad, törlődnek, és ismételje meg a növekedési és csökkenés a ciklus.

Ha megnézzük a példában hibaüzenetet a StackExchange.Redis (build 1.0.450 vagy újabb), látni fogja, hogy most már jelenít meg szálkészlet statisztika (Olvasóhoz és a FELDOLGOZÓI részleteket lásd alább).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Az előző példában láthatja, hogy Olvasóhoz szál 6 foglalt szálak és a rendszer 4 minimális szálak engedélyezése van konfigurálva. Ebben az esetben az ügyfél akkor valószínűleg már látott két 500 ms késések mert 6 > 4.

Vegye figyelembe, hogy StackExchange.Redis elérjék időtúllépések Ha Olvasóhoz vagy a MUNKAVÉGZŐ szál növekedését szabályozott beolvasása.

### <a name="recommendation"></a>Ajánlás
Adja meg ezeket az adatokat, javasoljuk, hogy ügyfeleink állítsa Olvasóhoz és a feldolgozó szálak minimális értéke nagyobb, mint az alapértelmezett érték. A dokumentum ajánlásai útmutatást milyen ezt az értéket kell, mert az egy alkalmazáshoz megfelelő érték lesz túl magas és alacsony egy másik alkalmazás nem ad. Ez a beállítás is hatással lehet más részein összetett alkalmazások teljesítményét így minden ügyfél kell ezt a beállítást, ő konkrét igényeiknek finomhangolásához. Jó kiindulópontot 200-as vagy 300, majd tesztelje, és igény szerint módosíthatja.

Hogyan konfigurálja ezt a beállítást:

* Az ASP.NET, használja a ["minIoThreads" vagy "minWorkerThreads" konfigurációs beállítás] [ "minIoThreads" configuration setting] alatt a `<processModel>` konfigurációs elem a Web.config fájlban. Ha alapjait az Azure websites szolgáltatásban futtatja, ez a beállítás nincs közzétéve a konfigurációs beállításokat. Azonban továbbra is kell tudni tartalomvédelemre konfigurálni ezt a beállítást, programozott módon (lásd alább) a Application_Start metódus az global.asax.cs.

  > [!NOTE] 
  > Ez a konfigurációs elemben megadott érték egy *magonként* beállítás. Ha például egy 4 processzormagos számítógép, és szeretné a minIOThreads beállítása futási időben 200 kell, ha használja `<processModel minIoThreads="50"/>`.
  >

* Kívül, az ASP.NET és az Azure-webhelyek global.asax, használja a [ThreadPool.SetMinThreads (...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API-T.

  > [!NOTE]
  > Az API által megadott érték egy globális beállítás, az egész AppDomain érintő. Ha rendelkezik egy 4 processzormagos számítógép, és szeretné beállítani minWorkerThreads és minIOThreads 50 CPU futási időben, akkor ThreadPool.SetMinThreads (200-as, 200-as) kell használnia.

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>További átviteli sebesség lekérdezése az ügyfélen, StackExchange.Redis használata esetén a kiszolgáló Szemétgyűjtési engedélyezése
Kiszolgáló globális Katalógus engedélyezésével optimalizálhatja az ügyfél, és jobb teljesítményt és átviteli sebességet biztosít, StackExchange.Redis használatakor. Kiszolgáló globális Katalógus és engedélyezését a további információkért lásd a következő cikkeket:

* [Kiszolgáló globális Katalógus engedélyezése](https://msdn.microsoft.com/library/ms229357.aspx)
* [A szemétgyűjtés – alapok](https://msdn.microsoft.com/library/ee787088.aspx)
* [A szemétgyűjtés és teljesítmény](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Teljesítménnyel kapcsolatos megfontolások körül kapcsolatok

Minden egyes tarifacsomagja az ügyfélkapcsolatokat, a memória és a sávszélesség eltérő korlátokkal rendelkeznek. Miközben lehetővé teszi, hogy minden egyes gyorsítótár mérete *akár* bizonyos számú kapcsolatok, minden kapcsolat Redis többletterhelést társított azt. Egy példa az ilyen terhelés miatt a TLS/SSL-titkosítást Processzor- és használati lenne. A maximális kapcsolathoz megadott korlátot, egy adott gyorsítótár méretének feltételezi, hogy egy kevésbé terhelt gyorsítótár. Ha a kapcsolat terhelés betöltése *plusz* Ügyfélműveletek terhelés meghaladja a kapacitását, a rendszer, a gyorsítótár kapacitással kapcsolatos problémákat tapasztalhatnak, még akkor is, ha nem túllépte a kapcsolathoz megadott korlátot, a jelenlegi gyorsítótár mérete a.

Az egyes szintek különböző kapcsolatokhoz korlátai kapcsolatos további információkért lásd: [Azure Redis Cache díjszabás](https://azure.microsoft.com/pricing/details/cache/). Kapcsolatok és más alapértelmezett konfigurációkkal kapcsolatos további információkért lásd: [kiszolgálókonfiguráció alapértelmezett Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hogyan követhetem figyelemmel az állapotának és teljesítményének a gyorsítótár?
A Microsoft Azure Redis Cache-példányokban figyelhető a [az Azure portal](https://portal.azure.com). Meg is metrikákat tekinthet meg, rögzítheti a kezdőpulton mérőszámdiagramok, testre szabhatja a dátum- és időtartományt figyelési diagramok, hozzáadása és metrikák eltávolítása a diagramok és riasztásokat állíthat be adott feltételek teljesülése esetén. További információkért lásd: [figyelő Azure Redis Cache](cache-how-to-monitor.md).

A Redis Cache **erőforrás menüben** figyelése és hibaelhárítása a gyorsítótárak több eszközöket is tartalmaz.

* **Problémák diagnosztizálása és megoldása** nyújt információt a gyakori problémák és stratégiák feloldása őket.
* **Erőforrás állapota** az erőforrás figyeli, és jelzi, hogy ha a várt módon fut. Az Azure Resource health szolgáltatással kapcsolatos további információkért lásd: [Azure Resource health áttekintése](../resource-health/resource-health-overview.md).
* **Új támogatási kérelem** nyisson egy támogatási kérelmet a gyorsítótár lehetőségeket biztosít.

Ezek az eszközök lehetővé teszik az Azure Redis Cache-példány állapotának figyelése és gyorsítótárazó alkalmazásainak kezeléséhez nyújt segítséget. További információkért lásd a "Támogatás és hibaelhárítás beállításai" szakaszának [konfigurálása az Azure Redis Cache](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Miért látok időtúllépések?
Időtúllépés fordulhat elő, az ügyfél felvegye a Redis használt. Ha egy parancs el lett küldve a Redis-kiszolgáló, a parancs várólistára van, és Redis-kiszolgáló végül szerzi be a parancsot, és végrehajtja. Azonban az ügyfél képes a folyamat során időtúllépés, és ha az nem kivétel következik be a hívó oldalon. Időtúllépési problémák hibaelhárításával kapcsolatos további információkért lásd: [ügyféloldali hibaelhárítási](cache-how-to-troubleshoot.md#client-side-troubleshooting) és [StackExchange.Redis időtúllépési kivételeket](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Miért megszakadt az ügyfél gyorsítótárából?
Az alábbiakban néhány gyakori oka egy gyorsítótár kapcsolatbontás.

* Ügyféloldali okok
  * Az ügyfélalkalmazás rendszer újratelepítése.
  * Az ügyfélalkalmazás egy skálázási műveletet végrehajtani.
    * A Cloud Services vagy a Web Apps esetén ezt okozhatják automatikus skálázást.
  * A hálózati réteg az ügyféloldalon módosítani.
  * Átmeneti hiba történt az ügyfél vagy a hálózati csomópontok az ügyfél és a kiszolgáló között.
  * A sávszélesség a küszöbérték-korlátok születtek.
  * CPU kötött műveletek végrehajtása túl sokáig tartott.
* Kiszolgálóoldali okok
  * A standard szintű gyorsítótár kínáló az Azure Redis Cache szolgáltatás kezdeményezett feladatátvétel az elsődleges csomópontról a másodlagos csomópontra.
  * Az Azure a példány, ahol a gyorsítótár lett telepítve lett javítása
    * Ez lehet a Redis-kiszolgáló frissítéseit, vagy általános virtuális gép karbantartásának.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Melyik Azure Gyorsítótárat használjam?
> [!IMPORTANT]
> Múlt évi megfelelően [közlemény](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), Azure Managed Cache Service és a szerepköralapú gyorsítótár szolgáltatás **kivonásra került** 2016. November 30. Azt javasoljuk, hogy használjon [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Áttelepítésével kapcsolatban további információkért lásd: [áttelepítése a Managed Cache Service az Azure Redis Cache](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Azure Redis Cache
Azure Redis Cache használata általánosan elérhető a mérete akár 53 GB és a egy rendelkezésre állási 99,9 %-os szolgáltatásiszint-szerződés tartozik. Az új [prémium szintű](cache-premium-tier-intro.md) méretek akár kínál 530 GB-os és a fürtszolgáltatás, a virtuális hálózat és az adatmegőrzés, 99,9 %-os SLA.

Az Azure Redis Cache egy biztonságos, dedikált Redis cache használata, a Microsoft által felügyelt lehetővé teszi az ügyfeleknek. Ajánlatunk révén kihasználhatja a gazdag funkciókat és környezetet a Redis és megbízható üzemeltetési és figyelése a Microsoft által biztosított beolvasása.

Ellentétben a hagyományos gyorsítótárakat, amelyek csak a kulcs-érték párok kezeléséhez a Redis általában esetén a rendkívül nagy teljesítményt nyújtva adattípusokat. Redis is támogat, ezek a típusok, pl. hozzáfűzése egy karakterlánc; atomi művelet futtatása az érték a kivonatot; növekszik hogyan lehet továbbítani rá egy listához; számítási metszet beállítása, a union és a különbség a; vagy a tag első egy rendezett készlet a legmagasabb prioritást. További funkciók között tranzakciók, pub/sub, Lua-szkriptek, kulcsok korlátozott time-to-live, és konfigurációs beállítások több viselkednek, mint egy hagyományos cache Redis támogatása.

A Redis sikeres egy másik fontos szempontja a kifogástalan állapotú, élénk nyílt forráskódú ökoszisztéma épülő projektszolgáltatásokat,. Ez több különböző nyelven érhető el a Redis-ügyfelek sokféle tükröződik. Az ökoszisztéma és az ügyfelek számos különböző lehetővé teszik az Azure Redis Cache szinte bármilyen számítási feladatot, akkor létre Azure-beli megoldásban használható.

Azure Redis Cache használatának első lépéseivel kapcsolatos további információkért lásd: [How to Azure Redis Cache használata](cache-dotnet-how-to-use-azure-redis-cache.md) és [Azure Redis Cache dokumentációja](index.md).

### <a name="managed-cache-service"></a>A felügyelt gyorsítótár-szolgáltatás
[A felügyelt gyorsítótár szolgáltatás 2016. November 30-volt elavult.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Archivált dokumentáció megtekintéséhez lásd: [archivált Managed Cache Service dokumentációja](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Szerepköralapú gyorsítótár
[A szerepköralapú gyorsítótár a 2016. November 30-volt elavult.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Archivált dokumentáció megtekintéséhez lásd: [archivált In-Role Cache-dokumentáció](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
