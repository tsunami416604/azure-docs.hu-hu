---
title: "A rugalmas adatbázis eszközök szószedet |} Microsoft Docs"
description: "A rugalmas adatbáziseszközöket használt kifejezések magyarázatát"
services: sql-database
documentationcenter: 
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 75abd818b29c78a4ae7499741904637e8ed6798c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="elastic-database-tools-glossary"></a>A rugalmas adatbázis eszközök szószedet
A következő feltételek meghatározása a [skálázáshoz rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md), Azure SQL-adatbázis szolgáltatása. Az eszközök kezeléséhez használt [shard leképezhető](sql-database-elastic-scale-shard-map-management.md), és adja meg a [ügyféloldali kódtár](sql-database-elastic-database-client-library.md), a [vegyes egyesítéses eszköz](sql-database-elastic-scale-overview-split-and-merge.md), [rugalmas készletek](sql-database-elastic-pool.md), és [lekérdezések](sql-database-elastic-query-overview.md). 

Ezeket a kifejezéseket használjuk [hozzáadása egy rugalmas eszközökkel shard](sql-database-elastic-scale-add-a-shard.md) és [a RecoveryManager osztály használatával shard térkép problémák elhárításának](sql-database-elastic-database-recovery-manager.md).

![Rugalmas méretezési feltételek][1]

**Adatbázis**: az Azure SQL-adatbázist. 

**Adatok függő útválasztási**: A funkció, amely lehetővé teszi egy alkalmazás egy adott horizontális kulcs megadott shard kapcsolódjon. Lásd: [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md). Hasonlítsa össze  **[több Shard lekérdezés](sql-database-elastic-scale-multishard-querying.md)**.

**Globális shard térkép**: A térkép horizontális kulcsok és a megfelelő szilánkok belül között egy **shard set**. A globális shard térkép tárolja a **shard térkép manager**. Hasonlítsa össze **helyi shard térkép**.

**Lista shard térkép**: shard térképre mely horizontális a kulcsok külön-külön vannak leképezve. Hasonlítsa össze **Shard térkép tartomány**.   

**Helyi shard térkép**: egy shard tárolják, a helyi shard leképezés a shardlets található a szilánkcímtárban leképezéseit tartalmazza.

**Több shard lekérdezés**: egy lekérdezést hajtanak több szilánkok; képes eredmények beállítása UNION ALL szemantikájú (más néven "szétterítési lekérdezés") adott vissza. Hasonlítsa össze **adatok függő útválasztási**.

**Több-bérlős** és **Single-bérlő**: Ez egy egyetlen-bérlő adatbázis és a több-bérlős adatbázis jeleníti meg:

![Egyetlen és több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Íme egy ábrázolása **szilánkos** egyetlen és több-bérlős adatbázisok. 

![Egyetlen és több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Tartomány shard térkép**: shard térképre, amelyben a shard terjesztési stratégia több tartomány folytonos értékek alapján. 

**Táblák hivatkozhat**: táblákat, amelyek nem szilánkos, de a szilánkok replikálódnak. Zip-kódok például egy összefoglaló táblázatot is tárolható. 

**A shard**: az Azure SQL-adatbázis, amely a szilánkos adatkészletet adatait tárolja. 

**A shard rugalmasság**: hajthatnak végre mindkét **horizontális skálázás** és **függőleges skálázás**.

**Horizontálisan skálázott táblák**: táblákat, amelyek szilánkos, azaz, amelynek adatait a horizontális értékek alapján szilánkok között van elosztva. 

**Horizontális kulcs**: határozza meg, hogy milyen adatok szilánkok pontjain oszlop értéke. Az érték típusa a következők egyike lehet: **int**, **bigint**, **varbinary**, vagy **uniqueidentifier**. 

**A shard set**: a shard térkép kezelő ugyanazt a shard térképet attribútummal szilánkok gyűjteménye.  

**Shardlet**: az összes adat egyetlen értéket egy horizontális skálázási kulcsának egy shard társított. Egy shardlet adatátvitelt jelölik a lehető legkisebb egység esetén szilánkos táblák terjesztése. 

**A shard térkép**: horizontális kulcsok és a megfelelő szilánkok közötti leképezések halmaza.

**A shard térkép manager**: A felügyeleti-objektum és az adattárhoz, amely tartalmazza a shard azt, a shard helyek és egy vagy több shard készletet leképezéseit.

![Leképezések][2]

## <a name="verbs"></a>Műveletek
**Horizontális skálázás**: scaling out (vagy a) történő hozzáadásával vagy eltávolításával a shard térképre szilánkok alább látható módon szilánkok gyűjteménye.

![Vízszintes és függőleges skálázás][3]

**Egyesítési**: két szilánkok shardlets áthelyezése egy shard, és ennek megfelelően frissítése a shard leképezés.

**Shardlet áthelyezés**: az a folyamat egyetlen shardlet áthelyezése egy másik szilánkcímtárban. 

**A shard**: vízszintesen particionálás azonos módon történő strukturált adatok egy horizontális skálázási kulcs alapján több adatbázis között.

**Vegyes**: az a folyamat egy shard több shardlets áthelyezése egy másik (általában új) szilánkcímtárban. Egy horizontális skálázási kulcsot a szétválási pont, a felhasználó által megadott.

**Függőleges méretezés**: az a folyamat felfelé skálázással (vagy le) egy egyedi shard teljesítményszintjét. Például módosítása a shard szabványos Premium (amely elegendő számítási erőforrás eredményez). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

