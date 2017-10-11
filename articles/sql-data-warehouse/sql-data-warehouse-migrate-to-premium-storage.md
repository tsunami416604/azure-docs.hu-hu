---
title: "Telepítse át a meglévő Azure adatraktár prémium szintű Storage |} Microsoft Docs"
description: "Prémium szintű storage egy meglévő data warehouse-ba történő áttelepítéséhez"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 860e50b532b4b0a21d3be54f087730070b0e56bb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Prémium szintű Storage az adatraktár áttelepítése
Új Azure SQL Data Warehouse [prémium szintű storage, a nagyobb teljesítmény kiszámíthatóságot][premium storage for greater performance predictability]. Standard szintű tárolót a jelenleg létező adatraktárak prémium szintű Storage most telepíthető át. Kihasználhatja az automatikus áttelepítése, vagy ha jobban szeret szabályozhatja, mikor kell áttelepíteni (amely does tartalmaz, amely bizonyos időre leállítást), teheti az áttelepítés saját maga.

Ha egynél több adatraktár, használja a [automatikus áttelepítése ütemezés] [ automatic migration schedule] meghatározni, ha azt is áttelepíthetők.

## <a name="determine-storage-type"></a>Tárolási típus meghatározása
Ha egy adatraktár hozott létre a következő dátum előtt, Standard szintű storage jelenleg használt.

| **Régió** | **Ez a dátum előtt létrehozott adatraktár** |
|:--- |:--- |
| Kelet-Ausztrália |Prémium szintű storage még nem érhető el |
| Kelet-Kína |2016. november 1. |
| Észak-Kína |2016. november 1. |
| Közép-Németország |2016. november 1. |
| Északkelet-Németország |2016. november 1. |
| Nyugat-India |Prémium szintű storage még nem érhető el |
| Nyugat-Japán |Prémium szintű storage még nem érhető el |
| USA északi középső régiója |2016. november 10. |

## <a name="automatic-migration-details"></a>Automatikus áttelepítése részletei
Alapértelmezés szerint kiforrottabb lesz az adatbázis az Ön közötti 18:00:00 és 6:00-kor a régió helyi idő alatt a [automatikus áttelepítése ütemezés][automatic migration schedule]. A meglévő adatraktár használhatatlan lesz, az áttelepítés során. Az áttelepítés adatraktár / / terabájt tárhelyet körülbelül egy óra igénybe vehet. Nem kell fizetnie az automatikus áttelepítése bármely részében.

> [!NOTE]
> Ha az áttelepítés befejeződött, az adatraktár lesz ismét online állapotú, és használható.
>
>

A Microsoft az alábbi lépéseket az áttelepítéshez (ezek nem igényelnek beavatkozást bármely beavatkozás) tart. Ebben a példában képzelhető el, hogy a meglévő adatraktár szabványos tárolón jelenleg neve "MyDW."

1. Microsoft átnevezi "MyDW" "[időbélyeg] MyDW_DO_NOT_USE_."
2. A Microsoft leállítja "[időbélyeg] MyDW_DO_NOT_USE_." Ebben az időszakban a biztonsági másolat használatban van. Ha azt a folyamat során problémába ütközik jelenhet meg több szüneteltetése és folytatása.
3. Microsoft hoz létre egy új data warehouse "MyDW" nevű a prémium szintű storage, a 2. lépésben készült biztonsági másolatból. "MyDW" csak kerülnek bele a visszaállítás befejezése után.
4. A visszaállítás befejezése után, "MyDW" arkusz ugyanazon adattárházegységek és állapota (felfüggesztett aktív), hogy az áttelepítés előtt volt.
5. Az áttelepítés befejezése után, a Microsoft törlése "MyDW_DO_NOT_USE_ [időbélyeg]".

> [!NOTE]
> A következő beállítások nem jelenik meg az áttelepítés részeként:
>
> * Az adatbázis szintjén naplózás kell ismét engedélyezni kell.
> * Az adatbázis szintjén tűzfalszabályok újból hozzá kell. A kiszolgáló szintjén tűzfalszabályok nem érintettek.
>
>

### <a name="automatic-migration-schedule"></a>Automatikus áttelepítése ütemezése
Automatikus áttelepítések 18:00:00 és 6:00-kor (helyi idő régiónként) a következő kimaradás ütemezése során kerül sor.

| **Régió** | **Becsült kezdő dátuma** | **Becsült befejezési dátum** |
|:--- |:--- |:--- |
| Kelet-Ausztrália |Még nem határozza meg |Még nem határozza meg |
| Kelet-Kína |2017. január 9. |2017. január 13. |
| Észak-Kína |2017. január 9. |2017. január 13. |
| Közép-Németország |2017. január 9. |2017. január 13. |
| Északkelet-Németország |2017. január 9. |2017. január 13. |
| Nyugat-India |Még nem határozza meg |Még nem határozza meg |
| Nyugat-Japán |Még nem határozza meg |Még nem határozza meg |
| USA északi középső régiója |2017. január 9. |2017. január 13. |

## <a name="self-migration-to-premium-storage"></a>Prémium szintű Storage önálló áttelepítés
Ha szabályozni szeretné a leállás esetén a rendszer, az alábbi lépések segítségével egy meglévő adatraktár szabványos tároló áttelepítése a prémium szintű Storage. Ha ezt a lehetőséget választja, akkor előtt végezze el az önálló áttelepítés automatikus áttelepítésének megkezdése az adott régióban. Ez biztosítja, hogy az automatikus áttelepítés ütközést okoz veszélyének elkerülése érdekében (tekintse meg a [automatikus áttelepítése ütemezés][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Önálló áttelepítési utasítások
Az adatraktár áttelepíteni, saját magának, a biztonsági másolat, és állítsa vissza a szolgáltatások. A helyreállítási rész az áttelepítés várható / terabájt tárhelyet körülbelül egy óra érvénybe / data warehouse-bA. Ha meg szeretné tartani a néven után az áttelepítés akkor fejeződött be, kövesse a [áttelepítéskor átnevezésére szolgáló lépéseket][steps to rename during migration].

1. [Felfüggesztés] [ Pause] az adatraktár. Ehhez szükséges, az automatikus biztonsági mentés.
2. [Visszaállítás] [ Restore] a legutóbbi pillanatképből.
3. Törölje a meglévő adatraktár szabványos tárolón. **Ha ezt a lépést nem sikerül, akkor mindkét adatraktárak fogjuk számlázni.**

> [!NOTE]
> A következő beállítások nem jelenik meg az áttelepítés részeként:
>
> * Az adatbázis szintjén naplózás kell ismét engedélyezni kell.
> * Az adatbázis szintjén tűzfalszabályok újból hozzá kell. A kiszolgáló szintjén tűzfalszabályok nem érintettek.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Adatraktár átnevezése (választható) az áttelepítés során
Két adatbázis ugyanazon a logikai kiszolgálón azonos neve nem lehet. Az SQL Data Warehouse mostantól támogatja a adatraktár átnevezése.

Ebben a példában képzelhető el, hogy a meglévő adatraktár szabványos tárolón jelenleg neve "MyDW."

1. Nevezze át a "MyDW" a következő ALTER DATABASE parancs használatával. (A példában azt kell nevezni "MyDW_BeforeMigration.")  Ez a parancs minden meglévő tranzakciókat, és a sikeres a master adatbázisban kell végrehajtani.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Felfüggesztés] [ Pause] "MyDW_BeforeMigration." Ehhez szükséges, az automatikus biztonsági mentés.
3. [Visszaállítás] [ Restore] a legutóbbi a pillanatkép készítése a szokásosnál (például "MyDW") nevű új adatbázis.
4. Törölje a "MyDW_BeforeMigration." **Ha ezt a lépést nem sikerül, akkor mindkét adatraktárak fogjuk számlázni.**


## <a name="next-steps"></a>Következő lépések
Prémium szintű Storage módosítását akkor is növekvő számának a blob-adatbázisfájlok a mögöttes architektúráját, valamint az adatraktár. Ez a változás teljesítménybeli előnyökben maximalizálása érdekében a fürtözött oszlopcentrikus indexek újraépítése az alábbi parancsfájl használatával. A parancsfájl használatára, a további tárolókban kényszeríti a meglévő adatok egy részét. Ha nem tesz semmit, az adatok fog természetes terjesztenie adott idő alatt, több adatot tölt be a táblába.

**Előfeltételek:**

- Az adatraktár futtasson-e 1000 adattárházegységek vagy annál újabb (lásd: [méretezési számítási teljesítményt][scale compute power]).
- A parancsfájl végrehajtása felhasználó kell lennie a [mediumrc szerepkör] [ mediumrc role] vagy újabb verzióját. Felhasználó hozzáadása ehhez a szerepkörhöz, hajtsa végre a következő:````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create table to control index rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute index rebuilds. If script fails, the below can be re-run to restart where last left off.
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Clean up table created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Ha problémába ütközik az adatraktárban [hozzon létre egy támogatási jegy] [ create a support ticket] és a hivatkozás "áttelepítés prémium szintű Storage" lehetséges okozta.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
