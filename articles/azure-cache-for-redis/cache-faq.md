---
title: Azure Cache for Redis – Gyakori kérdések
description: Ismerje meg az Azure cache Redis kapcsolatos gyakori kérdésekre, mintákra és ajánlott eljárásokra adott válaszokat
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 6ba292850c057284fff265c8a77386d21374942a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010222"
---
# <a name="azure-cache-for-redis-faq"></a>Azure Cache for Redis – Gyakori kérdések
Ismerje meg az Azure cache-hez készült Redis kapcsolatos gyakori kérdésekre, mintákra és ajánlott eljárásokra adott válaszokat.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi a teendő, ha nem válaszolt a kérdésre?
Ha a kérdés nem szerepel a listán, tudassa velünk, és segítünk megtalálni a választ.

* A GYIK végén felteheti a kérdéseit, és az Azure cache csapatával és a jelen cikk más tagjaival is foglalkozhat.
* Ha szélesebb közönséget szeretne elérni, felteheti a kérdést az [Azure cache MSDN fórumán](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) , és az Azure cache csapatával és a Közösség más tagjaival is folytathatja.
* Ha a szolgáltatásra vonatkozó kérést szeretne végezni, küldje el kérelmeit és ötleteit az Azure cache-be a [Redis-felhasználói hanghoz](https://feedback.azure.com/forums/169382-cache).
* Küldhet e-mailt az [Azure cache külső visszajelzései](mailto:azurecache@microsoft.com)között is.

## <a name="azure-cache-for-redis-basics"></a>Azure cache a Redis alapjaihoz
Az ebben a szakaszban található gyakori kérdések az Azure cache néhány alapszintű Redis vonatkoznak.

* [Mi az az Azure Cache for Redis?](#what-is-azure-cache-for-redis)
* [Hogyan szerezhetem be az Azure cache-t a Redis-hez?](#how-can-i-get-started-with-azure-cache-for-redis)

A következő gyakori kérdések a Redis készült Azure cache alapfogalmait és feladatait ismertetik, és az egyik másik gyakori kérdésben is megválaszolják őket.

* [Milyen Azure cache-t használ a Redis-ajánlat és-méret használatához?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Milyen Azure cache-t használhatok a Redis-ügyfelek számára?](#what-azure-cache-for-redis-clients-can-i-use)
* [Létezik helyi emulátor az Azure cache Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hogyan figyeli a gyorsítótár állapotát és teljesítményét?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Tervezési GYIK
* [Milyen Azure cache-t használ a Redis-ajánlat és-méret használatához?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure cache a Redis teljesítményéhez](#azure-cache-for-redis-performance)
* [Milyen régióban érdemes megkeresni a gyorsítótárat?](#in-what-region-should-i-locate-my-cache)
* [Mennyit kell fizetnem az Azure cache Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Használhatom az Azure cache-t a Redis Azure Government Cloud, az Azure China Cloud vagy a Microsoft Azure Germany használatával?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Fejlesztői GYIK
* [Mi a StackExchange. Redis konfigurációs beállításai?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Milyen Azure cache-t használhatok a Redis-ügyfelek számára?](#what-azure-cache-for-redis-clients-can-i-use)
* [Létezik helyi emulátor az Azure cache Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hogyan Futtathatok Redis-parancsokat?](#how-can-i-run-redis-commands)
* [Miért nem rendelkezik az Azure cache for Redis egy MSDN Class Library-hivatkozással, mint a többi Azure-szolgáltatással?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Használhatom az Azure cache-t a Redis PHP-munkamenet-gyorsítótárként?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Mik azok a Redis-adatbázisok?](#what-are-redis-databases)

## <a name="security-faqs"></a>Biztonsági GYIK
* [Mikor engedélyezzem a nem TLS/SSL portot a Redis-hez való csatlakozáshoz?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Éles GYIK
* [Mik a bevált eljárások az éles környezetben?](#what-are-some-production-best-practices)
* [Milyen szempontokat kell figyelembe venni a gyakori Redis parancsok használatakor?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Fontos információk a szálkészlet munkaszála belépett növekedéséről](#important-details-about-threadpool-growth)
* [A StackExchange. Redis használata esetén engedélyezze a kiszolgáló GC-t, hogy nagyobb átviteli sebességet kapjon az ügyfélen.](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Teljesítménnyel kapcsolatos szempontok a kapcsolatokhoz](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Figyelés és hibaelhárítás – GYIK
Az ebben a szakaszban található gyakori kérdések az általános monitorozási és hibaelhárítási kérdésekkel foglalkoznak. Az Azure cache Redis-példányok figyelésével és hibaelhárításával kapcsolatos további információkért lásd: az [Azure cache figyelése a Redis](cache-how-to-monitor.md) és a különböző hibaelhárítási útmutatókhoz.

* [Hogyan figyeli a gyorsítótár állapotát és teljesítményét?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Miért látok időtúllépéseket?](#why-am-i-seeing-timeouts)
* [Miért bontottam le az ügyfelem a gyorsítótárat?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Korábbi gyorsítótár-ajánlatok – gyakori kérdések
* [Melyik Azure cache-ajánlat megfelelő a számomra?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Mi az az Azure Cache for Redis?
A Redis készült Azure cache a népszerű, nyílt forráskódú szoftverek [Redis](https://redis.io/)alapul. Hozzáférést biztosít egy biztonságos, dedikált Azure cache-hez a Microsoft által felügyelt Redis, és az Azure-on belül bármely alkalmazásból elérhető. Részletesebb áttekintést a Azure.com-beli Redis-termékek [Azure cache](https://azure.microsoft.com/services/cache/) -ről szóló oldalán talál.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hogyan szerezhetem be az Azure cache-t a Redis-hez?
Az Azure cache Redis többféleképpen is elsajátíthatja.

* A [.net](cache-dotnet-how-to-use-azure-redis-cache.md), a [ASP.net](cache-web-app-howto.md), a [Java](cache-java-get-started.md), a [Node. js](cache-nodejs-get-started.md)és a [Python](cache-python-get-started.md)szolgáltatáshoz elérhető oktatóanyagok közül választhat.
* Megtekintheti [, hogyan hozhat létre nagy teljesítményű alkalmazásokat a Redis Microsoft Azure cache használatával](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Megtekintheti az ügyfél dokumentációját a projekt fejlesztői nyelvének megfelelő ügyfelek számára, hogy megtudja, hogyan használhatja a Redis. Számos Redis-ügyfél használható az Azure cache használatával a Redis. A Redis-ügyfelek listáját a következő témakörben tekintheti meg: [https://redis.io/clients](https://redis.io/clients).

Ha még nem rendelkezik Azure-fiókkal, a következőket teheti:

* [Nyisson egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a keretét el is használta, továbbra is megtarthatja a fiókot, és használhatja az ingyenes szolgáltatásokat és lehetőségeket.
* [Aktiválja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Milyen Azure cache-t használ a Redis-ajánlat és-méret használatához?
A Redis minden egyes Azure-gyorsítótára különböző szintű **méreteket**, **sávszélességet**, **magas rendelkezésre állást**és **SLA** -lehetőségeket kínál.

A gyorsítótár-ajánlat kiválasztásának szempontjai a következők:

* **Memória**: az alapszintű és a Standard csomag 250 MB – 53 GB. A prémium szint akár 1,2 TB-ot (fürtöt) vagy 120 GB-ot (nem fürtözött) is kínál. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál.
* Hálózati teljesítmény: Ha olyan számítási **feladattal**rendelkezik, amely nagy adatátvitelt igényel, a prémium szint nagyobb sávszélességet biztosít a standard és az alapszinthez képest. Az egyes szinteknél a nagyobb méretű gyorsítótárak is nagyobb sávszélességet igényelnek a gyorsítótárat működtető mögöttes virtuális gép miatt. További információkért tekintse meg a [következő táblázatot](#cache-performance).
* **Átviteli sebesség**: a prémium szint a maximálisan elérhető átviteli sebességet kínálja. Ha a gyorsítótár-kiszolgáló vagy-ügyfél eléri a sávszélesség-korlátot, időtúllépést kaphat az ügyfél oldalán. További információt az alábbi táblázat tartalmaz.
* **Magas rendelkezésre állás/SLA**: az Azure cache for Redis garantálja, hogy az idő legalább 99,9%-ában elérhető a standard/Premium gyorsítótár. Az SLA-val kapcsolatos további tudnivalókért tekintse meg [Az Azure cache Redis díjszabását](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Az SLA csak a cache-végpontokhoz való kapcsolódást fedi le. Az SLA nem fedi le az adatvesztés elleni védelmet. Javasoljuk, hogy a prémium szint Redis adatmegőrzési funkciójának használatával növelje a rugalmasságot az adatvesztéssel szemben.
* **Redis-adatmegőrzés**: a prémium szint lehetővé teszi, hogy megmaradjon az Azure Storage-fiók gyorsítótár-adatvédelme. Alapszintű/standard gyorsítótárban az összes adat tárolása csak a memóriában történik. Az alapul szolgáló infrastrukturális problémák potenciális adatvesztést okozhatnak. Javasoljuk, hogy a prémium szint Redis adatmegőrzési funkciójának használatával növelje a rugalmasságot az adatvesztéssel szemben. Az Azure cache for Redis RDB és AOF (hamarosan elérhető) lehetőségeket kínál a Redis megőrzésében. További információ: az [adatmegőrzés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-persistence.md)-hez.
* **Redis-fürt**: a 120 GB-nál nagyobb gyorsítótárak létrehozásához, illetve az adatszegmensek több Redis-csomóponton keresztüli kibontásához használhatja a Redis-fürtszolgáltatást, amely a prémium szintű csomagban érhető el. Minden csomópont egy elsődleges/replika-gyorsítótárból áll a magas rendelkezésre állás érdekében. További információ: [fürtözés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-clustering.md)-hez.
* **Fokozott biztonság és hálózati elkülönítés**: az Azure Virtual Network (VNET) üzembe helyezése fokozott biztonságot és elkülönítést biztosít az Azure-gyorsítótár Redis, valamint az alhálózatok, a hozzáférés-vezérlési házirendek és más funkciók számára a hozzáférés további korlátozásához. További információkért lásd: [Virtual Network támogatásának konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-vnet.md)-hez.
* **Redis konfigurálása**: a standard és a prémium szinten is konfigurálhatja a Redis a lemezterület-értesítésekhez.
* **Ügyfélkapcsolatok maximális száma**: a prémium szint azon ügyfelek maximális számát kínálja, akik csatlakozhatnak a Redis, és nagyobb számú kapcsolattal rendelkeznek a nagyobb méretű gyorsítótárak esetében. A fürtözés nem fokozza a fürtözött gyorsítótár számára elérhető kapcsolatok számát. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál.
* **Dedikált mag a Redis-kiszolgálóhoz**: a prémium szinten az összes gyorsítótár mérete dedikált alapszintű a Redis számára. Az alap/standard szintekben a C1-es méretnek és a fentieknek dedikált magja van a Redis-kiszolgálóhoz.
* **A Redis egyszálas** , így a kettőnél több mag nem nyújt további előnyt, mint a két magot, de a nagyobb méretű virtuálisgép-méretek általában nagyobb sávszélességgel rendelkeznek, mint a kisebb méretek. Ha a gyorsítótár-kiszolgáló vagy-ügyfél eléri a sávszélességre vonatkozó korlátozásokat, időtúllépést kap az ügyfél oldalán.
* **Teljesítménybeli fejlesztések**: a prémium szintű gyorsítótárak üzembe helyezése gyorsabb processzorokkal rendelkező hardvereken történik, ami jobb teljesítményt nyújt az alap-vagy standard szinthez képest. A prémium szintű gyorsítótárak nagyobb átviteli sebességgel és kisebb késéssel rendelkeznek.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure cache a Redis teljesítményéhez
Az alábbi táblázat a standard és prémium gyorsítótárak különböző méretekben történő tesztelésekor megfigyelt maximális sávszélességet mutatja a IaaS virtuális gépről a Redis-végponthoz tartozó Azure cache használatával `redis-benchmark.exe` . TLS átviteli sebesség esetén a Redis-benchmark a stunnel használatával csatlakozik az Azure cache-hez a Redis-végponthoz.

>[!NOTE] 
>Ezek az értékek nem garantáltak, és ezekhez a számokhoz nem biztosítunk SLA-t, de jellemzőnek kell lennie. Az alkalmazás megfelelő gyorsítótár-méretének meghatározásához be kell töltenie a saját alkalmazásának tesztelését.
>Előfordulhat, hogy ezek a számok változhatnak, mivel az újabb eredményeket rendszeresen közzéteszjük.
>

Ebből a táblázatból a következő következtetéseket tudjuk felhívni:

* Az azonos méretű gyorsítótárak átviteli sebessége magasabb a prémium szinten a standard szinthez képest. Például egy 6 GB-os gyorsítótárral a P1 adatátviteli sebessége 180 000 kérelem/másodperc (RPS), a C3 100 000 RPS-hez képest.
* A Redis fürtszolgáltatással a teljesítmény lineárisan növekszik a fürtben lévő szegmensek számának növelésével. Ha például 10 szegmensből álló P4-fürtöt hoz létre, akkor az elérhető átviteli sebesség 400 000 * 10 = 4 000 000 RPS.
* A nagyobb méretű kulcsok átviteli sebessége magasabb a prémium szinten a standard szinthez képest.

| Tarifacsomag | Méret | Processzormagok | Rendelkezésre álló sávszélesség | 1 KB-os érték mérete | 1 KB-os érték mérete |
| --- | --- | --- | --- | --- | --- |
| **Standard gyorsítótár mérete** | | |**Megabit/mp (MB/s)/megabájt/mp (MB/s)** |**Másodpercenkénti kérelmek (RPS) nem SSL** |**Másodpercenkénti kérelmek (RPS) SSL** |
| C0 | 250 MB | Megosztott | 100/12,5  |  15 000 |   7500 |
| C1 |   1 GB | 1      | 500/62,5  |  38 000 |  20 720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41 000 |  37 000 |
| C3 csomag |   6 GB | 4      | 1000/125  | 100 000 |  90,000 |
| C4 |  13 GB | 2      | 500/62,5  |  60.000 |  55 000 |
| C5 csomag |  26 GB | 4      | 1 000/125 | 102 000 |  93 000 |
| C6 |  53 GB | 8      | 2 000/250 | 126 000 | 120 000 |
| **Prémium szintű gyorsítótár mérete** | |**CPU-magok/szegmensek** | **Megabit/mp (MB/s)/megabájt/mp (MB/s)** |**Másodpercenkénti kérelmek (RPS) nem SSL, szegmens szerint** |**Másodpercenkénti kérelmek másodpercenkénti száma (RPS)** |
| P1 |   6 GB |  2 | 1 500/187,5 | 180 000 | 172 000 |
| P2 |  13 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P3 |  26 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P4 |  53 GB |  8 | 6 000/750   | 400,000 | 373 000 |
| P5 | 120 GB | 20 | 6 000/750   | 400,000 | 373 000 |

A stunnel beállításával vagy a Redis `redis-benchmark.exe`-eszközök (például) letöltésével kapcsolatos utasításokért lásd a [Hogyan futtathatom a Redis-parancsokat?](#cache-commands) című szakaszt.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Milyen régióban érdemes megkeresni a gyorsítótárat?
A legjobb teljesítmény és a legalacsonyabb késés érdekében keresse meg az Azure-gyorsítótárat a Redis ugyanabban a régióban, ahol a gyorsítótár-ügyfélalkalmazás található.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Mennyit kell fizetnem az Azure cache Redis?
Az Azure cache for Redis díjszabása [itt](https://azure.microsoft.com/pricing/details/cache/)található. A díjszabás lapon a díjszabás óradíjként szerepel. A gyorsítótárak számlázása percenként történik a gyorsítótár létrehozásának időpontjától egészen a gyorsítótár törlésekor. Nincs lehetőség a gyorsítótár számlázásának leállítására vagy felfüggesztésére.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Használhatom az Azure cache-t a Redis Azure Government Cloud, az Azure China Cloud vagy a Microsoft Azure Germany használatával?
Igen, a Redis-hez készült Azure cache Azure Government felhőben, az Azure China 21Vianet-felhőben és Microsoft Azure Germany érhető el. A Redis Azure cache eléréséhez és kezeléséhez használt URL-címek eltérőek ezekben a felhőkben az Azure nyilvános felhővel szemben.

| Felhő   | Redis DNS-utótagja            |
|---------|---------------------------------|
| Nyilvános  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Németország | *. redis.cache.cloudapi.de       |
| Kína   | *. redis.cache.chinacloudapi.cn  |

Az Azure cache más Felhőkkel való Redis való használatának szempontjaival kapcsolatos további információkért tekintse meg a következő hivatkozásokat.

- [Azure Government-adatbázisok – Azure cache a Redis-hez](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet-felhő – Azure cache a Redis-hoz](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

További információ az Azure cache Redis való használatáról a PowerShell használatával Azure Government felhőben, az Azure China 21Vianet Cloud és a Microsoft Azure Germanyban: [Kapcsolódás más felhőhöz – Azure cache a Redis powershellhez](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Mi a StackExchange. Redis konfigurációs beállításai?
A StackExchange. Redis számos lehetőséget kínál. Ez a szakasz néhány gyakori beállításról beszél. További információ a StackExchange. Redis beállításokról: [StackExchange. Redis konfiguráció](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Leírás | Ajánlás |
| --- | --- | --- |
| AbortOnConnectFail |Ha igaz értékre van állítva, a kapcsolat hálózati hiba után nem fog újracsatlakozni. |Állítsa hamis értékre, és hagyja, hogy a StackExchange. Redis automatikusan újracsatlakozik. |
| ConnectRetry |A kapcsolódási kísérletek megismétlésének száma a kezdeti csatlakozás során. |Útmutatásért tekintse meg az alábbi megjegyzéseket. |
| ConnectTimeout |A csatlakozási műveletekhez tartozó MS-időtúllépés. |Útmutatásért tekintse meg az alábbi megjegyzéseket. |

Általában az ügyfél alapértelmezett értékei elegendőek. A számítási feladatok alapján részletesen beállíthatja a beállításokat.

* **Újrapróbálkozások**
  * A ConnectRetry és a ConnectTimeout esetében az általános útmutató fail fast, majd újra próbálkozik. Ez az útmutató a számítási feladatokon alapul, és mennyi időt vesz igénybe az ügyfél számára, hogy kiadjon egy Redis-parancsot, és választ kapjon.
  * Hagyja, hogy a StackExchange. Redis automatikusan újracsatlakozik a kapcsolat állapotának ellenőrzése és az újbóli csatlakoztatása helyett. **Kerülje a ConnectionMultiplexer. IsConnected tulajdonság használatát**.
  * Bevezetés – időnként előfordulhat, hogy az újrapróbálkozást és a hógolyó újrapróbálkozását, illetve a rendszer soha nem állítja vissza. Ha bekövetkezik a bevezetést, érdemes lehet egy exponenciális leállítási újrapróbálkozási algoritmust használni a Microsoft Patterns & Practices csoport által közzétett [általános útmutató](../best-practices-retry-general.md) című témakörben leírtak szerint.
  
* **Időtúllépési értékek**
  * Vegye figyelembe a számítási feladatokat, és ennek megfelelően állítsa be az értékeket. Ha nagyméretű értékeket tárol, állítsa az időtúllépést magasabb értékre.
  * Állítsa `AbortOnConnectFail` hamis értékre, és hagyja, hogy a StackExchange. Redis újracsatlakozik.
  * Egyetlen ConnectionMultiplexer-példányt használjon az alkalmazáshoz. A LazyConnection használatával létrehozhat egyetlen, a kapcsolati tulajdonság által visszaadott példányt, ahogy az a [ConnectionMultiplexer osztály használatával a gyorsítótárhoz való kapcsolódással](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)is látható.
  * Állítsa a `ConnectionMultiplexer.ClientName` tulajdonságot az alkalmazás-példány egyedi nevére diagnosztikai célokra.
  * Több `ConnectionMultiplexer` példány használata az egyéni munkaterhelésekhez.
      * Ezt a modellt követheti, ha eltérő terhelést használ az alkalmazásban. Például:
      * A nagyméretű kulcsok kezeléséhez egy multiplexer is tartozhat.
      * A kis kulcsok kezeléséhez egy multiplexer is tartozhat.
      * Megadhat különböző értékeket a kapcsolati időtúllépésekhez, és újrapróbálkozhat a logikával minden egyes használt ConnectionMultiplexer.
      * Állítsa be `ClientName` az egyes multiplexerek tulajdonságát a diagnosztika segítésére.
      * Ez az útmutató az egyszerűbb késést eredményezhet `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Milyen Azure cache-t használhatok a Redis-ügyfelek számára?
A Redis egyik nagyszerű dologa, hogy sok ügyfél támogatja számos különböző fejlesztési nyelvet. Az ügyfelek aktuális listájáért lásd: [Redis-ügyfelek](https://redis.io/clients). A különböző nyelveket és ügyfeleket bemutató oktatóanyagokat lásd: az [Azure cache használata a Redis](cache-dotnet-how-to-use-azure-redis-cache.md) és a testvér cikkeihez a tartalomjegyzékben.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Létezik helyi emulátor az Azure cache Redis?
Nincs helyi emulátor az Azure cache-hez a Redis-hez, de a Redis-Server. exe MSOpenTech verzióját futtathatja a helyi számítógép [Redis parancssori eszközeiről](https://github.com/MSOpenTech/redis/releases/) , és csatlakozhat ahhoz, hogy hasonló élményt kapjon a helyi gyorsítótár-emulátorhoz, ahogy az alábbi példában látható:

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


Ha kívánja, beállíthatja, hogy a [Redis. conf](https://redis.io/topics/config) fájl jobban illeszkedjen az online Azure cache [alapértelmezett gyorsítótár-beállításaihoz](cache-configure.md#default-redis-server-configuration) a Redis, ha szükséges.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hogyan Futtathatok Redis-parancsokat?
A [Redis parancsokban](https://redis.io/commands#) felsorolt parancsok bármelyikét felhasználhatja, kivéve a [Redis parancsokban felsorolt parancsokat, amelyek nem támogatottak az Azure cache for Redis esetében](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Több lehetőség is van a Redis parancsok futtatására.

* Ha standard vagy prémium szintű gyorsítótárral rendelkezik, Redis-parancsokat futtathat a Redis- [konzol](cache-configure.md#redis-console)használatával. A Redis-konzol biztonságos módot biztosít a Redis parancsok futtatására a Azure Portal.
* Használhatja a Redis parancssori eszközöket is. A használatához hajtsa végre a következő lépéseket:
* Töltse le a [Redis parancssori eszközöket](https://github.com/MSOpenTech/redis/releases/).
* Kapcsolódjon a gyorsítótárhoz `redis-cli.exe`a használatával. Továbbítsa a gyorsítótár-végpontot a-h kapcsolóval és a-a kulcs használatával a következő példában látható módon:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> A Redis parancssori eszközei nem működnek a TLS-porttal, de használhatnak olyan segédprogramot, `stunnel` amely az eszközök TLS-porthoz való biztonságos csatlakoztatásához szükséges utasításokat követve a [Redis parancssori eszköz használata az Azure cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) című cikkben található útmutatást.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Miért nem rendelkezik az Azure cache for Redis egy MSDN Class Library-hivatkozással, mint a többi Azure-szolgáltatással?
A Redis Microsoft Azure gyorsítótára a Redis népszerű, nyílt forráskódú Azure cache-re épül. Számos [Redis-ügyfél](https://redis.io/clients) számára elérhető számos programozási nyelvhez. Minden ügyfél saját API-val rendelkezik, amely hívásokat kezdeményez az Azure cache Redis-példányhoz a [Redis parancsok](https://redis.io/commands)használatával.

Mivel minden ügyfél eltérő, az MSDN-ben nem találhatók központi osztályok, és mindegyik ügyfél saját dokumentációt tart fenn. A dokumentáción kívül számos oktatóanyag mutatja be, hogyan kezdheti el az Azure cache használatát a Redis különböző nyelveken és gyorsítótár-ügyfeleken. Az oktatóanyagok eléréséhez tekintse meg az [Azure cache használata a Redis](cache-dotnet-how-to-use-azure-redis-cache.md) és a testvéri cikkek a tartalomjegyzékben című cikket.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Használhatom az Azure cache-t a Redis PHP-munkamenet-gyorsítótárként?
Igen, ha a Redis-hez készült Azure cache-t PHP-munkamenet-gyorsítótárként szeretné használni, adja meg a kapcsolati `session.save_path`karakterláncot a Redis-példányhoz tartozó Azure cache-hez.

> [!IMPORTANT]
> Ha az Azure cache-t PHP-Redis használja, akkor a gyorsítótárhoz való csatlakozáshoz használt biztonsági kulcsot URL-címként kell kódolnia, ahogy az az alábbi példában is látható:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Ha a kulcs nem kódolt URL-cím, a következőhöz hasonló üzenet jelenhet meg:`Failed to parse session.save_path`
>
>

További információ az Azure cache Redis való használatáról a PhpRedis-ügyféllel PHP-munkamenet-gyorsítótárként: [php-munkamenet kezelője](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Mik azok a Redis-adatbázisok?

A Redis-adatbázisok csak az azonos Redis-példányon belüli adatmennyiségek logikai elkülönítését jelentik. A gyorsítótár memóriája egy adott adatbázis összes adatbázisa és tényleges memória-felhasználása között van megosztva az adatbázisban tárolt kulcsoktól/értéktől. Egy C6-os gyorsítótár például 53 GB memóriával rendelkezik, a p5 pedig 120 GB. Dönthet úgy, hogy az összes 53 GB/120 GB-ot egy adatbázisba helyezi, vagy akár több adatbázis között is feloszthatja. 

> [!NOTE]
> Ha prémium szintű Azure cache-t használ a Redis-alapú fürtözéshez, csak az adatbázis 0 érhető el. Ez a korlátozás belső Redis korlátozás, és nem kifejezetten az Azure cache for Redis. További információkért lásd: [az ügyfélalkalmazás használatának módosítása a fürtözéshez?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Mikor engedélyezzem a nem TLS/SSL portot a Redis-hez való csatlakozáshoz?
A Redis-kiszolgáló nem támogatja natív módon a TLS-t, de az Azure cache for Redis nem. Ha az Azure cache-hez csatlakozik a Redis-hez, és az ügyfél támogatja a TLS-t, például a StackExchange. Redis, akkor a TLS protokollt kell használnia.

>[!NOTE]
>A nem TLS port alapértelmezés szerint le van tiltva az új Azure cache Redis-példányokhoz. Ha az ügyfél nem támogatja a TLS-t, akkor a nem TLS portot a [gyorsítótár konfigurálása az Azure cache-ben Redis](cache-configure.md) [című cikkben](cache-configure.md#access-ports) található utasításokat követve engedélyeznie kell a nem TLS-port használatát.
>
>

A Redis-eszközök `redis-cli` , például a TLS-port használata nem működnek, de használhatnak olyan segédprogramot `stunnel` is, amely az eszközök TLS-porthoz való biztonságos csatlakoztatását a [bejelentési ASP.NET munkamenet-szolgáltatója a Redis előzetes kiadásához](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) című blogbejegyzésben.

A Redis-eszközök letöltésével kapcsolatos utasításokért tekintse meg a [Hogyan futtathatom a Redis parancsokat?](#cache-commands) című szakaszt.

### <a name="what-are-some-production-best-practices"></a>Mik a bevált eljárások az éles környezetben?
* [StackExchange. Redis – ajánlott eljárások](#stackexchangeredis-best-practices)
* [Konfigurálás és fogalmak](#configuration-and-concepts)
* [Teljesítménytesztelés](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange. Redis – ajánlott eljárások
* Állítsa `AbortConnect` false (hamis) értékre, majd engedélyezze a ConnectionMultiplexer automatikus újrakapcsolódását. [Részletekért lásd itt](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* A ConnectionMultiplexer újrafelhasználása – ne hozzon létre újat minden kérelemhez. Az `Lazy<ConnectionMultiplexer>` [itt látható](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) minta használata javasolt.
* A Redis kisebb értékekkel működik a legjobban, ezért érdemes lehet nagyobb mennyiségű kulcsot feldarabolni több kulcsra. Ebben a [Redis-vitában](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)az 100 kb nagy méretű. Olvassa el [ezt a cikket](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) egy olyan problémával kapcsolatban, amelyet nagy értékek okozhatnak.
* Az időtúllépések elkerüléséhez konfigurálja a [szálkészlet munkaszála belépett beállításait](#important-details-about-threadpool-growth) .
* Használjon legalább 5 másodperces alapértelmezett connectTimeout. Ez az intervallum elegendő időt biztosít a StackExchange. Redis számára a kapcsolat újbóli létrehozásához a hálózati rendszerindítási hiba esetén.
* Vegye figyelembe a futtatott különböző műveletekhez kapcsolódó teljesítménnyel kapcsolatos költségeket. Például a `KEYS` parancs egy O (n) művelet, ezért el kell kerülni. A [Redis.IO hely](https://redis.io/commands/) részletesen ismerteti az általa támogatott műveletek időbeli összetettségét. Az egyes parancsokra kattintva megtekintheti az egyes műveletek bonyolultságát.

#### <a name="configuration-and-concepts"></a>Konfigurálás és fogalmak
* Használjon standard vagy prémium szintű üzemi rendszereket. Az Alapszint egyetlen csomópontot tartalmaz adatreplikáció nélkül, és SLA sem tartozik hozzá. Ezen kívül használjon legalább C1 szintű gyorsítótárat. A C0 cache-gyorsítótárak jellemzően egyszerű fejlesztési/tesztelési helyzetekben használatosak.
* Ne feledje, hogy a Redis egy **memóriában** tárolt adattároló. Olvassa el [ezt a cikket](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , hogy tisztában legyenek az adatvesztést okozó forgatókönyvekkel.
* Fejlessze a rendszerét úgy, hogy a [javítás és a feladatátvétel miatt](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)képes legyen kezelni a kapcsolódási naplókat.

#### <a name="performance-testing"></a>Teljesítménytesztelés
* Kezdje a használatával `redis-benchmark.exe` , hogy a saját Perf-tesztek megírása előtt a lehető legnagyobb átviteli sebességre legyen szükség. Mivel `redis-benchmark` a nem támogatja a TLS-t, a teszt futtatása előtt [engedélyeznie kell a nem TLS portot a Azure Portalon](cache-configure.md#access-ports) . Példák: [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* A teszteléshez használt ügyfél virtuális gépnek ugyanabban a régióban kell lennie, mint a Redis-példányhoz tartozó Azure cache-nek.
* Azt javasoljuk, hogy a Dv2 virtuálisgép-sorozatokat az ügyfélhez használja, mivel azok jobb hardvert használnak, és a legjobb eredményt kell biztosítani.
* Győződjön meg arról, hogy a kiválasztott ügyfél virtuális gépe legalább annyi számítási és sávszélességi képességgel rendelkezik, mint a tesztelt gyorsítótár.
* Engedélyezze a VRSS az ügyfélszámítógépen, ha Windows rendszeren van. [Részletekért lásd itt](https://technet.microsoft.com/library/dn383582.aspx).
* A prémium szintű Redis-példányok jobb hálózati késést és átviteli sebességet biztosítanak, mivel a processzor és a hálózat esetében is jobb hardveren futnak.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Milyen szempontokat kell figyelembe venni a gyakori Redis parancsok használatakor?

* Ne használjon olyan Redis-parancsokat, amelyek hosszú ideig tartanak a végrehajtás során, kivéve, ha teljes mértékben tisztában van a parancsok hatásával. Ne futtassa például a [Keys](https://redis.io/commands/keys) parancsot éles környezetben. A kulcsok számától függően hosszú időt is igénybe vehet. A Redis egy egyszálas kiszolgáló, és egyszerre dolgozza fel a parancsokat. Ha a kulcsok után más parancsokat is kiállítottak, a rendszer addig nem dolgozza fel őket, amíg a Redis feldolgozza a KEYS parancsot. A [Redis.IO hely](https://redis.io/commands/) részletesen ismerteti az általa támogatott műveletek időbeli összetettségét. Az egyes parancsokra kattintva megtekintheti az egyes műveletek bonyolultságát.
* Kulcsok mérete – érdemes kis kulcs/értékek vagy nagy kulcs/értékek használata? Ez a forgatókönyvtől függ. Ha a forgatókönyv nagyobb kulcsokat igényel, módosíthatja a ConnectionTimeout, majd újrapróbálkozhat az értékekkel, és módosíthatja az újrapróbálkozási logikát. A Redis-kiszolgáló szemszögéből a kisebb értékek jobb teljesítményt nyújtanak.
* Ezek a megfontolások nem azt jelentik, hogy nem tárolhat nagyobb értékeket a Redis; a következő szempontokat kell figyelembe vennie: A késések magasabbak lesznek. Ha olyan adatkészlettel rendelkezik, amely nagyobb és kisebb, akkor több ConnectionMultiplexer-példányt is használhat, amelyek mindegyike eltérő időkorláttal és újrapróbálkozási értékekkel van konfigurálva, ahogy az előző, a [StackExchange. Redis konfigurációs beállítások do](#cache-configuration) szakaszban leírtak szerint.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?
* [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát. Megtekintheti a metrikákat a Azure Portalban, és az Ön által választott eszközökkel [letöltheti és áttekintheti](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) őket.
* A Redis-benchmark. exe használatával betöltheti a Redis-kiszolgáló tesztelését.
* Győződjön meg arról, hogy a terhelés-tesztelési ügyfél és a Redis Azure-gyorsítótára ugyanabban a régióban található.
* Használja a Redis-CLI. exe fájlt, és figyelje a gyorsítótárat az INFO parancs használatával.
* Ha a terhelés nagy mennyiségű memóriát okoz, nagyobb gyorsítótár-méretre kell méreteznie.
* A Redis-eszközök letöltésével kapcsolatos utasításokért tekintse meg a [Hogyan futtathatom a Redis parancsokat?](#cache-commands) című szakaszt.

A következő parancsok példát mutatnak a Redis-benchmark. exe használatára. A pontos eredmények érdekében futtassa ezeket a parancsokat egy olyan virtuális gépről, amely ugyanabban a régióban található, mint a gyorsítótár.

* Folyamaton keresztüli beállított kérelmek tesztelése 1k hasznos adattartalommal

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* A folyamatok tesztelése egy 1k hasznos adattartalommal.
  Megjegyzés: a gyorsítótár feltöltéséhez először futtassa a fent látható SET tesztet.

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Fontos információk a szálkészlet munkaszála belépett növekedéséről
A CLR-beli szálkészlet munkaszála belépett két típusú szálat tartalmaz: "Worker" és "I/O-befejezési port" (olvasóhoz) szálak.

* A munkaszálakat olyan dolgokhoz használják, `Task.Run(…)`mint például `ThreadPool.QueueUserWorkItem(…)` a, vagy a metódusok feldolgozása. Ezeket a szálakat a CLR különböző összetevői is használják, ha a munkafolyamatnak egy háttérben futó szálon kell történnie.
* A olvasóhoz szálakat akkor használja a rendszer, amikor aszinkron IO történik, például a hálózatról való olvasáskor.

A szál készlete új munkavégző szálakat vagy I/O-befejezési szálakat biztosít igény szerint (szabályozás nélkül), amíg el nem éri a "minimális" beállítást az egyes típusú szálak esetében. Alapértelmezés szerint a szálak minimális száma a rendszeren lévő processzorok számára van beállítva.

Ha a meglévő (foglalt) szálak száma eléri a "minimális" számú szálat, a szálkészlet munkaszála belépett fogja szabályozni, hogy az új szálakat 500 ezredmásodpercen belül egy szálhoz adja. Ha a rendszer általában egy olvasóhoz-szálat igénylő munkafolyamatot kap, akkor az gyorsan feldolgozza a munkát. Ha azonban a munka felszakadása nagyobb, mint a beállított "minimális" beállítás, akkor némi késés fog történni a munka bizonyos részének feldolgozásakor, mivel a szálkészlet munkaszála belépett megvárja a két dolog egyikét.

1. Egy meglévő szál szabadon feldolgozhatja a munkát.
2. Egy meglévő szál 500 MS-ra nem lesz szabad, ezért létrejön egy új szál.

Alapvetően azt jelenti, hogy ha a foglalt szálak száma nagyobb, mint a minimális szál, valószínűleg 500 – MS késleltetést fizet, mielőtt az alkalmazás feldolgozza a hálózati forgalmat. Azt is fontos megjegyezni, hogy ha egy meglévő szál 15 másodpercnél hosszabb ideig marad üresjáratban (attól függően, hogy mit emlékszik), a rendszer törli, és a növekedés és a zsugorodás ezen ciklusa is megismételhető.

Ha egy példa a StackExchange. Redis (build 1.0.450 vagy újabb) hibaüzenetet jelenít meg, látni fogja, hogy most kinyomtatja a szálkészlet munkaszála belépett statisztikáit (lásd a olvasóhoz és a feldolgozó adatait alább).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Az előző példában látható, hogy a olvasóhoz szál esetében hat foglalt szál van, és a rendszer úgy van konfigurálva, hogy négy minimális szálat engedélyezzen. Ebben az esetben az ügyfél valószínűleg 2 500 – MS késéssel fog rendelkezni, mivel 6 > 4.

Vegye figyelembe, hogy a StackExchange. Redis az időtúllépéseket is elérheti, ha a olvasóhoz vagy a munkavégző szálak növekedését szabályozzák.

### <a name="recommendation"></a>Ajánlás

Ezeknek az információknak az alapján határozottan azt javasoljuk, hogy az ügyfelek az alapértelmezett értéknél nagyobb értékre állítsa be a olvasóhoz és a feldolgozói szálak minimális konfigurációs értékét. Az értékhez nem biztosítunk egy-egy méretre kiterjedő útmutatást, mivel az egyik alkalmazás megfelelő értéke valószínűleg túl magas vagy alacsony lesz egy másik alkalmazás esetében. Ez a beállítás hatással lehet a bonyolult alkalmazások más részeinek teljesítményére is, így minden ügyfélnek az adott igényeknek megfelelően kell finomítani ezt a beállítást. A megfelelő kiindulási hely 200 vagy 300, majd szükség szerint tesztelhető és csípés.

A beállítás konfigurálása:

* Azt javasoljuk, `global.asax.cs`hogy programozottan módosítsa ezt a beállítást a [szálkészlet munkaszála belépett. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) metódussal a alkalmazásban. Például:

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
  > A metódus által megadott érték globális beállítás, amely hatással van a teljes alkalmazástartomány. Ha például egy 4 magos géppel rendelkezik, és a *minWorkerThreads* és a *minIoThreads* processzort 50-ra szeretné beállítani, akkor a **szálkészlet munkaszála belépett. SetMinThreads (200, 200)** értéket kell használnia.

* A szálak minimális beállítása a [ *minIoThreads* vagy a *minWorkerThreads* konfigurációs beállítással](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) `<processModel>` is megadható a alkalmazásban `Machine.config`, általában a következő helyen:. `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` **A szálak minimális számának beállítása így általában nem ajánlott, mert ez egy rendszerszintű beállítás.**

  > [!NOTE]
  > A konfigurációs elemben megadott érték egy *alapszintű* beállítás. Ha például egy 4 magos géppel rendelkezik, és szeretné, hogy a *minIoThreads* -beállítás értéke 200, akkor a következőt fogja használni `<processModel minIoThreads="50"/>`:.
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>A StackExchange. Redis használata esetén engedélyezze a kiszolgáló GC-t, hogy nagyobb átviteli sebességet kapjon az ügyfélen.
A kiszolgáló GC szolgáltatásának engedélyezése optimalizálja az ügyfelet, és jobb teljesítményt és átviteli sebességet biztosít a StackExchange. Redis használatakor. A kiszolgáló GC-vel és annak engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

* [A kiszolgáló GC engedélyezése](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [A Garbage Collection alapjai](/dotnet/standard/garbage-collection/fundamentals)
* [A szemetet gyűjtése és teljesítménye](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Teljesítménnyel kapcsolatos szempontok a kapcsolatokhoz

Az egyes díjszabási szintek eltérő korlátokkal rendelkeznek az ügyfélkapcsolatok, a memória és a sávszélesség tekintetében. Míg a gyorsítótárak minden mérete *egy adott* számú kapcsolaton keresztül engedélyezett, a Redis-kapcsolatokhoz kapcsolódó minden egyes kapcsolathoz hozzá kell rendelni a terhelést. Ilyen például a CPU és a memóriahasználat a TLS/SSL titkosítás miatt. Egy adott gyorsítótár méretének maximális korlátja egy enyhén betöltött gyorsítótár. Ha a terhelés a kapcsolatok terhelése *és* az ügyfél műveleteinek terhelése meghaladja a rendszer kapacitását, akkor a gyorsítótár a kapacitással kapcsolatos problémákat is tapasztalhatja, még akkor is, ha nem lépte túl a gyorsítótár jelenlegi méretére vonatkozó korlátot.

Az egyes rétegek különböző kapcsolatainak korlátaival kapcsolatos további információkért lásd az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/). További információ a kapcsolatokról és az egyéb alapértelmezett konfigurációkról: az [alapértelmezett Redis-kiszolgáló konfigurációja](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hogyan figyeli a gyorsítótár állapotát és teljesítményét?
Az Redis-példányok Microsoft Azure gyorsítótára figyelhető a [Azure Portalban](https://portal.azure.com). Megtekintheti a metrikákat, rögzítheti a metrikák diagramokat a Kezdőpulton, testreszabhatja a figyelési diagramok dátumát és időtartományát, metrikákat adhat hozzá és távolíthat el a diagramokból, valamint riasztásokat állíthat be bizonyos feltételek teljesülése esetén. További információ: [Az Azure cache figyelése a Redis](cache-how-to-monitor.md).

Az Azure cache for Redis **erőforrás menü** számos eszközt is tartalmaz a gyorsítótárak figyeléséhez és hibaelhárításához.

* A **problémák diagnosztizálása és megoldása** a gyakori problémákról és stratégiákról nyújt információkat.
* A **Resource Health** figyeli az erőforrást, és jelzi, hogy a várt módon fut-e. Az Azure Resource Health szolgáltatással kapcsolatos további információkért lásd: az [Azure Resource Health áttekintése](../resource-health/resource-health-overview.md).
* Az **új támogatási kérelem** lehetőséget biztosít a gyorsítótárhoz tartozó támogatási kérelem megnyitására.

Ezek az eszközök lehetővé teszik az Azure cache Redis-példányok állapotának figyelését, valamint a gyorsítótárazási alkalmazások kezelését. További információ: az [Azure cache konfigurálása a Redis-hoz](cache-configure.md)– a "támogatási & hibaelhárítási beállítások" című szakasz.

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Miért látok időtúllépéseket?
Időtúllépések történnek abban az ügyfélben, amelyet a Redis való kommunikációhoz használ. Ha egy parancsot a Redis-kiszolgálónak továbbítanak, a parancs várólistára kerül, és a Redis-kiszolgáló végül felveszi a parancsot, és végrehajtja azt. Az ügyfél azonban időtúllépést okozhat a folyamat során, és ha kivétel történik a hívási oldalon. Az időtúllépési problémák elhárításával kapcsolatos további információkért lásd: [ügyféloldali hibaelhárítás](cache-troubleshoot-client.md) és [StackExchange. Redis időtúllépési kivételek](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Miért bontottam le az ügyfelem a gyorsítótárat?
A gyorsítótár leválasztásának néhány gyakori oka a következő:

* Ügyféloldali okok
  * Az ügyfélalkalmazás újra lett telepítve.
  * Az ügyfélalkalmazás skálázási műveletet hajtott végre.
    * Cloud Services vagy Web Apps esetén ennek oka az automatikus skálázás lehet.
  * A hálózati réteg módosult az ügyfél oldalán.
  * Átmeneti hibák fordultak elő az ügyfélen vagy a hálózati csomópontok között az ügyfél és a kiszolgáló között.
  * Elérte a sávszélességre vonatkozó küszöbértéket.
  * A CPU-kötésű műveletek végrehajtása túl sokáig tartott.
* Kiszolgálóoldali okok
  * A standard szintű gyorsítótár-ajánlat esetében az Azure cache for Redis szolgáltatás egy feladatátvételt kezdeményezett az elsődleges csomópontról a másodlagos csomópontra.
  * Az Azure a gyorsítótár üzembe helyezési példányának javítását használta
    * Ez lehet a Redis-kiszolgáló frissítései vagy az általános virtuális gép karbantartása.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Melyik Azure cache-ajánlat megfelelő a számomra?
> [!IMPORTANT]
> A tavalyi év [bejelentése](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)szerint az Azure Managed cache Service és az Azure szerepköralapú gyorsítótár szolgáltatás 2016. november 30-án **megszűnt** . Javasoljuk, hogy használja az [Azure cache-t a Redis](https://azure.microsoft.com/services/cache/). Az áttelepítéssel kapcsolatos információkért lásd: [áttelepítés Managed cache Serviceról az Azure cache-be a Redis-hez](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Az Azure cache for Redis általánosan elérhető a 120 GB-os méretekben, és az 99,9%-os rendelkezésre állási SLA-val rendelkezik. Az új [prémium szint](cache-premium-tier-intro.md) akár 1,2 TB-os méretet is kínál, és támogatja a fürtözést, a VNET és az adatmegőrzést, 99,9%-os SLA-val.

A Redis készült Azure cache lehetővé teszi, hogy az ügyfelek biztonságos, dedikált Azure cache-gyorsítótárat használjanak a Microsoft által felügyelt Redis. Ezzel az ajánlattal kihasználhatja a Redis által biztosított gazdag funkciókat és ökoszisztémát, valamint a Microsoft megbízható üzemeltetését és monitorozását.

A hagyományos gyorsítótárak eltérően, amelyek csak a kulcs-érték párokkal foglalkoznak, a Redis népszerű a nagy teljesítményű adattípusokhoz. A Redis ezen típusok esetében is támogatja a futó atomi műveleteket, például egy karakterlánchoz való hozzáfűzést. egy kivonat értékének növelése leküldés egy listára; számítástechnikai csoport – metszet, Unió és különbség; vagy a legmagasabb prioritású tag beszerzése egy rendezett készletben. A további funkciók közé tartozik a tranzakciók, a pub/sub, a LUA Scripting, a korlátozott élettartamú kulcsok, valamint a Redis a hagyományos gyorsítótárhoz hasonló konfigurációs beállítások.

A Redis egy másik kulcsfontosságú aspektusa az, hogy az egészséges, élénk, nyílt forráskódú ökoszisztéma épül fel. Ez a több nyelven elérhető Redis-ügyfelek változatos készletében jelenik meg. Ez az ökoszisztéma és az ügyfelek széles köre lehetővé teszi, hogy az Azure cache-t szinte bármilyen, az Azure-ban felépített munkaterhelés Redis.

A Redis készült Azure cache használatának első lépéseivel kapcsolatos további információkért lásd: az [Azure cache használata a Redis](cache-dotnet-how-to-use-azure-redis-cache.md) és [Az Azure cache-hez a Redis dokumentációjában](index.yml).

### <a name="managed-cache-service"></a>Managed cache szolgáltatás
[A Managed cache szolgáltatás 2016. november 30-án megszűnt.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Az archivált dokumentáció megtekintéséhez tekintse meg az [archivált Managed cache Service dokumentációját](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>szerepköralapú gyorsítótár
[A szerepköralapú gyorsítótár 2016. november 30-án megszűnt.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Az archivált dokumentáció megtekintéséhez tekintse meg az [archivált szerepköralapú gyorsítótár dokumentációját](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
