---
title: Mi az Azure Cache redis? | Microsoft Docs
description: Ismerje meg, mi az Azure Cache redis, és milyen gyakran használják.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 5b2b3a3ec0e9aec603a69211a7493afc31e83283
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236549"
---
# <a name="what-is-azure-cache-for-redis"></a>Mi az Azure Cache redis

A Redis az Azure Cache a népszerű szoftver alapján [Redis](https://redis.io/). Általában a háttér-adattárakra nagymértékben támaszkodó rendszerek teljesítményének és skálázhatóságának javítására szolgáló gyorsítótárként használják. A teljesítmény javítása érdekében a rendszer ideiglenesen átmásolja a gyakran használt adatokat egy, az alkalmazás közelében lévő gyors tárolóba. A [Azure Cache redis](https://redis.io/), ez a gyors tároló memóriához található az Azure redis Cache-lemezről egy adatbázisba való betöltése helyett.

Az Azure Cache redis is használható egy memórián belüli adattár struktúra, elosztott nem relációs adatbázis és a közvetítő. A Redis-motor kis késésének és nagy átviteli sebességének köszönhetően javul az alkalmazás teljesítménye.

Az Azure Cache Redis hozzáférést biztosít egy biztonságos, dedikált Azure Cache redis, a Microsoft felügyeli, Azure-ban üzemeltetett, és elérhető-e bármely alkalmazás belül, vagy az Azure-on kívül.

## <a name="why-use-azure-cache-for-redis"></a>Miért érdemes használni az Azure Cache redis

Nincsenek sok közös mintát, ahol Azure Cache redis használt alkalmazásarchitektúra támogatásához, vagy alkalmazások teljesítményének javítása érdekében. A leggyakoribbak többek között a következők:

| Mintázat      | Leírás                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Mivel az adatbázis nagy méretű lehet, ezért nem ajánlott az egész adatbázist betölteni a gyorsítótárba. Gyakran a [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) mintával, szükség szerint töltik be az adatelemeket a gyorsítótárba. Ha a rendszer módosításokat végez a háttéradatokon, akkor az időnként a gyorsítótárat is frissítheti, amelyet más ügyfeleknek is továbbít. Emellett a rendszer lejárati időt rendelhet az adatelemekhez, vagy egy kiürítési szabályzat segítségével újból betöltheti az adatfrissítéseket a gyorsítótárba.|
| [Tartalmak gyorsítótárazása](cache-aspnet-output-cache-provider.md) | A legtöbb weboldal sablonokból jön létre, és rendelkezik többek között fejlécekkel, láblécekkel, eszköztárakkal, menükkel stb. Nem változnak gyakran, így nem ajánlott dinamikusan létrehozni őket. Egy memórián belüli gyorsítótár használata, például az Azure Cache Redis, a webkiszolgálók gyors hozzáférést biztosít az ilyen típusú statikus tartalom háttér-adattárainak képest. Ez a minta csökkenti a feldolgozási időt és a kiszolgálóterhelést, amely a tartalmak dinamikus létrehozásával járna. Ennek köszönhetően csökkenhet a webkiszolgálók válaszideje, valamint csökkenthető a számítási feladatok kezeléséhez szükséges kiszolgálók száma. Az Azure Cache redis biztosít a redis Cache kimeneti gyorsítótár-szolgáltató támogatási ezt a mintát az ASP.NET-tel.|
| [Felhasználói munkamenetek gyorsítótárazása](cache-aspnet-session-state-provider.md) | Ezt a mintát általában áruházi kosaraknál és olyan egyéb felhasználói előzmény típusú adatoknál használják, amelyeket a webalkalmazás a felhasználói cookie-khoz társíthat. Ha túl sok információt tárol cookie-kban, csökkenhet a teljesítmény, mivel a cookie mérete nő, és a rendszer minden kérés esetén átadja és ellenőrzi a cookie-t. Egy jellemző megoldás a cookie kulcsként való használata az adatok háttér-adatbázisból való lekérdezéséhez. Azure Cache redis, például egy memórián belüli gyorsítótár használata társítson a felhasználó esetén sokkal gyorsabb, mint egy teljes körű relációs adatbázis használata. |
| Feladatok és üzenetek üzenetsor-kezelése | Amikor az alkalmazások kéréseket fogadnak, a kéréshez társított műveletek végrehajtása gyakran hosszabb időt vesz igénybe. Gyakori eljárás a hosszabb ideig futó műveletek üzenetsorba való állítása, amelynek a feldolgozása később, esetenként egy másik kiszolgálón történik. Ezt a késleltetési módot a tevékenységek üzenetsorba való helyezésének nevezik. Számos szoftverösszetevőt úgy terveztek, hogy támogassa a tevékenységek üzenetsorba való helyezését. Az Azure Cache a redis gyorsítótár is szolgál erre a célra is egy elosztott üzenetsorba.|
| Elosztott tranzakciók | Az alkalmazásokra vonatkozó gyakori követelmény, hogy egyetlen (atomi) műveletként tudják futtatni a háttér-adattárra irányuló parancssorozatokat. Minden parancsnak sikeresnek kell lennie, vagy mindegyiket vissza kell állítani a kezdeti állapotba. Az Azure Cache a redis támogatja a kötegelt parancsok végrehajtása formájában egyetlen műveletként [tranzakciók](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Az Azure Cache Redis-i ajánlatainak közzétételéhez

Az Azure Cache redis az alábbi szinteken érhető el:

| Szint | Leírás |
|---|---|
Alapszintű | Egy csomópontos gyorsítótár. Ez a szint több memóriaméretet támogat (250 MB–53 GB). Ez a szint ideális a fejlesztéshez/teszteléshez és a nem kritikus számítási feladatokhoz. Az alapszintű csomag nem rendelkezik szolgáltatásszint-szerződéssel (SLA) |
| Standard | Replikált gyorsítótár egy két csomópontos, elsődleges/másodlagos konfigurációban, amelyet a Microsoft kezel, és magas (99,9%-os) rendelkezésre állású szolgáltatásszint-szerződéssel rendelkezik |
| Prémium | A prémium szint a vállalati használatra kész szint. A prémium szintű gyorsítótárak több funkciót támogatnak, és nagyobb átviteli sebességgel, valamint gyorsabb válaszidőkkel rendelkeznek. A prémium szintű gyorsítótárakat nagyobb teljesítményű hardvereken helyezik üzembe, és az alapszintű vagy a standard szintnél jobb teljesítményt biztosítanak. Ez azt jelenti, hogy egy ugyanolyan méretű gyorsítótár esetén az átviteli sebesség prémium szinten a standard szintnél nagyobb lesz. |

> [!TIP]
> Méret, teljesítmény és a sávszélesség a prémium gyorsítótárak kapcsolatos további információkért lásd: [redis Cache – gyakori kérdések az Azure Cache](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

A gyorsítótárat a létrehozása után is vertikálisan felskálázhatja magasabb szintűvé. Az alacsonyabb szintre való vertikális leskálázás nem támogatott. A méretezési részletes tudnivalókért lásd: [How to Scale Azure Cache redis](cache-how-to-scale.md) és [hogyan automatizálhatja a skálázási művelet](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Funkciók összehasonlítása

A [Azure Cache Redis díjszabási](https://azure.microsoft.com/pricing/details/cache/) oldal nyújt részletes összehasonlítását láthatja az egyes csomagok. Az alábbi táblázat a szintek által támogatott néhány funkciót írja le:

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

* [ASP.NET webes alkalmazás – rövid útmutató](cache-web-app-howto.md) hozzon létre egy egyszerű ASP.NET-webalkalmazás, egy Azure Cache redis használó.
* [.NET – rövid útmutató](cache-dotnet-how-to-use-azure-redis-cache.md) hozzon létre egy Azure Cache redis használó .NET-alkalmazás.
* [.NET core – rövid útmutató](cache-dotnet-core-quickstart.md) hozzon létre egy .NET Core-alkalmazást, amely egy Azure Cache Redis használ.
* [NODE.js – rövid útmutató](cache-nodejs-get-started.md) hozzon létre egy egyszerű Node.js-alkalmazást, amely egy Azure Cache Redis használ.
* [Java rövid](cache-java-get-started.md) hozzon létre egy egyszerű Java-alkalmazás, amely egy Azure Cache Redis használ.
* [Python rövid](cache-python-get-started.md) hozzon létre egy Python-alkalmazás, amely egy Azure Cache Redis használ.
