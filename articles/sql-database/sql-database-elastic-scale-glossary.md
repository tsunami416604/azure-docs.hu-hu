---
title: Rugalmas adatbázis-eszközök szószedet
description: A rugalmas adatbázis-eszközökhöz használt kifejezések magyarázata
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823634"
---
# <a name="elastic-database-tools-glossary"></a>Rugalmas adatbázis-eszközök szószedet

A következő kifejezések vannak definiálva a [rugalmas adatbázis-eszközök](sql-database-elastic-scale-introduction.md), az Azure SQL Database egyik szolgáltatása. Az eszközök a [szegmenstérképek](sql-database-elastic-scale-shard-map-management.md), valamint az [ügyfélkönyvtár](sql-database-elastic-database-client-library.md), a [felosztott egyesítési eszköz](sql-database-elastic-scale-overview-split-and-merge.md), [a rugalmas készletek](sql-database-elastic-pool.md)és a [lekérdezések](sql-database-elastic-query-overview.md)kezelésére szolgálnak . 

Ezeket a kifejezéseket a [shard hozzáadása rugalmas adatbázis-eszközök használatával](sql-database-elastic-scale-add-a-shard.md) és a RecoveryManager osztály használatával a [shard térképekkel kapcsolatos problémák megoldásához](sql-database-elastic-database-recovery-manager.md)használja.

![Rugalmas méretezési kifejezések][1]

**Adatbázis**: Egy Azure SQL-adatbázis. 

**Adatfüggő útválasztás:** Az a funkció, amely lehetővé teszi, hogy egy alkalmazás egy adott szegmenshez adott szegmenshez csatlakozzon. Lásd: [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md). Hasonlítsa össze a **[multi-shard lekérdezéssel.](sql-database-elastic-scale-multishard-querying.md)**

**Globális shard térkép**: A szilánkok kulcsai és a hozzájuk tartozó szilánkok közötti térkép egy **szegmenskészleten**belül . A globális shard térkép a **shard térképkezelőben van tárolva.** Hasonlítsa össze a **helyi shard térkép**.

**Lista shard térkép:** A shard térkép, amelyben a szilánkok kulcsok külön-külön vannak leképezve. Hasonlítsa össze a **Range Shard térkép .**   

**Helyi shard térkép**: A shard, a helyi shard térkép tartalmazza a shardlets, amelyek a shard leképezések található a shard.

**Többszegmenses lekérdezés:** Az a képesség, hogy egy lekérdezést több szegmensek ellen; az eredményhalmazok a UNION ALL szemantikával (más néven "fan-out lekérdezés" használatával) adják vissza. Hasonlítsa össze az **adatfüggő útválasztásokkal.**

**Több-bérlős** és **egy-bérlős:** Ez egy egy-bérlős adatbázis és egy több-bérlős adatbázis:

![Egy- és több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Az alábbiakban a **szilánkos** egy- és több-bérlős adatbázisok ábrázolása. 

![Egy- és több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Tartomány shard térkép:** A shard térkép, amelyben a shard terjesztési stratégia alapul több tartományok összefüggő értékek. 

**Hivatkozási táblák:** Táblák, amelyek nem szilánkos, de replikálódik a szilánkok között. Az irányítószámok például egy referenciatáblában tárolhatók. 

**Shard:** Egy Azure SQL-adatbázis, amely egy szilánkos adatkészlet adatait tárolja. 

**Szilánkrugalmasság**: A **vízszintes és** a **függőleges méretezés**végrehajtása.

**Szilánkos táblák:** Szilánkos táblák, azaz amelynek adatai a szilánkok között a szilánkok kulcsértékei alapján oszlik meg. 

**Szilánkos kulcs:** Oszlopérték, amely meghatározza, hogyan adatok között oszlik meg. Az értéktípus a következők egyike lehet: **int**, **bigint**, **varbinary**vagy **uniqueidentifier**. 

**Shard set**: A szegmensek gyűjteménye, amelyek a shard térképkezelőben ugyanahhoz a szegmenstérképhez vannak rendelve.  

**Shardlet:** A szilánkok egy szilánkos kulcs ának egyetlen értékével társított összes adat. A shardlet a szilánkos táblák újraelosztása során lehetséges legkisebb adatmozgási egység. 

**Shard térkép:** A szilánkok és a megfelelő szilánkok közötti leképezések készletei.

**Shard map manager:** A felügyeleti objektum és az adattár, amely tartalmazza a shard térkép(ek), shard helyek és leképezések egy vagy több szegmens készletek.

![Leképezések][2]

## <a name="verbs"></a>Parancsok
**Horizontális skálázás**: A horizontális felskálázás (vagy a) a szegmensek gyűjteménye hozzáadása vagy eltávolítása a szilánkok egy szegmens térkép, az alábbiak szerint.

![Vízszintes és függőleges méretezés][3]

**Egyesítés:** A két szilánkról egy szegmensre való áthelyezése és a shard térkép ennek megfelelő frissítése.

**Shardlet áthelyezése:** A cselekmény áthelyezése egy shardlet egy másik szegmensben. 

**Shard:** A horizontálisan particionálási azonos strukturált adatok horizontális an horizontális kulcs alapján horizontálisan.

**Split**: A cselekmény több shardlets egyik shard a másikra (általában új) shard. A szegmensek kulcs a felhasználó által megosztott pontként biztosított.

**Vertikális skálázás:** Az egyes szegmensek számítási méretének felskálázása (vagy leskálázása). Például egy szegmens standardról prémiumra történő módosítása (ami több számítási erőforrást eredményez). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

