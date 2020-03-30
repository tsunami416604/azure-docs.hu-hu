---
title: Skálázható felhőalapú adatbázisok készítése
description: Méretezhető .NET adatbázis-alkalmazások létrehozása a rugalmas adatbázis-ügyfélkódtárral
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
ms.openlocfilehash: ae26f669ddbe2cc2c5b6e25a9c1c0229e88dc2e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823910"
---
# <a name="building-scalable-cloud-databases"></a>Skálázható felhőalapú adatbázisok készítése

Az adatbázisok skálázása könnyen elvégezhető az Azure SQL Database méretezhető eszközeivel és funkcióival. Különösen a **rugalmas adatbázis-ügyfélkódtár** használatával hozhat létre és kezelhet kibővített adatbázisokat. Ez a funkció lehetővé teszi, hogy könnyedén fejleszthet szilánkos alkalmazásokat az Azure SQL-adatbázisok százaival – vagy akár ezreivel.

Letöltés:

* A könyvtár Java-verziója, [lásd: Maven Központi Adattár](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A tár .NET verziója, [lásd: NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentáció

1. [Ismerkedés az Elastic Database-eszközökkel](sql-database-elastic-scale-get-started.md)
2. [Rugalmas adatbázis-funkciók](sql-database-elastic-scale-introduction.md)
3. [A szilánkleképezés kezelése](sql-database-elastic-scale-shard-map-management.md)
4. [Meglévő adatbázisok áttelepítése horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
6. [Több szegmenses lekérdezések](sql-database-elastic-scale-multishard-querying.md)
7. [Szegmens hozzáadása rugalmas adatbázis-eszközökkel](sql-database-elastic-scale-add-a-shard.md)
8. [Több-bérlős alkalmazások rugalmas adatbázis-eszközökkel és sorszintű biztonsággal](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Ügyféltár-alkalmazások frissítése](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Rugalmas lekérdezések – áttekintés](sql-database-elastic-query-overview.md)
11. [Rugalmas adatbáziseszközökkel kapcsolatos kifejezések](sql-database-elastic-scale-glossary.md)
12. [Rugalmas adatbázis-ügyféltár entitáskeretrendszerrel](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Rugalmas adatbázis-ügyfélkódtár dapperrel](sql-database-elastic-scale-working-with-dapper.md)
14. [Felosztásos egyesítés i](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-client-library.md) 
16. [Gyakori kérdések a rugalmas adatbázis-eszközökkel kapcsolatos kérdésekről](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Ügyfélképességek

Az alkalmazások *horizontális skálázáshasználatával* horizontális kihívást jelent mind a fejlesztő, mind a rendszergazda számára. Az ügyfélkódtár leegyszerűsíti a felügyeleti feladatokat olyan eszközök biztosításával, amelyek lehetővé teszik mind a fejlesztők, mind a rendszergazdák számára a kibővített adatbázisok kezelését. Egy tipikus példában számos adatbázisok, az úgynevezett "szilánkok", kezelésére. Az ügyfelek ugyanabban az adatbázisban találhatók, és ügyfelenként egy adatbázis (egybérlős séma) található. Az ügyfélkönyvtár a következő szolgáltatásokat tartalmazza:

- **Shard Map Management**: Egy speciális adatbázis az úgynevezett "shard map manager" jön létre. Shard térkép-kezelés az a képesség, egy alkalmazás a szegmensek metaadatainak kezelésére. A fejlesztők ezt a funkciót használhatják az adatbázisok szilánkként történő regisztrálására, az egyes szilánkok vagy kulcstartományok hozzárendeléseinek leírására, valamint a metaadatok karbantartására, ahogy az adatbázisok száma és összetétele a kapacitásváltozásoknak megfelelően alakul ki. A rugalmas adatbázis-ügyfélkódtár nélkül sok időt kell töltenie a felügyeleti kód írásával a szilánkok megvalósításakor. További részletek: [Shard térkép kezelés.](sql-database-elastic-scale-shard-map-management.md)

- **Adatfüggő útválasztás:** Képzeljen el egy kérelmet az alkalmazásba érkező. A kérelem értékkőlehívási kulcsértéke alapján az alkalmazásnak meg kell határoznia a megfelelő adatbázist a kulcsérték alapján. Ezután megnyit egy kapcsolatot az adatbázissal a kérelem feldolgozásához. Az adatfüggő útválasztás lehetővé teszi a kapcsolatok egyetlen egyszerű hívással történő megnyitását az alkalmazás shard térképére. Az adatfüggő útválasztás az infrastruktúrakód egy másik területe volt, amelyet most a rugalmas adatbázis-ügyfélkódtár funkciói fednek le. További információt az [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md).
- **Többszegmenses lekérdezések (MSQ)**: Többshard lekérdezési működik, ha egy kérelem több (vagy az összes) szilánkok. A többszegmenses lekérdezés ugyanazt a T-SQL-kódot hajtja végre az összes szegmensen vagy szegmensek készletén. A részt vevő szegmensek eredményeit egy átfogó eredményhalmazba egyesíti a UNION ALL szemantika használatával. Az ügyféltáron keresztül elérhetővé tett funkciók számos feladatot kezelnek, többek között a kapcsolatkezelést, a szálkezelést, a hibakezelést és a köztes eredmények feldolgozását. Az MSQ legfeljebb több száz szegmenslekérdezést képes lekérdezni. További információt a [Többsánhard lekérdezés.](sql-database-elastic-scale-multishard-querying.md)

Általában a rugalmas adatbázis-eszközök használatával az ügyfelek elvárják, hogy teljes T-SQL-funkciók at shard-local műveletek küldésekor, szemben a kereszt-shard műveletek, amelyek saját szemantika.



## <a name="next-steps"></a>További lépések

- Rugalmas adatbázis-ügyfélkódtár ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – a tár **letöltéséhez.**

- [Első lépések a rugalmas adatbázis-eszközök](sql-database-elastic-scale-get-started.md) - kipróbálni a **minta alkalmazás,** amely bemutatja az ügyfél-funkciókat.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - a kódhoz való hozzájáruláshoz.
- [Azure SQL Database rugalmas lekérdezés áttekintése](sql-database-elastic-query-overview.md) – rugalmas lekérdezések használatához.

- [Adatok áthelyezése a kibővített felhőadatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md) – a **felosztott egyesítési eszköz**használatával kapcsolatos utasításokért.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

