---
title: Skálázható felhőalapú adatbázisok készítése
description: Méretezhető .NET-adatbázis-alkalmazások készítése a rugalmas adatbázis-ügyféloldali kódtár használatával
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: 0b5b3c924a644c065327db36a6a8d64b4a552d40
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690504"
---
# <a name="building-scalable-cloud-databases"></a>Skálázható felhőalapú adatbázisok készítése

Az adatbázisok horizontális felskálázása könnyen megvalósítható a Azure SQL Database méretezhető eszközeivel és szolgáltatásaival. Az **Elastic Database ügyféloldali kódtár** használatával méretezhető adatbázisok hozhatók létre és kezelhetők. Ez a funkció lehetővé teszi, hogy az Azure SQL-adatbázisok százait vagy akár ezreit használó többszegmensű alkalmazásokat egyszerűen fejlesszen.

Letöltés:

* A könyvtár Java-verziója: [Maven Central adattár](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A könyvtár .NET-verziója: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentáció

1. [Ismerkedés az Elastic Database-eszközökkel](sql-database-elastic-scale-get-started.md)
2. [Elastic Database funkciók](sql-database-elastic-scale-introduction.md)
3. [A szilánkleképezés kezelése](sql-database-elastic-scale-shard-map-management.md)
4. [Meglévő adatbázisok migrálása a vertikális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
6. [Több szegmenses lekérdezések](sql-database-elastic-scale-multishard-querying.md)
7. [Szegmens hozzáadása Elastic Database eszközök használatával](sql-database-elastic-scale-add-a-shard.md)
8. [Több-bérlős alkalmazások rugalmas adatbázis-eszközökkel és sor szintű biztonsággal](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Ügyféloldali függvénytár-alkalmazások frissítése](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Rugalmas lekérdezések áttekintése](sql-database-elastic-query-overview.md)
11. [Rugalmas adatbáziseszközökkel kapcsolatos kifejezések](sql-database-elastic-scale-glossary.md)
12. [Ügyféloldali kódtár Elastic Database Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Rugalmas adatbázis-ügyféloldali kódtár, jól öltözött](sql-database-elastic-scale-working-with-dapper.md)
14. [Felosztás és egyesítés eszköz](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-client-library.md) 
16. [Gyakori kérdések a rugalmas adatbázis-eszközökről](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Ügyfél-képességek

A horizontális felskálázást *használó alkalmazások* horizontálisan a fejlesztő és a rendszergazda számára is kihívást jelent. Az ügyféloldali kódtár leegyszerűsíti a felügyeleti feladatokat azáltal, hogy olyan eszközöket biztosít, amelyek lehetővé teszik a fejlesztők és a rendszergazdák számára a kibővített adatbázisok kezelését. Egy tipikus példában számos adatbázis létezik, amely a "szegmensek" néven is ismert. Az ügyfelek ugyanabban az adatbázisban találhatók, és az ügyfelek egy-egy adatbázisa (egybérlős séma). Az ügyféloldali kódtár a következő funkciókat tartalmazza:

- Szegmenses társítások **kezelése**: a rendszer létrehozza a "szegmens Map Manager" nevű speciális adatbázist. A szegmensek közötti társítások kezelése lehetővé teszi, hogy az alkalmazások a szegmensekkel kapcsolatos metaadatokat kezeljék. A fejlesztők ezt a funkciót használhatják az adatbázisok szegmensként való regisztrálásához, leírják az egyes horizontális kulcsok vagy a kulcsok tartományának leképezéseit az adatbázisokra, és karbantartják ezeket a metaadatokat, mivel az adatbázisok száma és összetétele változik a kapacitás változásainak megfelelően. A rugalmas adatbázis-ügyféloldali kódtár nélkül sok időt kell fordítania a felügyeleti kód megírására a horizontális skálázás végrehajtása során. Részletekért lásd: a szegmenses [hozzárendelések kezelése](sql-database-elastic-scale-shard-map-management.md).

- **Adatfüggő útválasztás**: Képzelje el az alkalmazásba érkező kérést. A kérelem horizontálisan megjelenő kulcs értéke alapján az alkalmazásnak meg kell határoznia a megfelelő adatbázist a kulcs értéke alapján. Ezután megnyílik egy kapcsolódás az adatbázishoz a kérelem feldolgozásához. Az Adatfüggő útválasztás lehetővé teszi, hogy a kapcsolatokat egyetlen egyszerű hívással nyissa meg az alkalmazás szegmenses térképére. Az Adatfüggő útválasztás egy másik, a rugalmas adatbázis ügyféloldali függvénytárában található funkció által lefedett infrastruktúra-kód. Részletekért lásd: [az Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md).
- **Több szegmenses lekérdezések (MSQ)** : több szegmenses lekérdezés működik, ha egy kérelem több (vagy az összes) szegmenst is magában foglal. Egy több szegmensből álló lekérdezés ugyanazt a T-SQL-kódot hajtja végre az összes szegmensen vagy egy szegmensen. A résztvevő szegmensek eredményeit egyesítjük egy átfogó eredménybe az Unió összes szemantikai funkciójával. Az ügyféloldali függvénytáron keresztül közzétett funkciók számos feladatot kezelnek, többek között a következők: a kapcsolatok kezelése, a szálak kezelése, a hibák kezelése és a közbenső eredmények feldolgozása. A MSQ legfeljebb száz szegmenst tud lekérdezni. Részletekért lásd: [több szegmenses lekérdezés](sql-database-elastic-scale-multishard-querying.md).

Általánosságban elmondható, hogy a rugalmas adatbázis-eszközöket használó ügyfeleink teljes T-SQL-funkciókat kapnak, amikor a szegmensek közötti helyi műveleteket elküldik, és nem a saját szemantikai feladatait.



## <a name="next-steps"></a>További lépések

- Elastic Database ügyféloldali kódtár ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.net](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – a könyvtár **letöltéséhez** .

- Ismerkedés a [rugalmas adatbázis-eszközökkel](sql-database-elastic-scale-get-started.md) – az ügyféloldali funkciókat bemutató **minta alkalmazás** kipróbálása.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.net](https://github.com/Azure/elastic-db-tools)) – a kódhoz való hozzájárulást tesz elérhetővé.
- [Azure SQL Database rugalmas lekérdezés áttekintése](sql-database-elastic-query-overview.md) – rugalmas lekérdezések használata.

- Adatáthelyezés a kibővített [felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md) – a **felosztott egyesítés eszköz**használatával kapcsolatos utasításokért.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

