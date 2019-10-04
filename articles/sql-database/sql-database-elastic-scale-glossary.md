---
title: Elastic Database eszközök szószedete | Microsoft Docs
description: A rugalmas adatbázis-eszközökhöz használt használati feltételek magyarázata
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
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568447"
---
# <a name="elastic-database-tools-glossary"></a>Elastic Database eszközök szószedete

A következő feltételek vannak meghatározva a [Elastic Database eszközökhöz](sql-database-elastic-scale-introduction.md), Azure SQL Database a szolgáltatáshoz. Az eszközök a szegmenses [térképek](sql-database-elastic-scale-shard-map-management.md)felügyeletére szolgálnak, és tartalmazzák az [ügyféloldali kódtárat](sql-database-elastic-database-client-library.md), a [felosztott egyesítési eszközt](sql-database-elastic-scale-overview-split-and-merge.md), [](sql-database-elastic-query-overview.md)a [rugalmas készleteket](sql-database-elastic-pool.md)és a lekérdezéseket. 

Ezek a kifejezések a szegmensek [Elastic Database eszközökkel](sql-database-elastic-scale-add-a-shard.md) való hozzáadásával, valamint [a recoverymanager osztállyal osztály használatával határozzák meg a szilánkok térképével kapcsolatos problémákat](sql-database-elastic-database-recovery-manager.md).

![Rugalmas skálázási feltételek][1]

**Adatbázis**: Azure SQL-adatbázis. 

**Adatfüggő útválasztás**: A funkció, amely lehetővé teszi, hogy az alkalmazás egy adott szegmenshez kapcsolódjon egy adott szegmenshez. Tekintse meg [az Adatfüggő útválasztást](sql-database-elastic-scale-data-dependent-routing.md). Összehasonlítás **[több szegmenses lekérdezéssel](sql-database-elastic-scale-multishard-querying.md)** .

**Globális**szegmenses Térkép: A horizontális Felskálázási kulcsok és a hozzájuk tartozó szegmensek közötti **Térkép.** A globális szegmenses térképet a rendszer a szegmens **map Managerben**tárolja. Összehasonlítás a **helyi**szegmenses térképsel.

Szegmens **Térkép listázása**: Egy szegmenses Térkép, amelyben a horizontális Felskálázási kulcsok külön vannak leképezve. Összehasonlítás a **tartományhoz**tartozó felosztási térképsel.   

**Helyi**szegmenses Térkép: A szegmensen tárolt helyi szegmens Térkép a szegmensen található shardletek leképezéseit tartalmazza.

**Több szegmensből álló lekérdezés**: Több szegmensre vonatkozó lekérdezések kibocsátásának lehetősége; az eredmények készleteit a UNION összes szemantika (más néven "kipróbálható lekérdezés") használatával kapjuk vissza. Összehasonlítás az **Adatfüggő útválasztással**.

**Több-** bérlősés egybérlős: Ez egy egybérlős adatbázist és egy több-bérlős adatbázist mutat be:

![Egy-és több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Itt láthatja a több -bérlős adatbázisok szétválasztását. 

![Egy-és több-bérlős adatbázisok](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Tartomány szegmensének leképezése**: Egy szegmenses Térkép, amelyben a szegmens eloszlási stratégia több összefüggő értéken alapul. 

**Hivatkozási táblázatok**: Azokat a táblákat, amelyek nincsenek szétszórva, de több szegmens között replikálódnak. A zip-kódok például egy hivatkozási táblában tárolhatók. 

Szegmens: Egy olyan Azure SQL Database-adatbázis, amely egy szegmensben tárolt adatkészletből tárolja az adatait. 

**Szilánk rugalmassága**: **Horizontális skálázás** és **vertikális skálázás**is elvégezhető.

**Szilánkokra osztott táblázatok**: A felosztott táblázatok, azaz az adatok elosztása a szegmensek között a horizontális Felskálázási kulcs értékei alapján. 

Horizontális Felskálázási **kulcs**: Egy oszlop értéke, amely meghatározza, hogy az adatszegmensek hogyan oszlanak el egymás között. Az érték típusa a következők egyike lehet: **int**, **bigint**, **varbinary**vagy **uniqueidentifier**. 

Szegmens **készlet**: Szegmensek gyűjteménye, amely ugyanahhoz a szegmenses térképhez van hozzárendelve a szegmenses Térkép kezelőjében.  

**Shardletbe**: Egy szegmensen lévő horizontális Felskálázási kulcs egyetlen értékével társított összes adatmennyiség. A shardletbe az adatáthelyezés legkisebb egysége, amely a felosztott táblák újraterjesztésekor lehetséges. 

Szegmenses **Térkép**: A horizontális Felskálázási kulcsok és a hozzájuk tartozó szegmensek közötti leképezések halmaza.

Szegmenses **Térkép kezelője**: Olyan felügyeleti objektum és adattár, amely egy vagy több szegmenshez tartozó szegmensi leképezést (ka) t, szegmens helyet és leképezéseket tartalmaz.

![Leképezések][2]

## <a name="verbs"></a>Igék
**Horizontális skálázás**: Az alábbi ábrán látható szegmensek kibővítésével (vagy a-ben) a szegmensek összevonásával (vagy a-ben) való kibontásával.

![Horizontális és vertikális skálázás][3]

**Egyesítés**: A shardletek két szegmensből egy szegmensbe való áthelyezése, és ennek megfelelően frissíti a szegmenses térképet.

**Shardletbe áthelyezése**: Egyetlen shardletbe áthelyezése egy másik szegmensbe. 

Szegmens: Horizontálisan particionálja az azonos módon strukturált adategységeket több adatbázis között egy vízszintesen elválasztó kulcs alapján.

**Felosztás**: Több shardletek áthelyezése az egyik szegmensből egy másikba (jellemzően új) szegmensbe. A horizontális Felskálázási kulcsot a felhasználó a felosztott pontként kapja meg.

**Vertikális skálázás**: Az egyes szegmensek számítási méretének méretezése (vagy le). Például ha a standard és a prémium közötti szegmenst szeretné módosítani (ami több számítási erőforrást eredményez). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

