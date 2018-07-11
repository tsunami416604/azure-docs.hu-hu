---
title: Mi az az Azure Redis Cache? | Microsoft Docs
description: Az Azure Redis Cache és a használatának ismertetése.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 585dcd120c42562b1520d4454f9d04e445553101
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096192"
---
# <a name="what-is-azure-redis-cache"></a>Mi az Azure Redis Cache

Az Azure Redis Cache a népszerű, nyílt forráskódú [Redis Cache](https://redis.io/)-re épül. Általában a háttér-adattárakra nagymértékben támaszkodó rendszerek teljesítményének és skálázhatóságának javítására szolgáló gyorsítótárként használják. A teljesítmény javítása érdekében a rendszer ideiglenesen átmásolja a gyakran használt adatokat egy, az alkalmazás közelében lévő gyors tárolóba. A [Redis Cache](https://redis.io/) használata esetén ezt a gyors tárolót nem egy adatbázis tölti be a lemezről, hanem a Redis Cache tárolja a memóriában.

Az Azure Redis Cache ezenkívül memórián belüli adatszerkezet-tárolóként, elosztott nem relációs adatbázisként és üzenetközvetítőként is használható. A Redis-motor kis késésének és nagy átviteli sebességének köszönhetően javul az alkalmazás teljesítménye.

Az Azure Redis Cache hozzáférést biztosít egy biztonságos, dedikált Redis Cache gyorsítótárhoz, amely az Azure-on belül üzemel, a Microsoft kezeli, és az Azure-on belül vagy azon kívül bármelyik alkalmazás számára hozzáférhető.

## <a name="why-use-azure-redis-cache"></a>Miért érdemes az Azure Redis Cache-t használni?

Számos gyakori minta létezik, ahol a Redis Cache-t az alkalmazás architektúrájának támogatásához vagy az alkalmazás teljesítményének javításához használják. A leggyakoribbak többek között a következők:

| Mintázat      | Leírás                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Mivel az adatbázis nagy méretű lehet, ezért nem ajánlott az egész adatbázist betölteni a gyorsítótárba. Gyakran a [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) mintával, szükség szerint töltik be az adatelemeket a gyorsítótárba. Ha a rendszer módosításokat végez a háttéradatokon, akkor az időnként a gyorsítótárat is frissítheti, amelyet más ügyfeleknek is továbbít. Emellett a rendszer lejárati időt rendelhet az adatelemekhez, vagy egy kiürítési szabályzat segítségével újból betöltheti az adatfrissítéseket a gyorsítótárba.|
| [Tartalmak gyorsítótárazása](cache-aspnet-output-cache-provider.md) | A legtöbb weboldal sablonokból jön létre, és rendelkezik többek között fejlécekkel, láblécekkel, eszköztárakkal, menükkel stb. Nem változnak gyakran, így nem ajánlott dinamikusan létrehozni őket. Egy memóriabeli gyorsítótár, például az Azure Redis Cache segítségével a háttér-adattáraknál gyorsabb hozzáférést biztosíthat a webkiszolgálóinak az ilyen statikus tartalmakhoz. Ez a minta csökkenti a feldolgozási időt és a kiszolgálóterhelést, amely a tartalmak dinamikus létrehozásával járna. Ennek köszönhetően csökkenhet a webkiszolgálók válaszideje, valamint csökkenthető a számítási feladatok kezeléséhez szükséges kiszolgálók száma. Az Azure Redis Cache a Redis kimeneti gyorsítótár-szolgáltatóval segíti a minta ASP.NET-támogatását.|
| [Felhasználói munkamenetek gyorsítótárazása](cache-aspnet-session-state-provider.md) | Ezt a mintát általában áruházi kosaraknál és olyan egyéb felhasználói előzmény típusú adatoknál használják, amelyeket a webalkalmazás a felhasználói cookie-khoz társíthat. Ha túl sok információt tárol cookie-kban, csökkenhet a teljesítmény, mivel a cookie mérete nő, és a rendszer minden kérés esetén átadja és ellenőrzi a cookie-t. Egy jellemző megoldás a cookie kulcsként való használata az adatok háttér-adatbázisból való lekérdezéséhez. Ha az információkat memóriabeli gyorsítótárral, például az Azure Redis Cache-sel rendeli a felhasználókhoz, az sokkal gyorsabb, mint a teljes relációs adatbázis használata. |
| Feladatok és üzenetek üzenetsor-kezelése | Amikor az alkalmazások kéréseket fogadnak, a kéréshez társított műveletek végrehajtása gyakran hosszabb időt vesz igénybe. Gyakori eljárás a hosszabb ideig futó műveletek üzenetsorba való állítása, amelynek a feldolgozása később, esetenként egy másik kiszolgálón történik. Ezt a késleltetési módot a tevékenységek üzenetsorba való helyezésének nevezik. Számos szoftverösszetevőt úgy terveztek, hogy támogassa a tevékenységek üzenetsorba való helyezését. A Redis Cache ezt a célt is szolgálja az elosztott üzenetsorként való működés mellett.|
| Elosztott tranzakciók | Az alkalmazásokra vonatkozó gyakori követelmény, hogy egyetlen (atomi) műveletként tudják futtatni a háttér-adattárra irányuló parancssorozatokat. Minden parancsnak sikeresnek kell lennie, vagy mindegyiket vissza kell állítani a kezdeti állapotba. A Redis Cache támogatja a parancskötegek [Tranzakciók](https://redis.io/topics/transactions) formájában történő, egyetlen műveletként való végrehajtását. |

## <a name="azure-redis-cache-offerings"></a>Azure Redis Cache-ajánlatok

Az Azure Redis Cache a következő szinteken érhető el:

| Szint | Leírás |
|---|---|
Alapszintű | Egy csomópontos gyorsítótár. Ez a szint több memóriaméretet támogat (250 MB–53 GB). Ez a szint ideális a fejlesztéshez/teszteléshez és a nem kritikus számítási feladatokhoz. Az alapszintű csomag nem rendelkezik szolgáltatásszint-szerződéssel (SLA) |
| Standard | Replikált gyorsítótár egy két csomópontos, elsődleges/másodlagos konfigurációban, amelyet a Microsoft kezel, és magas (99,9%-os) rendelkezésre állású szolgáltatásszint-szerződéssel rendelkezik |
| Prémium | A prémium szint a vállalati használatra kész szint. A prémium szintű gyorsítótárak több funkciót támogatnak, és nagyobb átviteli sebességgel, valamint gyorsabb válaszidőkkel rendelkeznek. A prémium szintű gyorsítótárakat nagyobb teljesítményű hardvereken helyezik üzembe, és az alapszintű vagy a standard szintnél jobb teljesítményt biztosítanak. Ez azt jelenti, hogy egy ugyanolyan méretű gyorsítótár esetén az átviteli sebesség prémium szinten a standard szintnél nagyobb lesz. |

> [!TIP]
> További információ a prémium gyorsítótárak méretéről, teljesítményéről és a sávszélességéről: [Azure Redis Cache – Gyakori kérdések](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).
>

A gyorsítótárat a létrehozása után is vertikálisan felskálázhatja magasabb szintűvé. Az alacsonyabb szintre való vertikális leskálázás nem támogatott. Részletes útmutatás a skálázáshoz: [Az Azure Redis Cache skálázása](cache-how-to-scale.md) és [Skálázási műveletek automatizálása](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Funkciók összehasonlítása

A [Redis Cache díjszabását](https://azure.microsoft.com/pricing/details/cache/) ismertető oldalon megtekintheti az egyes szintek részletes összehasonlítását. Az alábbi táblázat a szintek által támogatott néhány funkciót írja le:

| Funkció leírása | Prémium | Standard | Alapszintű |
| ------------------- | :-----: | :------: | :---: |
| [Szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-fürt](cache-how-to-premium-clustering.md) |✔|-|-|
| [Biztonság a tűzfalszabályok használatával](cache-configure.md#firewall) |✔|✔|✔|
| [Továbbfejlesztett biztonság és elkülönítés VNettel](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importálás és exportálás](cache-how-to-import-export-data.md) |✔|-|-|
| [Frissítések ütemezése](cache-administration.md#schedule-updates) |✔|-|-|
| [Georeplikáció](cache-how-to-geo-replication.md) |✔|-|-|
| [Újraindítás](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>További lépések

* [ASP.NET-webalkalmazás – Rövid útmutató](cache-web-app-howto.md) Egy Azure Redis Cache-t használó egyszerű ASP.NET-webalkalmazás létrehozása.
* [.NET – Rövid útmutató](cache-dotnet-how-to-use-azure-redis-cache.md) Egy Azure Redis Cache-t használó .NET-alkalmazás létrehozása.
* [.NET Core – Rövid útmutató](cache-dotnet-core-quickstart.md) Egy Azure Redis Cache-t használó .NET Core-alkalmazás létrehozása.
* [Node.js – Rövid útmutató](cache-nodejs-get-started.md) Egy Azure Redis Cache-t használó egyszerű Node.js-alkalmazás létrehozása.
* [Java – Rövid útmutató](cache-java-get-started.md) Egy Azure Redis Cache-t használó egyszerű Java-alkalmazás létrehozása.
* [Python – Rövid útmutató](cache-python-get-started.md) Egy Azure Redis Cache-t használó Python-alkalmazás létrehozása.
