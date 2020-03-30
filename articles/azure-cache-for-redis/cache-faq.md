---
title: Azure Cache for Redis – Gyakori kérdések
description: Ismerje meg a válaszokat az Azure Cache for Redis gyakori kérdéseire, mintáira és bevált gyakorlataira
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: ddf7999153e9d9722e627d148b116750fe3aaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278713"
---
# <a name="azure-cache-for-redis-faq"></a>Azure Cache for Redis – Gyakori kérdések
Ismerje meg a válaszokat az Azure Cache for Redis gyakori kérdéseire, mintáira és ajánlott eljárásokra.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi van, ha a kérdésemre nem kap választ?
Ha kérdése nem szerepel itt, tudassa velünk, és mi segítünk megtalálni a választ.

* Feltehet egy kérdést a gyakori kérdések végén található megjegyzésekben, és kapcsolatba léphet az Azure Cache csapatával és a közösség más tagjaival erről a cikkről.
* Szélesebb közönség elérése érdekében feltehet egy kérdést az [Azure Cache MSDN fórumon,](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) és kapcsolatba léphet az Azure Cache csapatával és a közösség többi tagjával.
* Ha szolgáltatáskérelmet szeretne benyújtani, elküldheti kéréseit és ötleteit az [Azure Cache for Redis User Voice szolgáltatásba.](https://feedback.azure.com/forums/169382-cache)
* E-mailt is küldhet nekünk az [Azure Cache külső visszajelzése i.](mailto:azurecache@microsoft.com)

## <a name="azure-cache-for-redis-basics"></a>Azure Cache for Redis alapjai
Az ebben a szakaszban található gyakori kérdések az Azure Cache for Redis néhány alapvető tudnivalóját ismertetik.

* [Mi az az Azure Cache for Redis?](#what-is-azure-cache-for-redis)
* [Hogyan kezdhetem el az Azure Cache for Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Az alábbi gyakori kérdések az Azure Cache for Redis alapfogalmait és kérdéseit ismertetik, és a többi GYIK-szakaszban válaszolnak.

* [Milyen Azure-gyorsítótárat kell használni a Redis-ajánlathoz és a mérethez?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Milyen Azure-gyorsítótárat használhatok a Redis-ügyfelek számára?](#what-azure-cache-for-redis-clients-can-i-use)
* [Van helyi emulátor az Azure Cache for Redis számára?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hogyan figyelhetem a gyorsítótár állapotát és teljesítményét?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Tervezési gyik
* [Milyen Azure-gyorsítótárat kell használni a Redis-ajánlathoz és a mérethez?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure-gyorsítótár a Redis teljesítményéhez](#azure-cache-for-redis-performance)
* [Milyen régióban keressem meg a gyorsítótárat?](#in-what-region-should-i-locate-my-cache)
* [Hogyan kell kiszámláznom az Azure Cache for Redis-t?](#how-am-i-billed-for-azure-cache-for-redis)
* [Használhatom az Azure Cache for Redis-t az Azure Government Cloud, az Azure China Cloud vagy a Microsoft Azure Germany használatával?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Fejlesztési gyik
* [Mire történik a StackExchange.Redis konfigurációs beállítások?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Milyen Azure-gyorsítótárat használhatok a Redis-ügyfelek számára?](#what-azure-cache-for-redis-clients-can-i-use)
* [Van helyi emulátor az Azure Cache for Redis számára?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hogyan futtathatom a Redis parancsokat?](#how-can-i-run-redis-commands)
* [Miért nem rendelkezik az Azure Cache for Redis egy MSDN-osztálykönyvtár-referencia, mint néhány más Azure-szolgáltatások?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Használhatom az Azure Cache for Redis-t PHP-munkamenet-gyorsítótárként?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Mik azok a Redis adatbázisok?](#what-are-redis-databases)

## <a name="security-faqs"></a>Biztonsági gyakori kérdések
* [Mikor engedélyezzem a nem SSL-portot a Redis-hez való csatlakozáshoz?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Gyártási gyIK
* [Melyek a termelés sel kapcsolatos bevált gyakorlatai?](#what-are-some-production-best-practices)
* [Milyen szempontok at használ a közös Redis parancsok használatakor?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hogyan tesztelhetem és tesztelhetem a gyorsítótár teljesítményét?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Fontos tudnivalók a ThreadPool növekedéséről](#important-details-about-threadpool-growth)
* [A kiszolgáló globális katalógusának engedélyezése, hogy nagyobb átviteli érték keljen az ügyfélen a StackExchange.Redis használata esetén](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [A kapcsolatok teljesítményével kapcsolatos szempontok](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Gyakori kérdések figyelése és hibaelhárítása
Az ebben a szakaszban található gyakori kérdések a gyakori figyelési és hibaelhárítási kérdéseket ismertetik. Az Azure-gyorsítótár Redis-példányok figyeléséről és hibaelhárításáról a [Hogyan figyelheti az Azure Cache for Redis-t](cache-how-to-monitor.md) és a különböző hibaelhárítási útmutatókat.

* [Hogyan figyelhetem a gyorsítótár állapotát és teljesítményét?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Miért látok időkiszabadságot?](#why-am-i-seeing-timeouts)
* [Miért lett leválasztva az ügyfelem a gyorsítótárról?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Korábbi gyorsítótár-ajánlatok – gyakori kérdések
* [Melyik Azure Cache-ajánlat a megfelelő számomra?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Mi az az Azure Cache for Redis?
Az Azure Cache for Redis a népszerű nyílt forráskódú [redis](https://redis.io/)szoftveren alapul. Hozzáférést biztosít egy biztonságos, dedikált Azure-gyorsítótárhoz a Redis számára, amelyet a Microsoft kezel, és amely az Azure-on belül bármely alkalmazásból elérhető. További részletes áttekintést az [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) termékoldalán Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hogyan kezdhetem el az Azure Cache for Redis?
Számos módja van az Azure Cache for Redis használatával.

* A [.NET,](cache-dotnet-how-to-use-azure-redis-cache.md) [a ASP.NET,](cache-web-app-howto.md)a [Java](cache-java-get-started.md), [a Node.js](cache-nodejs-get-started.md)és a [Python](cache-python-get-started.md)elérhető oktatóanyagaink közül választhat.
* [A Microsoft Azure Cache for Redis segítségével](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)megtekintheti a nagy teljesítményű alkalmazások készítésének módját.
* A Redis használatának megtekintéséhez tekintse meg a projekt fejlesztési nyelvének megfelelő ügyfelek ügyféldokumentációját. Sok Redis-ügyfél, amely használható az Azure Cache for Redis. A Redis-ügyfelek listáját [https://redis.io/clients](https://redis.io/clients)lásd: .

Ha még nem rendelkezik Azure-fiókkal, a következőket teheti:

* [Nyisson egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a keretét el is használta, továbbra is megtarthatja a fiókot, és használhatja az ingyenes szolgáltatásokat és lehetőségeket.
* [Aktiválja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Milyen Azure-gyorsítótárat kell használni a Redis-ajánlathoz és a mérethez?
Minden Egyes Azure Cache redis kínál különböző **méretű,** **sávszélesség,** **magas rendelkezésre állású,** és **SLA-lehetőségek.**

A gyorsítótár-ajánlat kiválasztásával kapcsolatban az alábbiakban felkel.

* **Memória:** Az alapszintű és a standard szintű csomagok 250 MB – 53 GB-ot kínálnak. A prémium szint legfeljebb 1,2 TB-ot (fürtként) vagy 120 GB-ot (nem fürtözött) kínál. További információ: [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Hálózati teljesítmény:** Ha olyan számítási feladattal rendelkezik, amely nagy átviteli csatornát igényel, a prémium szintű szint nagyobb sávszélességet kínál, mint a Standard vagy az Alapszintű. Az egyes rétegeken belül is nagyobb méretű gyorsítótárak nagyobb sávszélességet, mert az alapul szolgáló virtuális gép, amely a gyorsítótárat üzemelteti. További információt az [alábbi táblázatban talál.](#cache-performance)
* **Átviteli teljesítmény:** A prémium szintű csomag a maximális rendelkezésre álló átviteli. Ha a gyorsítótár-kiszolgáló vagy az ügyfél eléri a sávszélesség-korlátokat, időtúltöltéseket kaphat az ügyféloldalon. További információt az alábbi táblázat tartalmaz.
* **Magas rendelkezésre állás/SLA:** Az Azure Cache for Redis garantálja, hogy a standard/prémium szintű gyorsítótár az idő legalább 99,9%-ában elérhető. Az SLA-ról az [Azure Cache for Redis pricing](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)című témakörben olvashat bővebben. Az SLA csak a gyorsítótár végpontjaihoz való kapcsolódást fedezi. Az SLA nem terjed ki az adatvesztés elleni védelemre. Azt javasoljuk, hogy a Redis adatmegőrzési funkció a prémium szinten, hogy növelje az adatvesztés sel szembeni rugalmasság növelése.
* **Redis data persistence:** A prémium szint lehetővé teszi, hogy egy Azure Storage-fiókban a gyorsítótár-adatok megőrzése. Az alapszintű/szabványos gyorsítótárban az összes adat csak a memóriában tárolódik. Az alapul szolgáló infrastrukturális problémák adatvesztést okozhatnak. Azt javasoljuk, hogy a Redis adatmegőrzési funkció a prémium szinten, hogy növelje az adatvesztés sel szembeni rugalmasság növelése. Az Azure Cache for Redis RDB és AOF (hamarosan) lehetőségeket kínál a Redis persistence.Azure Cache for Redis offers RDB and AOF (soon) options in Redis persistence. További információ: [A Redis prémium szintű Azure-gyorsítótárának adatmegőrzési beállítása](cache-how-to-premium-persistence.md)című témakörben talál.
* **Redis Cluster:** 120 GB-nál nagyobb gyorsítótárak létrehozásához vagy több Redis-csomóponton lévő adatok szilánkos lesújtására használhatja a Redis-fürtözést, amely a prémium szinten érhető el. Minden csomópont egy elsődleges/replika-gyorsítótárpárból áll a magas rendelkezésre állás érdekében. További információ: [A fürtözés konfigurálása prémium szintű Azure-gyorsítótárhoz a Redis hez című témakörben.](cache-how-to-premium-clustering.md)
* **Fokozott biztonság és hálózati elkülönítés:** Az Azure Virtual Network (VNET) telepítése fokozott biztonságot és elkülönítést biztosít az Azure Cache for Redis számára, valamint az alhálózatok, a hozzáférés-vezérlési szabályzatok és egyéb funkciók számára a hozzáférés további korlátozása érdekében. További információ: [A virtuális hálózat támogatáskonfigurálása a Prémium szintű Azure-gyorsítótár redishez című témakörben.](cache-how-to-premium-vnet.md)
* **Redis konfigurálása:** A standard és a prémium szintű csomagokban is konfigurálhatja a Redis keyspace értesítésekhez.
* **Az ügyfélkapcsolatok maximális száma**: A prémium szintű csomag a Redis-hez csatlakozni képes ügyfelek maximális számát kínálja, nagyobb méretű gyorsítótárak esetén nagyobb számú kapcsolattal. A fürtözés nem növeli a fürtözött gyorsítótárhoz elérhető kapcsolatok számát. További információ: [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Dedikált Core for Redis Server:** A prémium szinten minden gyorsítótárméret rendelkezik egy dedikált maggal a Redis számára. Az alapszintű/standard szinteken a C1-es és újabb méretegy dedikált maggal rendelkezik a Redis-kiszolgálóhoz.
* **A Redis egyszálas,** így a kettőnél több mag nem nyújt további előnyt a két maghoz, de a nagyobb virtuálisgép-méretek általában nagyobb sávszélességgel rendelkeznek, mint a kisebb méretek. Ha a gyorsítótár-kiszolgáló vagy az ügyfél eléri a sávszélesség-korlátokat, akkor időtúltöltéseket kap az ügyféloldalon.
* **Teljesítménybeli fejlesztések:** A prémium szintű gyorsítótárak gyorsabb processzorokkal rendelkező hardveren vannak telepítve, ami jobb teljesítményt nyújt az alapszintű vagy a standard szinthez képest. A prémium szintű gyorsítótárak nagyobb átviteli és alacsonyabb késési.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure-gyorsítótár a Redis teljesítményéhez
Az alábbi táblázat bemutatja a maximális sávszélesség-értékeket, amelyeket a standard és prémium szintű gyorsítótárak különböző méretű, Az Azure-gyorsítótár redis végponton használt `redis-benchmark.exe` szabványos és prémium szintű gyorsítótárak tesztelése során figyelt meg. Az SSL átviteli teljesítmény, redis-benchmark stunnel használatával csatlakozik az Azure Cache a Redis végpont.

>[!NOTE] 
>Ezek az értékek nem garantáltak, és ezekhez a számokhoz nincs SLA, de tipikusnak kell lenniük. Be kell töltenie a saját alkalmazás betöltése az alkalmazás megfelelő gyorsítótárméretének meghatározásához.
>Ezek a számok változhatnak, amint rendszeresidőközönként közzétesszük az újabb eredményeket.
>

Ebből a táblázatból a következő következtetéseket vonhatjuk le:

* Az azonos méretű gyorsítótárak átviteli igénye magasabb a prémium szinten, mint a standard szint. 6 GB-os gyorsítótár esetén például a P1 átviteli igény180 000 kérelem másodpercenként (RPS), szemben a C3 100 000 RPS-ével.
* A Redis fürtözés, átviteli hálózat lineárisan növekszik, ahogy növeli a szegmensek (csomópontok) a fürtben. Például ha 10 szegmensből álló P4-fürtöt hoz létre, akkor a rendelkezésre álló átviteli érték 400 000 * 10 = 4 millió RPS.
* A nagyobb kulcsméretek átviteli ideje magasabb a prémium szinten, mint a standard szint.

| Tarifacsomag | Méret | Processzormagok | Rendelkezésre álló sávszélesség | 1 KB-os értékméret | 1 KB-os értékméret |
| --- | --- | --- | --- | --- | --- |
| **Szabványos gyorsítótárméretek** | | |**Megabit/mp (Mb/s) / Megabájt másodpercenként (MB/s)** |**Kérelmek másodpercenként (RPS) nem SSL** |**Kérelmek másodpercenként (RPS) SSL** |
| C0 | 250 MB | Megosztott | 100 / 12.5  |  15 000 |   7500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100 000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60.000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Prémium szintű gyorsítótárméretek** | |**Processzormagok szegmensenként** | **Megabit/mp (Mb/s) / Megabájt másodpercenként (MB/s)** |**Kérelmek másodpercenként (RPS) nem SSL, szegmensenként** |**Kérelmek másodpercenként (RPS) SSL, szegmensenként** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

A stunnel beállításával vagy a Redis-eszközök `redis-benchmark.exe`letöltésével kapcsolatos útmutatásért tekintse meg a [Hogyan futtathatom a Redis parancsokat?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Milyen régióban keressem meg a gyorsítótárat?
A legjobb teljesítmény és a legalacsonyabb késés érdekében keresse meg az Azure Cache for Redis ugyanabban a régióban, mint a cache-ügyfélalkalmazás.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Hogyan kell kiszámláznom az Azure Cache for Redis-t?
Az Azure Cache for Redis [díjszabása itt](https://azure.microsoft.com/pricing/details/cache/)található. Az árképzési oldal az árképzést óradíjként sorolja fel. A gyorsítótárak számlázása a gyorsítótár létrehozásától a gyorsítótár törléséig eltelt időtől percenként történik. Nincs lehetőség a gyorsítótár számlázásának leállítására vagy szüneteltetésére.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Használhatom az Azure Cache for Redis-t az Azure Government Cloud, az Azure China Cloud vagy a Microsoft Azure Germany használatával?
Igen, az Azure Cache for Redis elérhető az Azure Government Cloud, az Azure China 21Vianet Cloud és a Microsoft Azure Germany szolgáltatásban. A Redis Azure Cache eléréséhez és kezeléséhez szükséges URL-címek ezekben a felhőkben különböznek az Azure Public Cloud-tól.

| Felhő   | A Redis DNS-utótagja            |
|---------|---------------------------------|
| Nyilvános  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Németország | *.redis.cache.cloudapi.de       |
| Kína   | *.redis.cache.chinacloudapi.cn  |

Az Azure Cache for Redis más felhőkkel való használata során az alábbi témakörökben további információt talál.

- [Azure Government-adatbázisok – Azure-gyorsítótár a Redis-hez](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud – Azure-gyorsítótár a Redis-hez](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Az Azure Cache for Redis és a PowerShell azure kormányzati felhőben, az Azure China 21Vianet Cloud és a Microsoft Azure Germany szolgáltatásban való használatáról a [Hogyan csatlakozhat más felhőkhöz – Azure Cache for Redis PowerShell.](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Mire történik a StackExchange.Redis konfigurációs beállítások?
StackExchange.Redis számos lehetőség közül választhat. Ez a szakasz a gyakori beállításokról szól. A StackExchange.Redis beállításairól a [StackExchange.Redis konfigurációja című](https://stackexchange.github.io/StackExchange.Redis/Configuration)témakörben talál további információt.

| ConfigurationOptions | Leírás | Ajánlás |
| --- | --- | --- |
| AbortOnConnectFail |Ha a beállítás igaz, a kapcsolat hálózati hiba után nem csatlakozik újra. |Állítsa a false értéket, és hagyja, hogy a StackExchange.Redautomatikusan újracsatlakozzon. |
| ConnectRetry |A csatlakozási kísérletek megismétlésének száma a kezdeti csatlakozás során. |Az alábbi megjegyzésekben talál útmutatást. |
| ConnectTimeout |Időelés ms-ban a csatlakozási műveletekhez. |Az alábbi megjegyzésekben talál útmutatást. |

Általában az ügyfél alapértelmezett értékei elegendőek. A beállításokat a munkaterhelés alapján finomíthatja.

* **Újrapróbálkozások**
  * A ConnectRetry és a ConnectTimeout esetében az általános útmutató az, hogy gyorsan sikertelen, majd próbálkozzon újra. Ez az útmutató a számítási feladatok, és mennyi időt vesz igénybe átlagosan az ügyfél, hogy kiadja a Redis parancsot, és választ kap.
  * Hagyja, hogy a StackExchange.Redis automatikusan újra csatlakozzon a kapcsolat állapotának ellenőrzése és saját maga újracsatlakoztatása helyett. **Ne használja a ConnectionMultiplexer.IsConnected tulajdonságot.**
  * Snowballing - néha előfordulhat, hogy befut egy kérdés, ahol újra és az újrapróbálkozások hógolyó, és soha nem épül fel. Ha hógolyózás történik, fontolja meg egy exponenciális visszafordulási újrapróbálkozási algoritmus használatát a Microsoft Patterns & Practices csoport által közzétett [Általános útmutató újrapróbálkozása](../best-practices-retry-general.md) című dokumentumban leírtak szerint.
  
* **Idő-eltinési értékek**
  * Vegye figyelembe a számítási feladatokat, és ennek megfelelően állítsa be az értékeket. Ha nagy értékeket tárol, állítsa az időtúlértéket magasabb értékre.
  * Állítsa `AbortOnConnectFail` hamisra, és hagyja, hogy a StackExchange.Redis újra csatlakozzon.
  * Egyetlen ConnectionMultiplexer-példány használata az alkalmazáshoz. A LazyConnection segítségével egyetlen példányt hozhat létre, amelyet egy Connection tulajdonság ad vissza, [ahogy az a Csatlakozás a gyorsítótárhoz a ConnectionMultiplexer osztály használatával című részben](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)látható.
  * Állítsa `ConnectionMultiplexer.ClientName` be a tulajdonságot egy alkalmazáspéldány egyedi nevére diagnosztikai célokra.
  * Használjon `ConnectionMultiplexer` több példányt az egyéni számítási feladatokhoz.
      * Ezt a modellt akkor követheti, ha az alkalmazás ban változó terhelés van. Példa:
      * Lehet egy multiplexer kezelésére nagy kulcsokat.
      * Lehet egy multiplexer kezelésére kis kulcsokat.
      * A kapcsolatidő-túllépéshez különböző értékeket állíthat be, és újrapróbálkozhat a használatával rendelkező minden egyes ConnectionMultiplexer-hez.
      * Állítsa `ClientName` be a tulajdonságot minden multiplexeren, hogy segítsen a diagnosztikában.
      * Ez az útmutató a/ `ConnectionMultiplexer`onkénti ésszerűbb késleltetéshez vezethet.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Milyen Azure-gyorsítótárat használhatok a Redis-ügyfelek számára?
Az egyik nagy dolog a Redis, hogy sok ügyfél támogatja a különböző fejlesztési nyelvek. Az ügyfelek aktuális listáját a [Redis-ügyfelek című témakörben tetszetős.](https://redis.io/clients) A több különböző nyelvet és ügyfelet lefedő oktatóanyagokról az [Azure Cache használata a Redis-hez](cache-dotnet-how-to-use-azure-redis-cache.md) című témakörben és a tartalomjegyzékben található testvércikkekben című témakörben található.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Van helyi emulátor az Azure Cache for Redis számára?
Nincs helyi emulátor az Azure Cache for Redis számára, de futtathatja a redis-server.exe MSOpenTech verzióját a helyi gépen lévő [Redis parancssori eszközökről,](https://github.com/MSOpenTech/redis/releases/) és csatlakozhat hozzá, hogy hasonló élményt kapjon a helyi gyorsítótár-emulátorhoz, amint az a következő példában látható:

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


A [redis.conf](https://redis.io/topics/config) fájlt beállíthatja úgy, hogy jobban megfeleljen az online Azure-gyorsítótár redis-i [alapértelmezett gyorsítótár-beállításainak,](cache-configure.md#default-redis-server-configuration) ha szükséges.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hogyan futtathatom a Redis parancsokat?
A [Redis-parancsokban](https://redis.io/commands#) felsorolt parancsok bármelyikét használhatja, kivéve az [Azure Cache for Redis által nem támogatott Redis-parancsokban](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)felsorolt parancsokat. A Redis parancsok futtatására több lehetőség is van.

* Ha standard vagy prémium szintű gyorsítótárral rendelkezik, a [Redis konzollal](cache-configure.md#redis-console)futtathatja a Redis parancsokat. A Redis konzol biztonságos módot biztosít a Redis-parancsok futtatására az Azure Portalon.
* A Redis parancssori eszközöket is használhatja. Használatukhoz hajtsa végre az alábbi lépéseket:
* Töltse le a [Redis parancssori eszközeit.](https://github.com/MSOpenTech/redis/releases/)
* Csatlakozás a gyorsítótárhoz a használatával. `redis-cli.exe` Adja át a gyorsítótár végpontját a -h kapcsolóval és a kulcs -a használatával, ahogy az a következő példában látható:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> A Redis parancssori eszközök nem működnek az SSL-porttal, `stunnel` de egy segédprogramot használhat, például biztonságosan csatlakoztathatja az eszközöket az SSL-porthoz a [Redis parancssori eszköz használata az Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) cikkben található utasításokat követve.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Miért nem rendelkezik az Azure Cache for Redis egy MSDN-osztálykönyvtár-referencia, mint néhány más Azure-szolgáltatások?
A Microsoft Azure Cache for Redis a népszerű nyílt forráskódú Azure-gyorsítótáron alapul a Redis számára. A [Redis ügyfelek](https://redis.io/clients) széles választéka számos programozási nyelven elérhető. Minden ügyfél saját API-val rendelkezik, amely [redis-parancsokkal](https://redis.io/commands)hívásokat kezdeményez az Azure Cache for Redis-példányhoz.

Mivel minden ügyfél más, nincs egy központi osztályhivatkozás az MSDN-en, és minden ügyfél saját referenciadokumentációt tart fenn. A referenciadokumentáció mellett számos oktatóanyag is található, amelyek bemutatják, hogyan kezdheti el az Azure Cache for Redis különböző nyelveket és gyorsítótárazási ügyfeleket. Ezekhez az oktatóanyagokhoz való hozzáférés, [lásd: Az Azure Cache használata a Redis](cache-dotnet-how-to-use-azure-redis-cache.md) és a testvér cikkek a tartalomjegyzékben.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Használhatom az Azure Cache for Redis-t PHP-munkamenet-gyorsítótárként?
Igen, az Azure Cache for Redis PHP-munkamenet-gyorsítótárként való használatához adja meg `session.save_path`a redis-példány Azure-gyorsítótárának kapcsolati karakterláncát a alkalmazásban.

> [!IMPORTANT]
> Ha az Azure Cache for Redis-t PHP-munkamenet-gyorsítótárként használja, url-címvel kell kódolnia a gyorsítótárhoz való csatlakozáshoz használt biztonsági kulcsot, amint az a következő példában látható:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Ha a kulcs nem URL-kódolva van, kivételt kaphat a következő üzenettel:`Failed to parse session.save_path`
>
>

Az Azure Cache for Redis PHP-munkamenet-gyorsítótárként a PhpRedis ügyféllel való használatáról a [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler)című témakörben talál további információt.

### <a name="what-are-redis-databases"></a>Mik azok a Redis adatbázisok?

A Redis-adatbázisok csak az adatok logikai elkülönítése ugyanazon a Redis-példányon belül. A gyorsítótár-memória meg van osztva az összes adatbázis között, és egy adott adatbázis tényleges memóriafelhasználása az adatbázisban tárolt kulcsoktól/értékektől függ. Egy C6-gyorsítótár például 53 GB memóriával, a P5 pedig 120 GB memóriával rendelkezik. Választhat, hogy az összes 53 GB / 120 GB-ot egyetlen adatbázisba helyezi, vagy feloszthatja több adatbázis között. 

> [!NOTE]
> Ha a fürtözés engedélyezve van a Redis prémium szintű Azure-gyorsítótárahasználata esetén, csak a 0-s adatbázis érhető el. Ez a korlátozás egy belső Redis korlátozás, és nem kifejezetten az Azure Cache for Redis. További információ: [Az ügyfélalkalmazáson módosításokat kell végeznem a fürtözés használatához?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Mikor engedélyezzem a nem SSL-portot a Redis-hez való csatlakozáshoz?
Redis-kiszolgáló natív módon nem támogatja az SSL, de az Azure Cache for Redis nem. Ha csatlakozik az Azure Cache for Redis és az ügyfél támogatja az SSL, például StackExchange.Redis, majd ssl-t kell használnia.

>[!NOTE]
>A nem SSL-port alapértelmezés szerint le van tiltva az új Azure-gyorsítótár redis példányok. Ha az ügyfél nem támogatja az SSL-t, akkor engedélyeznie kell a nem SSL-portot a [Redis-gyorsítótár konfigurálása](cache-configure.md) az Azure Cache for Redis cikk [Access ports](cache-configure.md#access-ports) szakaszában található utasításokat követve.
>
>

Redis eszközök, `redis-cli` mint például nem működnek az SSL-port, de egy segédprogram, például `stunnel` biztonságosan csatlakoztathatja az eszközöket az SSL-port követve az utasításokat a [beiktatása ASP.NET munkamenet állapotszolgáltató redis preview release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogbejegyzést.

A Redis-eszközök letöltésével kapcsolatos tudnivalókat a [Hogyan futtathatom a Redis parancsok?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Melyek a termelés sel kapcsolatos bevált gyakorlatai?
* [A StackExchange.Redis ajánlott eljárása](#stackexchangeredis-best-practices)
* [Konfiguráció és koncepciók](#configuration-and-concepts)
* [Teljesítménytesztelés](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>A StackExchange.Redis ajánlott eljárása
* Állítsa `AbortConnect` hamisra, majd hagyja, hogy a ConnectionMultiplexer automatikusan újracsatlakozzon. [Lásd itt a részleteket](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Használja fel újra a ConnectionMultiplexer - ne hozzon létre egy újat minden kérelmet. Az `Lazy<ConnectionMultiplexer>` [itt látható](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) minta ajánlott.
* A Redis kisebb értékekkel működik a legjobban, ezért fontolja meg a nagyobb adatok több kulcsra történő feldarabolását. [Ebben a Redis vita](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb tekinthető nagy. Olvassa el ezt a [cikket](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) egy példa probléma, amely által okozott nagy értékek.
* Konfigurálja a [ThreadPool-beállításokat](#important-details-about-threadpool-growth) az időmeghosszabbítások elkerülése érdekében.
* Használja legalább az alapértelmezett connectTimeout 5 másodperc. Ez az időköz elegendő időt ad a StackExchange.Redis-nek a kapcsolat helyreállítására hálózati blip esetén.
* Legyen tisztában a különböző futtatott műveletekteljesítmény-költségeivel. Például a `KEYS` parancs egy O(n) művelet, és el kell kerülni. A [redis.io hely](https://redis.io/commands/) az általa támogatott műveletek időösszetettségével kapcsolatos részleteket tartalmaz. Az egyes műveletek összetettségének megtekintéséhez kattintson az egyes parancsokra.

#### <a name="configuration-and-concepts"></a>Konfiguráció és koncepciók
* Használja a standard vagy prémium szintű termelési rendszerek. Az Alapszint egyetlen csomópontot tartalmaz adatreplikáció nélkül, és SLA sem tartozik hozzá. Ezen kívül használjon legalább C1 szintű gyorsítótárat. C0-gyorsítótárak általában egyszerű fejlesztési/tesztelési forgatókönyvek.
* Ne feledje, hogy a Redis egy **memórián belüli** adattár. Olvassa el [ezt a cikket,](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) hogy tisztában lévén esetek, ahol adatvesztés fordulhat elő.
* Fejlessze a rendszert úgy, hogy képes legyen kezelni a csatlakozási blips [miatt folt és feladatátvétel](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teljesítménytesztelés
* Kezdje `redis-benchmark.exe` azzal, hogy megérzi a lehetséges átviteli, mielőtt megírja a saját perf tesztek. Mivel `redis-benchmark` nem támogatja az SSL,a teszt futtatása előtt engedélyeznie kell [a nem SSL-port az Azure Portalon keresztül.](cache-configure.md#access-ports) Példák: [Hogyan tesztelhetem és tesztelhetem a gyorsítótár teljesítményét?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* A teszteléshez használt ügyfél virtuális gépnek ugyanabban a régióban kell lennie, mint az Azure Cache for Redis-példánynak.
* Javasoljuk, hogy a Dv2 VM sorozat az ügyfél, mivel azok jobb hardver, és meg kell adni a legjobb eredményt.
* Győződjön meg arról, hogy a választott ügyfél virtuális gép legalább annyi számítási és sávszélesség-képességgel rendelkezik, mint a tesztelt gyorsítótár.
* Engedélyezze a VRSS-t az ügyfélszámítógépen, ha Windows rendszert szeretne. [Lásd itt a részleteket](https://technet.microsoft.com/library/dn383582.aspx).
* A prémium szintű Redis-példányok jobb hálózati késést és átviteli csatornát, mert futnak a jobb hardver mind a CPU és a hálózat.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Milyen szempontok at használ a közös Redis parancsok használatakor?

* Ne használjon olyan Redis parancsokat, amelyek hosszú időt vesz igénybe, kivéve, ha teljes mértékben tisztában van ezeknek a parancsoknak a hatásával. Például ne futtassa a [KEYS](https://redis.io/commands/keys) parancsot éles környezetben. A kulcsok számától függően a visszatérés hosszú időt vehet igénybe. A Redis egyszálas kiszolgáló, amely egyenként dolgozza fel a parancsokat. Ha a KEYS után más parancsokat is kiadott, azok nem lesznek feldolgozva, amíg a Redis fel nem dolgozza a KEYS parancsot. A [redis.io hely](https://redis.io/commands/) az általa támogatott műveletek időösszetettségével kapcsolatos részleteket tartalmaz. Az egyes műveletek összetettségének megtekintéséhez kattintson az egyes parancsokra.
* Kulcsméretek - használjak kis kulcsot/értékeket vagy nagy kulcsot/értékeket? A forgatókönyvtől függ. Ha a forgatókönyv nagyobb kulcsokat igényel, módosíthatja a ConnectionTimeout-ot, majd újrapróbálkozhat az értékekkel, és módosíthatja az újrapróbálkozási logikát. A Redis kiszolgáló szempontjából a kisebb értékek jobb teljesítményt nyújtanak.
* Ezek a szempontok nem jelentik azt, hogy nem tárolhat nagyobb értékeket a Redis-ben; tisztában kell lennie az alábbi szempontokkal. A késések magasabbak lesznek. Ha egy nagyobb és kisebb adatkészlettel rendelkezik, több ConnectionMultiplexer-példányt is használhat, amelyek mindegyike más időtúllépési és újrapróbálkozási értékekkel van konfigurálva, ahogy azt a [StackExchange.Redis konfigurációs beállítások](#cache-configuration) című szakaszismertetiban leírtak szerint ismertetett.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hogyan tesztelhetem és tesztelhetem a gyorsítótár teljesítményét?
* [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát. Megtekintheti a metrikákat az Azure Portalon, és [letöltheti és áttekintheti](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) őket az Ön által választott eszközökkel.
* A redis-benchmark.exe segítségével tesztelheti a Redis-kiszolgálót.
* Győződjön meg arról, hogy a terheléstesztelési ügyfél és az Azure Cache a Redis ugyanabban a régióban.
* Használja a redis-cli.exe parancsot, és figyelje a gyorsítótárat az INFO paranccsal.
* Ha a terhelés okozza a memória nagy töredezettségét, nagyobb gyorsítótárméretre kell méretezhetőt.
* A Redis-eszközök letöltésével kapcsolatos tudnivalókat a [Hogyan futtathatom a Redis parancsok?](#cache-commands)

A következő parancsok példát mutatnak be a redis-benchmark.exe használatára. A pontos eredmények érdekében futtassa ezeket a parancsokat egy virtuális gép ugyanabban a régióban, mint a gyorsítótár.

* Folyamathoz tartozó SET-kérelmek tesztelése 1k hasznos adathasználatával

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Tesztelje a futószalagos GET-kérelmeket 1k hasznos adat használatával.
  MEGJEGYZÉS: Futtassa a fenti SET tesztet először a gyorsítótár feltöltéséhez

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Fontos tudnivalók a ThreadPool növekedéséről
A CLR ThreadPool kétféle szálat kínál : "Worker" és "I/O Completion Port" (IOCP) szálakat.

* A munkaszálak olyan műveletekhez `Task.Run(…)`használatosak, mint a a vagy a vagy `ThreadPool.QueueUserWorkItem(…)` a metódus feldolgozása. Ezeket a szálakat a CLR különböző összetevői is használják, amikor a munkát egy háttérszálon kell megtörténnie.
* Az IOCP-szálak akkor használatosak, amikor aszinkron IO történik, például a hálózatról történő olvasáskor.

A szálkészlet igény szerint új munkaszálakat vagy I/O-befejezési szálakat biztosít (szabályozás nélkül), amíg el nem éri az egyes szálak "Minimális" beállítását. Alapértelmezés szerint a szálak minimális száma a rendszeren lévő processzorok számára van beállítva.

Miután a meglévő (foglalt) szálak száma eléri a szálak "minimális" számát, a ThreadPool szabályozza az új szálak 500 ezredmásodpercenkénti egy szálba való befecskendezésének sebességét. Általában, ha a rendszer kap egy tört a munka igénylő IOCP szál, akkor gyorsan feldolgozza, hogy a munka. Ha azonban a munkasorozat több, mint a beállított "Minimum" beállítás, akkor a munka egy részének feldolgozása némi késéssel történik, mivel a Szálkészlet két dolog valamelyikére vár.

1. Egy meglévő szál szabadon feldolgozható a munka.
2. Egyetlen meglévő szál sem lesz 500 ms-ra szabadon, így új szál jön létre.

Alapvetően ez azt jelenti, hogy ha a foglalt szálak száma nagyobb, mint a Min szálak száma, akkor valószínűleg 500 ms-os késleltetést fizet, mielőtt az alkalmazás feldolgozza a hálózati forgalmat. Is, fontos megjegyezni, hogy ha egy meglévő szál marad tétlen hosszabb, mint 15 másodperc (alapján, amit emlékszem), akkor meg kell tisztítani, és ez a ciklus a növekedés és a zsugorodás is ismételje meg.

Ha megtekintünk egy példa hibaüzenetet StackExchange.Redis (build 1.0.450 vagy újabb), látni fogja, hogy most nyomtat ThreadPool statisztikák (lásd az IOCP és a WORKER részleteket alább).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Az előző példában láthatja, hogy az IOCP-szál esetében hat foglalt szál van, és a rendszer úgy van beállítva, hogy négy minimális szálat engedélyez. Ebben az esetben az ügyfél valószínűleg látott volna két 500 ms-os késéseket, mert 6 > 4.

Vegye figyelembe, hogy a StackExchange.Redis időtúltöltéseket érhet el, ha az IOCP vagy a WORKER szálak növekedését szabályozza.

### <a name="recommendation"></a>Ajánlás

Ezen információk alapján javasoljuk, hogy az ügyfelek az IOCP és a WORKER szálak minimális konfigurációs értékét az alapértelmezett értéknél nagyobb értékre állítsa be. Nem adhatunk egy kaptafára útmutatást arról, hogy mi legyen ez az érték, mert az egyik alkalmazás megfelelő értéke valószínűleg túl magas vagy alacsony egy másik alkalmazásszámára. Ez a beállítás a bonyolult alkalmazások más részeinek teljesítményét is befolyásolhatja, ezért minden ügyfélnek finomhangolnia kell ezt a beállítást a saját igényeinek megfelelően. Egy jó kiindulási hely 200 vagy 300, majd tesztelje és csípés, ha szükséges.

A beállítás konfigurálása:

* Javasoljuk, hogy programozott módon módosítsa ezt a beállítást a [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) metódus használatával a alkalmazásban. `global.asax.cs` Példa:

```cs
private readonly int minThreads = 200;
void Application_Start(object sender, EventArgs e)
{
    // Code that runs on application startup
    AreaRegistration.RegisterAllAreas();
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
    ThreadPool.SetMinThreads(minThreads, minThreads);
}
```

  > [!NOTE]
  > Az ezzel a módszerrel megadott érték globális beállítás, amely az egész AppDomain tartományt érinti. Ha például egy 4 magos géppel rendelkezik, és a *minWorkerThreads* és *a minIoThreads* értéke 50 processzoronként futásközben, akkor **a ThreadPool.SetMinThreads(200, 200)**.

* A minimális szálak beállítását a [ *minIoThreads* vagy *minWorkerThreads konfigurációs* beállítással](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) is `Machine.config`megadhatja `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`a konfigurációs elem alatt a `<processModel>` alkalmazásban, amely általában a helyen található. **A minimális szálak számának ily módon való beállítása általában nem ajánlott, mivel ez egy rendszerszintű beállítás.**

  > [!NOTE]
  > Az ebben a konfigurációs elemben megadott érték *magonkénti* beállítás. Ha például egy 4 magos géppel rendelkezik, és azt szeretné, hogy a *minIoThreads-beállítás* futásidőben 200 legyen, akkor a . `<processModel minIoThreads="50"/>`
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>A kiszolgáló globális katalógusának engedélyezése, hogy nagyobb átviteli érték keljen az ügyfélen a StackExchange.Redis használata esetén
A kiszolgáló globális katalógusának engedélyezése optimalizálhatja az ügyfelet, és jobb teljesítményt és átviteli teljesítményt biztosít a StackExchange.Redis használatakor. A globális iskolai kapcsolatról és engedélyezéséről további információt az alábbi cikkekben talál:

* [A kiszolgáló globális katalógusának engedélyezése](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Alapjai Garbage Collection](/dotnet/standard/garbage-collection/fundamentals)
* [Szemétgyűjtés és -teljesítmény](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>A kapcsolatok teljesítményével kapcsolatos szempontok

Minden tarifacsomag különböző korlátozásokat biztosít az ügyfélkapcsolatokra, a memóriára és a sávszélességre vonatkozóan. Bár a gyorsítótár minden egyes mérete *lehetővé teszi, hogy egy* bizonyos számú kapcsolat, minden kapcsolat Redis van többletterhelést társítva. Ilyen többletterhelés például a CPU és a memória használata a TLS/SSL titkosítás eredményeként. Egy adott gyorsítótár méretének maximális kapcsolati korlátja enyhén betöltött gyorsítótárat feltételez. Ha az ügyfélműveletekből származó terhelés *és* az ügyfélműveletekből származó terhelés meghaladja a rendszer kapacitását, a gyorsítótár kapacitásproblémákat tapasztalhat, még akkor is, ha nem lépte túl az aktuális gyorsítótár méretére vonatkozó kapcsolati korlátot.

Az egyes szintekre vonatkozó különböző kapcsolatkorlátokról az [Azure Cache for Redis díjszabáscímű](https://azure.microsoft.com/pricing/details/cache/)témakörben talál további információt. A kapcsolatokról és más alapértelmezett konfigurációkról az [Alapértelmezett Redis-kiszolgáló konfigurációja](cache-configure.md#default-redis-server-configuration)című témakörben olvashat bővebben.

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hogyan figyelhetem a gyorsítótár állapotát és teljesítményét?
A Microsoft Azure Cache for Redis-példányok figyelhetők meg az [Azure Portalon.](https://portal.azure.com) Megtekintheti a mérőszámokat, mérőszámokat rögzíthet a Starttáblára, testreszabhatja a figyelési diagramok dátum- és időtartományát, metrikákat adhat hozzá és távolíthat el a diagramokból, és riasztásokat állíthat be bizonyos feltételek teljesülése esetén. További információ: [Monitor Azure Cache for Redis](cache-how-to-monitor.md).

Az Azure Cache for Redis **Resource menü** is számos eszközt tartalmaz a gyorsítótárak figyelésére és hibaelhárítására.

* **A problémák diagnosztizálása és megoldása** tájékoztatást nyújt a gyakori problémákról és a megoldásukra vonatkozó stratégiákról.
* **Az erőforrás állapota** figyeli az erőforrást, és jelzi, hogy a várt módon fut-e. Az Azure Resource állapotszolgáltatásáról az [Azure Resource állapotának áttekintése](../resource-health/resource-health-overview.md)című témakörben olvashat bővebben.
* **Az új támogatási kérelem** lehetőséget biztosít a gyorsítótár támogatási kérelmének megnyitására.

Ezek az eszközök lehetővé teszik az Azure-gyorsítótár redis-példányok állapotának figyelését, és segítenek a gyorsítótárazási alkalmazások kezelésében. További információt az [Azure Cache for Redis](cache-configure.md)"Támogatás & hibaelhárítási beállítások" című részében talál.

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Miért látok időkiszabadságot?
Időmeghosszabbítások történnek az ügyfélnél, amivel beszél redis-hez. Amikor egy parancsot küld a Redis-kiszolgálónak, a parancs várólistára kerül, és a Redis-kiszolgáló végül felveszi a parancsot, és végrehajtja azt. Az ügyfél azonban időtúlhatja a folyamatot, és ha kivételt tesz, az a hívó oldalon jelenik meg. Az időtúlváltási problémák elhárításáról az [ügyféloldali hibaelhárítás](cache-troubleshoot-client.md) és [a StackExchange.Redis időtúloldal-kivételeket](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)című témakörben talál.

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Miért lett leválasztva az ügyfelem a gyorsítótárról?
Az alábbiakban a gyorsítótár-kapcsolat bontásának gyakori okai találhatók.

* Ügyféloldali okok
  * Az ügyfélalkalmazás újralett telepítve.
  * Az ügyfélalkalmazás méretezési műveletet hajtott végre.
    * A Felhőszolgáltatások vagy a Web Apps esetében ez az automatikus skálázás nak köszönhető.
  * Az ügyféloldalon megváltozott a hálózati réteg.
  * Átmeneti hibák történtek az ügyfélben vagy az ügyfél és a kiszolgáló közötti hálózati csomópontokban.
  * A sávszélesség-küszöbértékek elérték.
  * A processzorhoz kötött műveletek túl sokáig tartott.
* Kiszolgálóoldali okok
  * A standard gyorsítótár-ajánlat, az Azure Cache for Redis szolgáltatás kezdeményezte a feladatátvételt az elsődleges csomópontról a másodlagos csomópontra.
  * Az Azure javította azt a példányt, ahol a gyorsítótár telepítve volt
    * Ez lehet a Redis-kiszolgáló frissítései vagy az általános virtuális gép karbantartása.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Melyik Azure Cache-ajánlat a megfelelő számomra?
> [!IMPORTANT]
> A tavalyi [bejelentésnek](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)az Azure felügyelt gyorsítótár-szolgáltatás és az Azure szerepkörön belül i **gyorsítótárszolgáltatása** 2016. Javasoljuk, hogy az [Azure Cache for Redis.](https://azure.microsoft.com/services/cache/) Az áttelepítésről további információt az [Áttelepítés a felügyelt gyorsítótárszolgáltatásról az Azure Cache for Redis szolgáltatásba című témakörben talál.](cache-migrate-to-redis.md)
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
A Redis Azure Cache for Redis általában 120 GB-os méretben érhető el, és 99,9%-os rendelkezésre állási SLA-val rendelkezik. Az új [prémium szint](cache-premium-tier-intro.md) akár 1,2 TB-os méreteket és a fürtözés, a vnet és az állandó ság támogatását is biztosítja, 99,9%-os SLA-val.

A Redis Azure Cache for Redis lehetővé teszi az ügyfelek számára, hogy a Microsoft által felügyelt biztonságos, dedikált Azure-gyorsítótárat használjanak a Redis számára. Ezzel az ajánlattal kihasználhatja a Redis által biztosított gazdag funkciókészletet és ökoszisztémát, valamint a Microsoft megbízható tárhely- és figyelési szolgáltatását.

A hagyományos gyorsítótárakkal ellentétben, amelyek csak a kulcs-érték párokkal foglalkoznak, a Redis népszerű a rendkívül nagy teljesítményt tanoncadattípusai miatt. A Redis támogatja az atomi műveletek futtatását is ezeken a típusokon, például egy karakterlánchoz való hozzáfűzést; az érték növelése kivonatban; egy listára való rányomás; számítástechnikai készlet metszéspontja, egyesítése és különbsége; vagy szerzés a tag a legmagasabb rangú egy rendezett meg. Egyéb funkciók közé tartozik a tranzakciók támogatása, pub /sub, Lua scripting, kulcsok korlátozott ideig-hátra van, és a konfigurációs beállításokat, hogy Redis viselkedik, mint egy hagyományos cache.

A Redis sikerének másik kulcsfontosságú eleme az egészséges, élénk, nyílt forráskódú ökoszisztéma, amely arra épül. Ez tükröződik a redis-ügyfelek több nyelven elérhető sokféle készletében. Ez az ökoszisztéma és az ügyfelek széles köre lehetővé teszi, hogy a Redis Azure-gyorsítótárat szinte bármilyen számítási feladat használja, amelyet az Azure-on belül építene fel.

Az Azure Cache for Redis használatának megkezdéséről az [Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) és az Azure Cache használata [a Redis dokumentációjában](index.yml)című témakörben olvashat bővebben.

### <a name="managed-cache-service"></a>Felügyelt gyorsítótár szolgáltatás
[2016. november 30-án a felügyelt gyorsítótár szolgáltatás kilett vonva.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Az archivált dokumentáció megtekintéséhez olvassa el [az Archivált felügyelt gyorsítótárszolgáltatás dokumentációját.](/previous-versions/azure/azure-services/dn386094(v=azure.100))

### <a name="in-role-cache"></a>Szerepkörön át helyezett gyorsítótár
[2016. november 30-án megszüntették a szerepkörön át való gyorsítótárat.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Az archivált dokumentáció megtekintéséhez olvassa el [az Archivált szerepkörön ként isztikáns gyorsítótár dokumentációját.](/previous-versions/azure/azure-services/dn386103(v=azure.100))

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
