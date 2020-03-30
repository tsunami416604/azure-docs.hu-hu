---
title: Mi az az Azure Cache for Redis?
description: Ismerje meg az Azure Cache for Redis gyorsítótár-félre, tartalom-gyorsítótárazás, felhasználói munkamenet-gyorsítótárazás, feladat- és üzenetsor-kezelés, valamint elosztott tranzakciók engedélyezéséhez.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126370"
---
# <a name="azure-cache-for-redis-description"></a>Az Azure Cache for Redis ismertetése

Az Azure Cache for Redis a [redis](https://redis.io/)nyílt forráskódú szoftveren alapuló memóriabeli adattárolót biztosít. Gyorsítótárként használva a Redis javítja a háttéradat-tárolókat nagymértékben támaszkodó rendszerek teljesítményét és méretezhetőségét. A teljesítmény az alkalmazás közelében található gyors tárolóba történő másolással javul. Az Azure Cache for Redis, ez a gyors tárolás a memóriában található, ahelyett, hogy egy adatbázis betöltődne a lemezről.

A Redis Azure Cache szolgáltatása memóriabeli adatstruktúra-tárolóként, elosztott nem relációs adatbázisként és üzenetközvetítőként is használható. A Redis-motor kis késésének és nagy átviteli sebességének köszönhetően javul az alkalmazás teljesítménye.

Az Azure Cache for Redis hozzáférést biztosít egy biztonságos, dedikált Redis-gyorsítótárhoz. A Redis Azure Cache for Redis-t a Microsoft kezeli, az Azure-on belül üzemelteti, és az Azure-on belül vagy kívül bármely alkalmazás számára elérhető.

## <a name="using-azure-cache-for-redis"></a>Az Azure Cache for Redis használata

A Redis Azure Cache for Redis javítja az alkalmazások teljesítményét a közös alkalmazásarchitektúra-minták támogatásával. A leggyakoribbak többek között a következők:

| Mintázat      | Leírás                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Az adatbázisok gyakran túl nagyok ahhoz, hogy közvetlenül a gyorsítótárba töltődjenek be. Gyakori, hogy a [gyorsítótár-félre](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) mintát adatokat csak szükség szerint tölt be a gyorsítótárba. Amikor a rendszer módosítja az adatokat, a rendszer frissítheti a gyorsítótárat is, amelyet aztán más ügyfelek nek oszt a rendszer. Emellett a rendszer beállíthatja az adatok lejárati, vagy egy kilakoltatási házirend segítségével az adatfrissítések a gyorsítótárba.|
| [Tartalmak gyorsítótárazása](cache-aspnet-output-cache-provider.md) | Számos weboldal statikus tartalmat, például élőfejet, élőlábat, szalagcímeket használó sablonból jön létre. Ezek a statikus elemek nem változhatnak gyakran. A memórián belüli gyorsítótár használata gyors hozzáférést biztosít a statikus tartalomhoz a háttéradat-tárolókhoz képest. Ez a minta csökkenti a feldolgozási időt és a kiszolgáló terhelését, így a webkiszolgálók jobban reagálnak. Ez lehetővé teszi, hogy csökkentse a kiszolgálók kezeléséhez szükséges terhelések. A Redis Azure Cache for Redis biztosítja a Redis kimeneti gyorsítótár-szolgáltató, hogy támogassa ezt a mintát ASP.NET.|
| [Felhasználói munkamenetek gyorsítótárazása](cache-aspnet-session-state-provider.md) | Ezt a mintát gyakran használják a bevásárlókosarak és más felhasználói előzmények adatait, hogy egy webes alkalmazás esetleg szeretné társítani a felhasználói cookie-kat. Ha túl sok információt tárol cookie-kban, csökkenhet a teljesítmény, mivel a cookie mérete nő, és a rendszer minden kérés esetén átadja és ellenőrzi a cookie-t. Egy tipikus megoldás a cookie-t használja kulcsként az adatbázis ban lévő adatok lekérdezéséhez. A memórián belüli gyorsítótár, például az Azure Cache for Redis használatával az adatok felhasználóval való társítása sokkal gyorsabb, mint a teljes relációs adatbázissal való interakció. |
| Feladatok és üzenetek üzenetsor-kezelése | Az alkalmazások gyakran adnak hozzá feladatokat a várólistához, ha a kérelemhez társított műveletek végrehajtása időt vesz igénybe. A hosszabb ideig futó műveletek várólistára kerülnek, hogy egymás után dolgozzák fel őket, gyakran egy másik kiszolgálóáltal.  Ezt a késleltetési módot a tevékenységek üzenetsorba való helyezésének nevezik. A Redis Azure Cache egy elosztott várólistát biztosít, amely engedélyezi ezt a mintát az alkalmazásban.|
| Elosztott tranzakciók | Az alkalmazások néha egy háttéradat-tároló elleni parancsok sorozatát igénylik egyetlen atomi műveletként való végrehajtásához. Minden parancsnak sikeresnek kell lennie, vagy mindegyiket vissza kell állítani a kezdeti állapotba. Az Azure Cache for Redis támogatja a parancsok kötegének egyetlen tranzakcióként történő [megvalósítását.](https://redis.io/topics/transactions) |

## <a name="azure-cache-for-redis-offerings"></a>Azure-gyorsítótár a Redis-ajánlatokhoz

Az Azure Cache for Redis a következő szinteken érhető el:

| Szint | Leírás |
|---|---|
Basic | Egy csomópontos gyorsítótár. Ez a szint több memóriaméretet (250 MB – 53 GB) támogat, és ideális fejlesztési/tesztelési és nem kritikus fontosságú számítási feladatokhoz. Az alapszintű csomag nem rendelkezik szolgáltatásszint-szerződéssel (SLA) |
| Standard | Replikált gyorsítótár egy kétcsomópontos, elsődleges/másodlagos konfigurációban, amelyet az Azure kezel magas rendelkezésre állású SLA-val (99,9%) |
| Prémium | A prémium szint a nagyvállalati használatra kész szint. A prémium szintű gyorsítótárak több funkciót támogatnak, és nagyobb átviteli sebességgel, valamint gyorsabb válaszidőkkel rendelkeznek. A prémium szintű gyorsítótárakat nagyobb teljesítményű hardvereken helyezik üzembe, és az alapszintű vagy a standard szintnél jobb teljesítményt biztosítanak. Ez az előny azt jelenti, hogy az azonos méretű gyorsítótár átviteli igénye magasabb lesz a prémium szintű standard szinthez képest. |

> [!TIP]
> A méretről, az átviteli fokról és a prémium szintű gyorsítótárakkal kapcsolatos sávszélességről az [Azure Cache for Redis – gyakori kérdések című témakörben](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)talál további információt.
>

A gyorsítótár létrehozása után magasabb szintre skálázható. Az alacsonyabb szintre való vertikális leskálázás nem támogatott. A skálázásrészletes használatával kapcsolatos útmutatást az [Azure-gyorsítótár méretezése a Redis-hez](cache-how-to-scale.md) című témakörben és [a skálázási műveletek automatizálása című témakörben talál.](cache-how-to-scale.md#how-to-automate-a-scaling-operation)

### <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Az [Azure Cache for Redis díjszabási](https://azure.microsoft.com/pricing/details/cache/) lap részletes összehasonlítást biztosít az egyes rétegek. Az alábbi táblázat a szintek által támogatott néhány funkciót írja le:

| Funkció leírása | Prémium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-fürt](cache-how-to-premium-clustering.md) |✔|-|-|
| [Biztonság a tűzfalszabályok használatával](cache-configure.md#firewall) |✔|✔|✔|
| Titkosítás az átvitel során |✔|✔|✔|
| [Továbbfejlesztett biztonság és elkülönítés VNettel](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importálás/exportálás](cache-how-to-import-export-data.md) |✔|-|-|
| [Ütemezett frissítések](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Georeplikáció](cache-how-to-geo-replication.md) |✔|-|-|
| [Újraindítás](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>További lépések

* [ASP.NET webalkalmazás rövid útmutatója](cache-web-app-howto.md) Hozzon létre egy egyszerű ASP.NET webalkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.
* [.NET rövid útmutató](cache-dotnet-how-to-use-azure-redis-cache.md) Hozzon létre egy .NET alkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.
* [.NET Core rövid útmutató](cache-dotnet-core-quickstart.md) Hozzon létre egy .NET Core alkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.
* [Node.js – rövid útmutató](cache-nodejs-get-started.md) Hozzon létre egy egyszerű Node.js alkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.
* [Java – rövid útmutató](cache-java-get-started.md) Hozzon létre egy egyszerű Java-alkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.
* [Python–rövid útmutató](cache-python-get-started.md) Hozzon létre egy Python-alkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.
