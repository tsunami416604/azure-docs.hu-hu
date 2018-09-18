---
title: Rugalmas adatbáziseszközökkel |} A Microsoft Docs
description: Elastic database-eszközök esetében használt kifejezések magyarázatát
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sstein
ms.openlocfilehash: 387f40204c8ab07ba0205fd74b5c6a549efff0ef
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728846"
---
# <a name="elastic-database-tools-glossary"></a>Rugalmas adatbáziseszközökkel kapcsolatos
Az alábbi feltételek vannak definiálva a [rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md), az Azure SQL Database szolgáltatás. Az eszközök kezelésére használhatók [szilánkleképezések](sql-database-elastic-scale-shard-map-management.md), és tartalmazzák a [ügyféloldali kódtár](sql-database-elastic-database-client-library.md), a [felosztási-egyesítési eszközének](sql-database-elastic-scale-overview-split-and-merge.md), [rugalmas készletek](sql-database-elastic-pool.md), és [lekérdezések](sql-database-elastic-query-overview.md). 

Ezek a fogalmak szerepelnek [hozzáadása a rugalmas Adatbáziseszközök használatáról szegmensek](sql-database-elastic-scale-add-a-shard.md) és [szilánkleképezési problémák javítása a RecoveryManager osztállyal](sql-database-elastic-database-recovery-manager.md).

![Rugalmas méretezés feltételek][1]

**Adatbázis**: egy Azure SQL-adatbázist. 

**Adatfüggő útválasztás**: A funkciót, amely lehetővé teszi az alkalmazás egy adott horizontális skálázási kulcs megadott szilánk csatlakozni. Lásd: [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md). Második összehasonlítandó  **[Többszegmenses lekérdezés](sql-database-elastic-scale-multishard-querying.md)**.

**Globális szegmenstérkép**: A térképen a horizontális skálázás kulcsok és a megfelelő szegmens belül között egy **szegmens set**. A globális szegmenstérkép tárolja a **szilánkleképezés-kezelővel**. Második összehasonlítandó **helyi szegmenstérkép**.

**Listás szegmenstérkép**: horizontálispartíció-térkép, mely a horizontális skálázás a kulcsok külön-külön vannak leképezve. Második összehasonlítandó **tartomány-Szegmenstérkép**.   

**Helyi szegmenstérkép**: egy szegmens tárolja, a helyi szegmenstérkép a szegmensben lévő shardlet adatain leképezéseit tartalmazza.

**Többszegmenses lekérdezés**: lehetővé teszi ki egy lekérdezést több szegmens; eredmények csoportok UNION ALL szemantika (más néven "lekérdezés logikájával") adott vissza. Második összehasonlítandó **Adatfüggő útválasztás**.

**Több-bérlős** és **egybérlős**: Ez egy egybérlős adatbázis és a egy több-bérlős adatbázis jeleníti meg:

![Egy vagy több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Íme egy reprezentációja **horizontálisan skálázott** egyetlen és több-bérlős adatbázisok. 

![Egy vagy több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Tartomány-szegmenstérkép**: horizontálispartíció-térkép, amelyben a szegmensek terjesztési stratégia több tartomány folytonos értékek alapján. 

**Táblák hivatkozhat**: táblákat, amelyek nem horizontálisan skálázott, de replikálódnak a szegmensek között. Például az irányítószámok hivatkozási táblában is tárolhatók. 

**Szilánkleképezés**: egy Azure SQL database tárolja az adatokat egy horizontálisan skálázott adatok készletből. 

**A horizontális partíciók rugalmassága**: hajthatnak végre mindkét **horizontális skálázást** és **vertikális skálázás**.

**Horizontálisan particionált táblák**: táblákat, amelyek horizontálisan skálázott, azaz, amelynek az adatait a horizontális skálázási kulcs értéke alapján szegmensek között elosztott. 

**Horizontális skálázási kulcs**: egy oszlop értékét, amely meghatározza, hogyan szegmensek között elosztott adatokat. Az érték típusa a következők egyike lehet: **int**, **bigint**, **varbinary**, vagy **uniqueidentifier**. 

**Szilánkleképezés-készlet**: A gyűjtemény, amely ugyanahhoz a szegmenstérképhez a szilánkleképezés-kezelővel a rendelnek a szegmens.  

**Shardlet**: az összes olyan szegmenskulcsot a szegmensek egyetlen érték társított adatokat. Egy shardlet adatáthelyezés lehető legkisebb egység esetén verziójának terjesztése a horizontálisan particionált táblák. 

**Horizontális skálázási térképet**: horizontális skálázási kulcsok és a megfelelő szegmensek közötti leképezések halmaza.

**Szilánkleképezés-kezelővel**: egy felügyeleti-objektum és az adattárhoz, amely tartalmazza a szegmens map(s), a szegmens helyek és a egy vagy több szegmensben készletek leképezését.

![Leképezések][2]

## <a name="verbs"></a>Műveletek
**Horizontális skálázás**: a méretezés ki (vagy a) a törvény szegmensek hozzáadásával vagy eltávolításával horizontálispartíció-térkép szilánkok, ahogy az alábbi gyűjteményét.

![Vízszintes és függőleges skálázás][3]

**Egyesítse**: shardlet áthelyezése két szegmensből egyik adatszilánkba író, és ennek megfelelően frissíti a horizontális skálázási térképet.

**Shardlet áthelyezési**: A művelet, egy egyetlen shardlet áthelyezése a másik adatszilánkba író. 

**Szilánkleképezés**: a strukturált adatok horizontális particionálása azonos módon történő horizontális skálázási kulcs alapján több adatbázis között.

**Split**: A művelet, egy szegmens több shardlet áthelyezését egy másik (általában új) szegmensben. Olyan szegmenskulcsot split pontot a felhasználó által megadott.

**Függőleges méretezés**: A művelet méretezéssel (), a számítási méret az egyes szegmensek. Például módosítása szegmensek standardról prémium szintre (amely több számítási erőforrást eredményezi). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

