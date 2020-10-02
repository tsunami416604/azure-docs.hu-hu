---
title: Mi az az Azure Cache for Redis?
description: Ismerje meg az Azure cache-t, amely lehetővé teszi a gyorsítótárazást, a tartalom gyorsítótárazását, a felhasználói munkamenetek gyorsítótárazását, a feladatok és a Message Queuing Redis, valamint az elosztott tranzakciókat.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 26f6c8e3aceddc6f766bb43a1e384d761dee32bf
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631379"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
A Redis készült Azure cache memóriában tárolt adattárakat biztosít a nyílt forráskódú szoftverek [Redis](https://redis.io/)alapján. A Redis javítja a háttérbeli adattárakat használó alkalmazások teljesítményét és méretezhetőségét. Nagy mennyiségű alkalmazás-kérést képes feldolgozni a gyakran használt adatoknak a kiszolgáló memóriájában való megtartásával, amely gyorsan beírható és olvasható. A Redis kritikus, kis késésű és nagy átviteli sebességű adattárolási megoldást nyújt a modern alkalmazásokhoz.

Az Azure cache for Redis Redis felügyelt szolgáltatásként kínál. Biztonságos és dedikált Redis-kiszolgálói példányokat és teljes Redis API-kompatibilitást biztosít. A szolgáltatást a Microsoft üzemelteti, amely az Azure-ban üzemel, és az Azure-on belüli és kívüli alkalmazások számára is elérhető.

A Redis készült Azure cache elosztott vagy tartalom-gyorsítótárként, egy munkamenet-tárolóban, egy üzenetkezelőben és más szolgáltatásokban használható. Különállóként vagy más Azure Database szolgáltatással (például Azure SQL vagy Cosmos DB) együtt is üzembe helyezhető.

## <a name="key-scenarios"></a>Főbb forgatókönyvek
Az Azure cache for Redis az alkalmazások teljesítményének növelésével javítja az alkalmazás-architektúrák általános mintáit. A leggyakoribbak többek között a következők:

| Mintázat      | Leírás                                        |
| ------------ | -------------------------------------------------- |
| [Adatgyorsítótár](cache-web-app-cache-aside-leaderboard.md) | Az adatbázisok gyakran túl nagyok a gyorsítótárba való közvetlen betöltéshez. Gyakori a [gyorsítótár-](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) feltöltési minta használata, hogy csak szükség esetén töltse be az adatgyorsítótárat a gyorsítótárba. Ha a rendszer módosítja az adatmódosítást, a rendszer frissíti a gyorsítótárat is, amelyet aztán más ügyfelek számára terjesztenek. Emellett a rendszer beállíthat egy lejárati időpontot, vagy egy kizárási házirend használatával aktiválhatja az adatfrissítéseket a gyorsítótárba.|
| [Tartalom-gyorsítótár](cache-aspnet-output-cache-provider.md) | Számos weblap olyan sablonokból jön létre, amelyek statikus tartalmat, például fejléceket, lábléceket és szalagcímeket használnak. Ezek a statikus elemek gyakran nem változnak. A memórián belüli gyorsítótár használata gyors hozzáférést biztosít a háttérben lévő adattárolókkal összehasonlítva a statikus tartalmakhoz. Ez a minta csökkenti a feldolgozási időt és a kiszolgáló terhelését, így a webkiszolgálók rugalmasabbak lesznek. Lehetővé teszi a terhelések kezeléséhez szükséges kiszolgálók számának csökkentését. A Redis készült Azure cache biztosítja a Redis kimeneti gyorsítótár-szolgáltatóját, hogy támogassa ezt a mintát a ASP.NET.|
| [Munkamenet-tároló](cache-aspnet-session-state-provider.md) | Ezt a mintát gyakran használják a bevásárlókocsi-és más felhasználói előzményi adatként, amelyeket a webalkalmazások felhasználói cookie-kkal kívánnak rendelni. Ha túl sok információt tárol cookie-kban, csökkenhet a teljesítmény, mivel a cookie mérete nő, és a rendszer minden kérés esetén átadja és ellenőrzi a cookie-t. Egy tipikus megoldás a cookie-t használja kulcsként az adatbázisban tárolt adatlekérdezéshez. A memóriában tárolt gyorsítótár, például a Redis-hez készült Azure cache használatával az adatok egy felhasználóhoz való hozzárendelése sokkal gyorsabb, mint a teljes körű, kapcsolatban álló adatbázisokkal való interakció. |
| Feladatok és üzenetek üzenetsor-kezelése | Az alkalmazások gyakran vesznek fel feladatokat egy várólistába, ha a kérelemhez társított műveletek végrehajtása időt vesz igénybe. A már futó műveletek várólistára kerülnek, és gyakran egy másik kiszolgáló végzi a feldolgozást.  Ezt a késleltetési módot a tevékenységek üzenetsorba való helyezésének nevezik. A Redis készült Azure cache elosztott várólistát biztosít a minta alkalmazásban való engedélyezéséhez.|
| Elosztott tranzakciók | Az alkalmazásokhoz időnként szükség van egy adott háttérbeli adattárolóra vonatkozó parancsok sorozatára, hogy egyetlen atomi műveletként fussanak. Minden parancsnak sikeresnek kell lennie, vagy mindegyiket vissza kell állítani a kezdeti állapotba. A Redis-hez készült Azure cache egyetlen [tranzakcióként](https://redis.io/topics/transactions)támogatja a parancsok kötegének végrehajtását. |

## <a name="redis-versions"></a>Redis-verziók

A Redis-hez készült Azure cache a Redis 4. x-es verzióját és a 6,0-as előnézetét támogatja. Eldöntöttük, hogy kihagyjuk a Redis 5,0-et, hogy a legújabb verziót hozza létre. Korábban az Azure cache for Redis csak egyetlen Redis-verziót tart fenn. Egy újabb főverzió-frissítést és legalább egy régebbi stabil verziót fog biztosítani. Kiválaszthatja, hogy az alkalmazás [melyik verziója](cache-how-to-version.md) legyen a legmegfelelőbb.

> [!NOTE]
> A Redis 6,0 jelenleg előzetes verzióban érhető el – [vegye fel velünk a kapcsolatot](mailto:azurecache@microsoft.com) , ha érdekli. Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Szolgáltatásszintek
A Redis készült Azure cache a következő szinteknél érhető el:

| Szint | Leírás |
|---|---|
| Alapszintű | Egy csomópontos gyorsítótár. Ez a szint több memóriát támogat (250 MB-53 GB), és ideális fejlesztési, tesztelési és nem kritikus fontosságú számítási feladatokhoz. Az alapszintű csomaghoz nem tartozik szolgáltatói szerződés (SLA). |
| Standard | Egy replikált gyorsítótár egy két csomópontos, elsődleges/replika, az Azure által kezelt konfigurációban, magas rendelkezésre állású [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)-val. |
| Prémium | A prémium szint a nagyvállalati használatra kész szint. A prémium szintű gyorsítótárak több funkciót támogatnak, és nagyobb átviteli sebességgel, valamint gyorsabb válaszidőkkel rendelkeznek. A prémium szintű gyorsítótárakat nagyobb teljesítményű hardvereken helyezik üzembe, és az alapszintű vagy a standard szintnél jobb teljesítményt biztosítanak. Ez az előny azt jelenti, hogy az azonos méretű gyorsítótár esetében a standard szinthez képest magasabb lesz a prémium szintű kapacitás. |

### <a name="feature-comparison"></a>Szolgáltatások összehasonlítása
Az [Azure cache for Redis díjszabása](https://azure.microsoft.com/pricing/details/cache/) az egyes szintek részletes összehasonlítását tartalmazza. Az alábbi táblázat a szintek által támogatott néhány funkciót írja le:

| Funkció leírása | Prémium | Standard | Alapszintű |
| ------------------- | :-----: | :------: | :---: |
| [Szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-fürt](cache-how-to-premium-clustering.md) |✔|-|-|
| [Biztonság a tűzfalszabályok használatával](cache-configure.md#firewall) |✔|✔|✔|
| Titkosítás az átvitel során |✔|✔|✔|
| [Továbbfejlesztett biztonság és elkülönítés VNettel](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Ütemezett frissítések](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Georeplikáció](cache-how-to-geo-replication.md) |✔|-|-|
| [Újraindítás](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>A megfelelő szint kiválasztása
Az Redis-szintű Azure cache kiválasztásakor vegye figyelembe a következőket.

* **Memória**: az alapszintű és a Standard csomag 250 MB – 53 GB. A prémium szint akár 1,2 TB-ot (fürtöt) vagy 120 GB-ot (nem fürtözött) is kínál. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál.
* Hálózati teljesítmény: Ha olyan számítási **feladattal**rendelkezik, amely nagy adatátvitelt igényel, a prémium szint nagyobb sávszélességet biztosít a standard és az alapszinthez képest. Az egyes szinteknél a nagyobb méretű gyorsítótárak is nagyobb sávszélességet igényelnek a gyorsítótárat működtető mögöttes virtuális gép miatt. További információ: [Azure cache for Redis Performance](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Átviteli sebesség**: a prémium szint a maximálisan elérhető átviteli sebességet kínálja. Ha a gyorsítótár-kiszolgáló vagy-ügyfél eléri a sávszélesség-korlátot, időtúllépést kaphat az ügyfél oldalán. További információt az alábbi táblázat tartalmaz.
* **Magas rendelkezésre állás**: az Azure cache for Redis garantálja, hogy a standard/prémium gyorsítótár elérhető az [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)-nak megfelelően. Az SLA csak a cache-végpontokhoz való kapcsolódást fedi le. Az SLA nem fedi le az adatvesztés elleni védelmet. Javasoljuk, hogy a prémium szint Redis adatmegőrzési funkciójának használatával növelje a rugalmasságot az adatvesztéssel szemben.
* **Redis-adatmegőrzés**: a prémium szint lehetővé teszi, hogy megmaradjon az Azure Storage-fiók gyorsítótár-adatvédelme. Alapszintű/standard gyorsítótárban az összes adat tárolása csak a memóriában történik. Az alapul szolgáló infrastrukturális problémák potenciális adatvesztést okozhatnak. Javasoljuk, hogy a prémium szint Redis adatmegőrzési funkciójának használatával növelje a rugalmasságot az adatvesztéssel szemben. Az Azure cache for Redis RDB és AOF (előzetes verzió) lehetőséget kínál a Redis megőrzésében. További információ: az [adatmegőrzés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-persistence.md)-hez.
* **Redis-fürt**: a 120 GB-nál nagyobb gyorsítótárak létrehozásához, illetve az adatszegmensek több Redis-csomóponton keresztüli kibontásához használhatja a Redis-fürtszolgáltatást, amely a prémium szintű csomagban érhető el. Minden csomópont egy elsődleges/replika-gyorsítótárból áll a magas rendelkezésre állás érdekében. További információ: [fürtözés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-clustering.md)-hez.
* **Fokozott biztonság és hálózati elkülönítés**: az Azure Virtual Network (VNET) üzembe helyezése fokozott biztonságot és elkülönítést biztosít az Azure-gyorsítótár Redis, valamint az alhálózatok, a hozzáférés-vezérlési házirendek és más funkciók számára a hozzáférés további korlátozásához. További információkért lásd: [Virtual Network támogatásának konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-vnet.md)-hez.
* **Redis konfigurálása**: a standard és a prémium szinten is konfigurálhatja a Redis a lemezterület-értesítésekhez.
* **Ügyfélkapcsolatok maximális száma**: a prémium szint azon ügyfelek maximális számát kínálja, akik csatlakozhatnak a Redis, és nagyobb számú kapcsolattal rendelkeznek a nagyobb méretű gyorsítótárak esetében. A fürtözés nem fokozza a fürtözött gyorsítótár számára elérhető kapcsolatok számát. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál.
* **Dedikált mag a Redis-kiszolgálóhoz**: a prémium szinten az összes gyorsítótár mérete dedikált alapszintű a Redis számára. Az alap/standard szintekben a C1-es méretnek és a fentieknek dedikált magja van a Redis-kiszolgálóhoz.
* **Egyszálas feldolgozás**: a Redis a parancsok feldolgozásához csak egy szálat használ. Az Azure cache for Redis emellett további magok használatát is felhasználja az I/O-feldolgozáshoz. Ha egyre több mag van, akkor az átviteli teljesítmény még akkor is javul, ha nem lehet lineáris skálázást létrehozni. Emellett a nagyobb méretű virtuálisgép-méretek általában nagyobb sávszélesség-korláttal rendelkeznek, mint a kisebbek. Ez segít elkerülni a hálózat telítettségét, ami időtúllépést okoz az alkalmazásban.
* **Teljesítménybeli fejlesztések**: a prémium szintű gyorsítótárak üzembe helyezése gyorsabb processzorokkal rendelkező hardvereken történik, ami jobb teljesítményt nyújt az alap-vagy standard szinthez képest. A prémium szintű gyorsítótárak nagyobb átviteli sebességgel és kisebb késéssel rendelkeznek. További információ: [Azure cache for Redis Performance](cache-planning-faq.md#azure-cache-for-redis-performance)

A gyorsítótárat a létrehozása után akár magasabb szinten is méretezheti. Az alacsonyabb szintre való vertikális leskálázás nem támogatott. A részletes skálázási utasításokért lásd: az [Azure cache skálázása Redis](cache-how-to-scale.md) és [a skálázási műveletek automatizálása](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>További lépések
* [ASP.net-webalkalmazás](cache-web-app-howto.md) – rövid útmutató Hozzon létre egy egyszerű ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.
* [.Net](cache-dotnet-how-to-use-azure-redis-cache.md) gyors útmutató Hozzon létre egy .NET-alkalmazást, amely egy Azure cache-t használ a Redis.
* [.Net Core](cache-dotnet-core-quickstart.md) gyors útmutató Hozzon létre egy .NET Core-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Node.js](cache-nodejs-get-started.md) rövid útmutató Hozzon létre egy egyszerű Node.js alkalmazást, amely egy Azure cache-t használ a Redis.
* [Java](cache-java-get-started.md) rövid útmutató Hozzon létre egy egyszerű Java-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Python](cache-python-get-started.md) rövid útmutató Hozzon létre egy olyan Python-alkalmazást, amely egy Azure cache-t használ a Redis.
