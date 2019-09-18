---
title: Mi az Azure cache for Redis? | Microsoft Docs
description: Ismerje meg, hogy a Redis milyen Azure cache-t és milyen gyakran használják.
services: cache
documentationcenter: ''
author: yegu-ms
manager: martinekuan
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: overview
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 1f0c943bed473178dadb09cfb9d355821e5236e8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066843"
---
# <a name="azure-cache-for-redis-description"></a>Az Azure cache Redis leírása

A Redis készült Azure cache a népszerű szoftver- [Redis](https://redis.io/)alapul. Általában a háttér-adattárakra nagymértékben támaszkodó rendszerek teljesítményének és skálázhatóságának javítására szolgáló gyorsítótárként használják. A teljesítmény javítása érdekében a rendszer ideiglenesen átmásolja a gyakran használt adatokat egy, az alkalmazás közelében lévő gyors tárolóba. Az [Azure cache for Redis](https://redis.io/)esetében ez a gyors tárterület az Azure cache for Redis memóriában található, és nem a lemezről egy adatbázisból tölthető be.

A Redis-hez készült Azure cache memóriában tárolt adatstruktúra-tárolóként, elosztott, nem összehasonlítható adatbázisként és egy Message Broker szolgáltatásként is használható. A Redis-motor kis késésének és nagy átviteli sebességének köszönhetően javul az alkalmazás teljesítménye.

Az Azure cache for Redis hozzáférést biztosít egy biztonságos, dedikált Redis cache-hez. A Redis készült Azure cache-t a Microsoft felügyeli, amely az Azure-on belül fut, és az Azure-on belüli és kívüli alkalmazások számára is elérhető.

## <a name="using-azure-cache-for-redis"></a>Az Azure cache használata a Redis

A Redis-hez készült Azure cache számos gyakori mintázatot használ az alkalmazások architektúrájának támogatásához vagy az alkalmazások teljesítményének növeléséhez. A leggyakoribbak többek között a következők:

| Mintázat      | Leírás                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Mivel az adatbázis nagy méretű lehet, ezért nem ajánlott az egész adatbázist betölteni a gyorsítótárba. Gyakran a [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) mintával, szükség szerint töltik be az adatelemeket a gyorsítótárba. Ha a rendszer módosításokat végez a háttéradatokon, akkor az időnként a gyorsítótárat is frissítheti, amelyet más ügyfeleknek is továbbít. Emellett a rendszer lejárati időt rendelhet az adatelemekhez, vagy egy kiürítési szabályzat segítségével újból betöltheti az adatfrissítéseket a gyorsítótárba.|
| [Tartalmak gyorsítótárazása](cache-aspnet-output-cache-provider.md) | A legtöbb weboldal sablonokból jön létre, és rendelkezik többek között fejlécekkel, láblécekkel, eszköztárakkal, menükkel stb. Nem változnak gyakran, így nem ajánlott dinamikusan létrehozni őket. A memóriában tárolt gyorsítótár (például az Azure cache for Redis) használatával a webkiszolgálók gyorsan hozzáférhetnek az ilyen típusú statikus tartalmakhoz a háttérbeli adattárolók összevetése révén. Ez a minta csökkenti a feldolgozási időt és a kiszolgálóterhelést, amely a tartalmak dinamikus létrehozásával járna. Ennek köszönhetően csökkenhet a webkiszolgálók válaszideje, valamint csökkenthető a számítási feladatok kezeléséhez szükséges kiszolgálók száma. A Redis készült Azure cache biztosítja a Redis kimeneti gyorsítótár-szolgáltatóját, hogy támogassa ezt a mintát a ASP.NET.|
| [Felhasználói munkamenetek gyorsítótárazása](cache-aspnet-session-state-provider.md) | Ezt a mintát általában áruházi kosaraknál és olyan egyéb felhasználói előzmény típusú adatoknál használják, amelyeket a webalkalmazás a felhasználói cookie-khoz társíthat. Ha túl sok információt tárol cookie-kban, csökkenhet a teljesítmény, mivel a cookie mérete nő, és a rendszer minden kérés esetén átadja és ellenőrzi a cookie-t. Egy jellemző megoldás a cookie kulcsként való használata az adatok háttér-adatbázisból való lekérdezéséhez. A memóriában tárolt gyorsítótár, például a Redis-hez készült Azure cache használatával az adatok egy felhasználóhoz való hozzárendelése sokkal gyorsabb, mint a teljes körű, kapcsolatban álló adatbázisokkal való interakció. |
| Feladatok és üzenetek üzenetsor-kezelése | Amikor az alkalmazások kéréseket fogadnak, a kéréshez társított műveletek végrehajtása gyakran hosszabb időt vesz igénybe. Gyakori eljárás a hosszabb ideig futó műveletek üzenetsorba való állítása, amelynek a feldolgozása később, esetenként egy másik kiszolgálón történik. Ezt a késleltetési módot a tevékenységek üzenetsorba való helyezésének nevezik. Számos szoftverösszetevőt úgy terveztek, hogy támogassa a tevékenységek üzenetsorba való helyezését. A Redis-hez készült Azure cache ezt a célt szolgálja, valamint egy elosztott várólistát is.|
| Elosztott tranzakciók | Az alkalmazásokra vonatkozó gyakori követelmény, hogy egyetlen (atomi) műveletként tudják futtatni a háttér-adattárra irányuló parancssorozatokat. Minden parancsnak sikeresnek kell lennie, vagy mindegyiket vissza kell állítani a kezdeti állapotba. A Redis-hez készült Azure cache egyetlen műveletként futtatja a parancsokat a [tranzakciók](https://redis.io/topics/transactions)formájában. |

## <a name="azure-cache-for-redis-offerings"></a>Azure cache a Redis ajánlatokhoz

A Redis készült Azure cache a következő szinteknél érhető el:

| Szint | Leírás |
|---|---|
Alapszintű | Egy csomópontos gyorsítótár. Ez a szint több memóriaméretet támogat (250 MB–53 GB). Ez a szint ideális a fejlesztéshez/teszteléshez és a nem kritikus számítási feladatokhoz. Az alapszintű csomag nem rendelkezik szolgáltatásszint-szerződéssel (SLA) |
| Standard | Replikált gyorsítótár egy két csomópontos, elsődleges/másodlagos konfigurációban, amelyet a Microsoft kezel, és magas (99,9%-os) rendelkezésre állású szolgáltatásszint-szerződéssel rendelkezik |
| Prémium | A prémium szint a nagyvállalati használatra kész szint. A prémium szintű gyorsítótárak több funkciót támogatnak, és nagyobb átviteli sebességgel, valamint gyorsabb válaszidőkkel rendelkeznek. A prémium szintű gyorsítótárakat nagyobb teljesítményű hardvereken helyezik üzembe, és az alapszintű vagy a standard szintnél jobb teljesítményt biztosítanak. Ez az előny azt jelenti, hogy az azonos méretű gyorsítótár esetében a standard szinthez képest magasabb lesz a prémium szintű kapacitás. |

> [!TIP]
> A mérettel, az átviteli sebességgel és a sávszélességgel kapcsolatos további információkért tekintse meg az [Azure cache for Redis – gyakori kérdések](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)című témakört.
>

A gyorsítótárat a létrehozása után is vertikálisan felskálázhatja magasabb szintűvé. Az alacsonyabb szintre való vertikális leskálázás nem támogatott. A részletes skálázási utasításokért lásd: az [Azure cache skálázása Redis](cache-how-to-scale.md) és [a skálázási műveletek automatizálása](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Az [Azure cache for Redis díjszabási](https://azure.microsoft.com/pricing/details/cache/) oldala az egyes szintek részletes összehasonlítását tartalmazza. Az alábbi táblázat a szintek által támogatott néhány funkciót írja le:

| Funkció leírása | Prémium | Standard | Alapszintű |
| ------------------- | :-----: | :------: | :---: |
| [Szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-fürt](cache-how-to-premium-clustering.md) |✔|-|-|
| [Biztonság a tűzfalszabályok használatával](cache-configure.md#firewall) |✔|✔|✔|
| Titkosítás átvitel közben |✔|✔|✔|
| [Továbbfejlesztett biztonság és elkülönítés VNettel](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importálás és exportálás](cache-how-to-import-export-data.md) |✔|-|-|
| [Ütemezett frissítések](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Georeplikáció](cache-how-to-geo-replication.md) |✔|-|-|
| [Újraindítás](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>További lépések

* [ASP.net-webalkalmazás](cache-web-app-howto.md) – rövid útmutató Hozzon létre egy egyszerű ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.
* [.Net](cache-dotnet-how-to-use-azure-redis-cache.md) gyors útmutató Hozzon létre egy .NET-alkalmazást, amely egy Azure cache-t használ a Redis.
* [.Net Core](cache-dotnet-core-quickstart.md) gyors útmutató Hozzon létre egy .NET Core-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Node. js](cache-nodejs-get-started.md) rövid útmutató Hozzon létre egy egyszerű Node. js-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Java](cache-java-get-started.md) rövid útmutató Hozzon létre egy egyszerű Java-alkalmazást, amely egy Azure cache-t használ a Redis.
* [Python](cache-python-get-started.md) rövid útmutató Hozzon létre egy olyan Python-alkalmazást, amely egy Azure cache-t használ a Redis.
