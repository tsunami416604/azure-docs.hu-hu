---
title: "A méretezhető felhőbeli adatbázisok létrehozása |} Microsoft Docs"
description: "A rugalmas adatbázis ügyféloldali kódtár méretezhető .NET adatbázis alkalmazások létrehozása"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 1f11c52d-13c1-4994-b9b1-5b1ae2f9255f
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: ddove
ms.openlocfilehash: 721b87c67aa5e8002f21faca5a10fe41b8958e1e
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="building-scalable-cloud-databases"></a>Skálázható felhőalapú adatbázisok készítése
Adatbázisok kiterjesztése könnyen elvégezhető méretezhető eszköz és funkció használata az Azure SQL Database. Különösen, használhatja a **Elastic Database ügyféloldali kódtárának** létrehozásához és kezeléséhez kiterjesztett adatbázisok. Ez a funkció lehetővé teszi, hogy könnyen fejleszthet a több száz használó szilánkos alkalmazások – vagy akár több ezer – az Azure SQL-adatbázisok. [Rugalmas feladat](sql-database-elastic-jobs-powershell.md) ezután felhasználhatók ezeknek az adatbázisoknak könnyű kezelés érdekében.

Letöltése:
* a könyvtár a .NET változata: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).
* a Java verzióját a könyvtárban, lásd: [Maven központi tárházban](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).

## <a name="documentation"></a>Dokumentáció
1. [Ismerkedés az Elastic Database-eszközökkel](sql-database-elastic-scale-get-started.md)
2. [A rugalmas adatbázis-szolgáltatások](sql-database-elastic-scale-introduction.md)
3. [A szilánkleképezés kezelése](sql-database-elastic-scale-shard-map-management.md)
4. [Telepítse át a meglévő adatbázisok kívánt réteget](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
6. [Több shard lekérdezések](sql-database-elastic-scale-multishard-querying.md)
7. [A rugalmas adatbázis-eszközökkel szilánkcímtárban hozzáadása](sql-database-elastic-scale-add-a-shard.md)
8. [Több-bérlős alkalmazásokhoz a rugalmas adatbáziseszközöket és sorszintű biztonsággal](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Szalagtár ügyfélalkalmazások frissítése](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Rugalmas lekérdezések – áttekintés](sql-database-elastic-query-overview.md)
11. [Rugalmas adatbáziseszközökkel kapcsolatos kifejezések](sql-database-elastic-scale-glossary.md)
12. [Az Entity Framework rugalmas adatbázis ügyféloldali kódtár](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [A rugalmas adatbázis ügyféloldali kódtára a Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Vegyes egyesítéses eszköz](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-client-library.md) 
16. [Gyakran ismételt kérdések a rugalmas adatbázis-eszközök](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Ügyfél-képességek
Használó alkalmazások kiterjesztése *horizontális* mind a fejlesztői, valamint a rendszergazda kihívást jelent. Az ügyféloldali kódtár mindkét fejlesztők számára lehetővé tevő eszközök biztosításával egyszerűsíti a a felügyeleti feladatokat, és kiterjesztett adatbázisok a rendszergazdák kezelik. Például nincsenek számos más adatbázis, "szilánkok," néven kezeléséhez. Az ügyfelek ugyanazon ugyanarra az adatbázisra, és egy adatbázisa ügyfél (single-bérlő séma) lehet. Az ügyféloldali kódtár ezeket a funkciókat tartalmazza:

- **A shard térkép felügyeleti**: a shard térkép "manager" különleges adatbázisban jön létre. A shard térkép felügyeleti azt a képességet a szilánkok metaadatainak kezelheti az alkalmazáshoz. Fejlesztők használhatják ezt a funkciót szilánkok adatbázisok regisztrálható, egyes horizontális kulcsok vagy kulcstartományokkal ezeket az adatbázisokat a leképezéseket leíró, és a metaadatok számának karbantartása, és az adatbázisok összeállítás fejlődésének kapacitás módosításokkal. A rugalmas adatbázis ügyféloldali kódtár nélkül kellene egy sok időt programozás a felügyeleti horizontális végrehajtása során. További információkért lásd: [Shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md).

- **Adatok függő útválasztási**: képzelhető el, az alkalmazás beérkező kérelem. A kérelem horizontális kulcs értéke alapján, az alkalmazás meg kell állapítania a megfelelő adatbázishoz, a kulcs értéke alapján. Majd nyílik meg a kapcsolat az adatbázissal feldolgozni a kérelmet. Adatok függő útválasztási lehetővé teszi a kapcsolatok megnyitni az alkalmazás a shard térkép egyetlen könnyen hívás. Adatok függő útválasztási lett egy másik olyan területen infrastruktúra kód, amely a rugalmas adatbázis ügyféloldali kódtár funkcióit mostantól mutatja. További információkért lásd: [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md).
- **Több shard lekérdezéseket (MSQ)**: több shard lekérdezése működik, amikor egy kérelem több (vagy az összes) szilánkok magában foglalja. Több shard lekérdezés ugyanazt a T-SQL-kódot minden szilánkok vagy egy a szilánkok hajtja végre. A programban részt vevő szilánkok eredményeinek egyesülnek egy átfogó eredménykészlet UNION ALL szemantika használatával. A funkciót, az ügyféloldali kódtár keresztül közzétett kezeli számos feladat, többek között: kapcsolat kezelése, a szál felügyeleti, a tartalék kezelése és a köztes eredmények feldolgozásakor. A szilánkok akár több száz MSQ kérdezheti le. További információkért lásd: [több shard lekérdezése](sql-database-elastic-scale-multishard-querying.md).

Általában rugalmas adatbázis-eszközt használó ügyfelek is alkalmasak funkcióját T-SQL helyi shard műveletek szemben, amelyek a saját szemantikája kereszt-shard műveletek elküldésekor.



## <a name="next-steps"></a>Következő lépések

- A rugalmas adatbázis ügyféloldali kódtár ([.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22)) – hogy **letöltése** a könyvtárban.

- [Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md) – próbálja meg a **mintaalkalmazás** ügyfél funkciók azt mutatja be.

- GitHub ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md)) – a kód hozzájárulások végrehajtásához.
- [Az Azure SQL Database rugalmas lekérdezési áttekintése](sql-database-elastic-query-overview.md) - rugalmas lekérdezések használata.

- [Adatok áthelyezése másik kiterjesztett felhő adatbázisok](sql-database-elastic-scale-overview-split-and-merge.md) - használatával a **vegyes egyesítéses eszköz**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

