---
title: Skálázható felhőalapú adatbázisok készítése |} A Microsoft Docs
description: Az elastic database-ügyfélkódtár méretezhető .NET adatbázis alkalmazások fejlesztése
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: c0d50f3a66d940618f2bc421537b113120a2eaca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006297"
---
# <a name="building-scalable-cloud-databases"></a>Skálázható felhőalapú adatbázisok készítése

Horizontális felskálázás adatbázisok egyszerűen elvégezhető méretezhető eszközök és funkciók használata az Azure SQL Database. Különösen is használhatja a **Elastic Database-ügyfélkódtár** , horizontálisan felskálázott adatbázisok létrehozása és kezelése. Ez a funkció lehetővé teszi, hogy Ön könnyen hozhat létre több száz használatával horizontálisan particionált alkalmazásokat – vagy akár több ezer – Azure SQL Database-adatbázisok. [Rugalmas feladatok](sql-database-elastic-jobs-powershell.md) ezután felhasználhatók ezeknek az adatbázisoknak a könnyű kezelés érdekében.

Letöltése:

* A Java-verzió a könyvtár, lásd: [Maven központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A könyvtár a .NET verzióját, lásd: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentáció

1. [Ismerkedés az Elastic Database-eszközökkel](sql-database-elastic-scale-get-started.md)
2. [Rugalmas adatbázis-szolgáltatások](sql-database-elastic-scale-introduction.md)
3. [A szilánkleképezés kezelése](sql-database-elastic-scale-shard-map-management.md)
4. [Horizontális felskálázás meglévő adatbázisok migrálása](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
6. [Több horizontális partíciós lekérdezések](sql-database-elastic-scale-multishard-querying.md)
7. [Rugalmas Adatbáziseszközök használatáról szilánk hozzáadása](sql-database-elastic-scale-add-a-shard.md)
8. [Az elastic database-eszközökkel és a sorszintű biztonság több-bérlős alkalmazások](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Ügyfélalkalmazások könyvtár frissítése](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Rugalmas lekérdezések áttekintése](sql-database-elastic-query-overview.md)
11. [Rugalmas adatbáziseszközökkel kapcsolatos kifejezések](sql-database-elastic-scale-glossary.md)
12. [Rugalmas adatbázis-ügyfélkódtárnak az Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Elastic database ügyfélkódtár dapperrel](sql-database-elastic-scale-working-with-dapper.md)
14. [Felosztási-egyesítési eszközének](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-client-library.md) 
16. [Elastic database-eszközökkel kapcsolatos gyakori kérdések](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Képességei

Horizontális felskálázás használó alkalmazások *horizontális skálázási* mind a fejlesztő csakúgy, mint a rendszergazda kihívást jelent. Az ügyféloldali kódtár leegyszerűsíti a felügyeleti feladatokat biztosít eszközöket, amelyek lehetővé teszik mind a fejlesztők és rendszergazdák kezelése horizontálisan felskálázott adatbázisok. Egy tipikus példában nincsenek számos adatbázis kezelése "szegmensek" néven ismert. Ügyfelek ugyanabban az adatbázisban vannak elhelyezve, és (egy egybérlős rendszer) ügyfelenként egy adatbázis. Az ügyféloldali kódtár ezeket a funkciókat tartalmazza:

- **Szilánkleképezés-kezelés**: A "szilánkleképezés-kezelő" nevű speciális adatbázis jön létre. Szilánkleképezés-kezelés arra, hogy az alkalmazás kezelheti a szegmenseket metaadatait. Fejlesztők használhatják ezt a funkciót szegmensek adatbázisok regisztrálásához, ismertetik a horizontális skálázás egyedi kulcsok vagy kulcstartományokkal ezeket az adatbázisokat a leképezések és karbantartása a számot a metaadatok és az adatbázisok összeállítás haladásával kapacitás változásainak. Az elastic database-ügyfélkódtár nélkül kellene fordítania rengeteg időt a felügyeleti kódírás, amikor a horizontális skálázás megvalósításáról. További információkért lásd: [Szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md).

- **Adatfüggő útválasztás**: Képzelje el, az alkalmazás érkező kérelmet. A horizontális skálázási kulcs értékét a kérelem alapján, az alkalmazásnak kell meghatározni a megfelelő adatbázist, a kulcs értéke alapján. Majd megnyitja a kapcsolatot az adatbázis feldolgozni a kérelmet. Adatfüggő útválasztás lehetővé teszi az alkalmazás horizontális skálázási térképet be egyetlen egyszerű hívásával nyissa meg a kapcsolatok. Adatfüggő útválasztás volt az infrastruktúra kód, amelyet az elastic database-ügyfélkódtár funkcióit mostantól jelez egy másik területéhez. További információkért lásd: [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md).
- **Több horizontális partíciós lekérdezések (MSQ)**: Többszegmenses lekérdezés működik, ha a kérés magában foglalja a szegmensek több (vagy az összes). Többszegmenses lekérdezés végrehajtja ugyanazt a T-SQL-kódot minden szegmensre vagy készletekbe. Az eredményeket a programban részt vevő szegmensből egyesítve az összesített eredmény UNION ALL szemantika használatával. A funkciókat, mint az ügyféloldali kódtár keresztül közzétett kezeli számos feladat, többek között: kapcsolat kezelése, a szál felügyeleti, a hibák kezelése és a köztes eredményeket feldolgozása. MSQ lekérdezheti a szegmensek akár több száz. További információkért lásd: [több szegmensre vonatkozó lekérdezésekkel](sql-database-elastic-scale-multishard-querying.md).

Általánosságban véve a rugalmas Adatbáziseszközök használatáról vezetjük teljes T-SQL-funkciók megszerezni a szilánkleképezés-helyi operations helyett a saját szemantikával rendelkező szegmensek közötti műveletek elküldésekor.



## <a name="next-steps"></a>További lépések

- Elastic Database-Ügyfélkódtár ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – annak **letöltése** a könyvtárban.

- [Ismerkedés az elastic database-eszközök](sql-database-elastic-scale-get-started.md) – próbálja ki a **mintaalkalmazás** , amely bemutatja az ügyfél-funkciók.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – javaslatokhoz a kódot.
- [Az Azure SQL Database rugalmas lekérdezésének áttekintése](sql-database-elastic-query-overview.md) – rugalmas lekérdezések használatához.

- [Adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md) – útmutató használatához a **felosztási-egyesítési eszközének**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

