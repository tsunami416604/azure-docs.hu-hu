---
title: Rugalmas adatbáziseszközökkel |} A Microsoft Docs
description: Elastic database-eszközök esetében használt kifejezések magyarázatát
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
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561433"
---
# <a name="elastic-database-tools-glossary"></a>Rugalmas adatbáziseszközökkel kapcsolatos

Az alábbi feltételek vannak definiálva a [rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md), az Azure SQL Database szolgáltatás. Az eszközök kezelésére használhatók [szilánkleképezések](sql-database-elastic-scale-shard-map-management.md), és tartalmazzák a [ügyféloldali kódtár](sql-database-elastic-database-client-library.md), a [felosztási-egyesítési eszközének](sql-database-elastic-scale-overview-split-and-merge.md), [rugalmas készletek](sql-database-elastic-pool.md), és [lekérdezések](sql-database-elastic-query-overview.md). 

Ezek a fogalmak szerepelnek [hozzáadása a rugalmas Adatbáziseszközök használatáról szegmensek](sql-database-elastic-scale-add-a-shard.md) és [szilánkleképezési problémák javítása a RecoveryManager osztállyal](sql-database-elastic-database-recovery-manager.md).

![Rugalmas méretezés feltételek][1]

**Adatbázis**: Azure SQL Database-adatbázis. 

**Adatfüggő útválasztás**: A funkció, amely lehetővé teszi az alkalmazás egy adott horizontális skálázási kulcs megadott szilánk csatlakozni. Lásd: [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md). Második összehasonlítandó  **[Többszegmenses lekérdezés](sql-database-elastic-scale-multishard-querying.md)**.

**Globális szegmenstérkép**: A térkép a horizontális skálázás kulcsok és a megfelelő szegmens belül között egy **szegmens set**. A globális szegmenstérkép tárolja a **szilánkleképezés-kezelővel**. Második összehasonlítandó **helyi szegmenstérkép**.

**Listás szegmenstérkép**: Horizontális skálázási térképet, mely a horizontális skálázás a kulcsok külön-külön vannak leképezve. Második összehasonlítandó **tartomány-Szegmenstérkép**.   

**Helyi szegmenstérkép**: Egy szegmens tárolja, a helyi szegmenstérkép a szegmensben lévő shardlet adatain leképezéseit tartalmazza.

**Többszegmenses lekérdezés**: Lehetővé teszi egy lekérdezést több szegmens; kiadása eredmények csoportok használatával a UNION ALL szemantika (más néven "lekérdezés logikájával") adja vissza. Második összehasonlítandó **Adatfüggő útválasztás**.

**Több-bérlős** és **egybérlős**: Ez jeleníti meg, egy egybérlős adatbázis és a egy több-bérlős adatbázis:

![Egy vagy több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Íme egy reprezentációja **horizontálisan skálázott** egyetlen és több-bérlős adatbázisok. 

![Egy vagy több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Tartomány-szegmenstérkép**: Horizontálispartíció-térkép, amelyben a szegmensek terjesztési stratégia több tartomány folytonos értékek alapján. 

**Táblák hivatkozhat**: Táblákhoz nem horizontálisan skálázott, de replikálódnak a szegmensek között. Például az irányítószámok hivatkozási táblában is tárolhatók. 

**Szilánkleképezés**: Szilánkokra osztott adatok bizonyos adatokat tároló Azure SQL-adatbázist. 

**A horizontális partíciók rugalmassága**: Mindkettő lehetővé **horizontális skálázást** és **vertikális skálázás**.

**Horizontálisan particionált táblák**: A táblázatok, amelyek horizontálisan skálázott, azaz, amelynek az adatait a horizontális skálázási kulcs értéke alapján szegmensek között elosztott. 

**Horizontális skálázási kulcs**: Egy oszlop értéke, amely meghatározza, hogyan szegmensek között elosztott adatokat. Az érték típusa a következők egyike lehet: **int**, **bigint**, **varbinary**, vagy **uniqueidentifier**. 

**Szilánkleképezés-készlet**: A szegmensek, amelyek ugyanahhoz a szegmenstérképhez a szilánkleképezés-kezelővel a rendelnek a gyűjteménye.  

**Shardlet**: A szegmensek horizontális skálázási kulcs egyetlen értéket társított összes adatot. Egy shardlet adatáthelyezés lehető legkisebb egység esetén verziójának terjesztése a horizontálisan particionált táblák. 

**Horizontális skálázási térképet**: A horizontális skálázás kulcsokat és a megfelelő szegmensek közötti leképezések halmaza.

**Szilánkleképezés-kezelővel**: A felügyeleti objektum és az adattárhoz, amely tartalmazza a szegmens map(s), a szegmens helyek és a egy vagy több szegmensben készletek leképezését.

![Leképezések][2]

## <a name="verbs"></a>Műveletek
**Horizontális skálázás**: A méretezés ki (vagy a) a törvény szegmensek hozzáadásával vagy eltávolításával horizontálispartíció-térkép szilánkok, ahogy az alábbi gyűjteményét.

![Vízszintes és függőleges skálázás][3]

**Egyesítse**: A törvény shardlet áthelyezése két szegmensből egyik adatszilánkba író, és ennek megfelelően frissíti a horizontális skálázási térképet.

**Shardlet áthelyezési**: A törvény egyetlen shardlet másik adatszilánkba író való áttérés. 

**Szilánkleképezés**: A vízszintes particionálás azonos módon törvény strukturált adatok horizontális skálázási kulcs alapján több adatbázis között.

**Split**: Az act az egyik szegmens több shardlet áthelyezését egy másik (általában új) szegmensben. Olyan szegmenskulcsot split pontot a felhasználó által megadott.

**Vertikális skálázás**: A művelet méretezéssel (), a számítási méret az egyes szegmensek. Például módosítása szegmensek standardról prémium szintre (amely több számítási erőforrást eredményezi). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

