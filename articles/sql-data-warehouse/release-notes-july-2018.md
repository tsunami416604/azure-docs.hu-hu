---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések a 2018 július |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: e63dd0a6feaedf95bb4845a3c5eded89e6585e36
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463497"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Mi az új Azure SQL Data warehouse? 2018. július
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és a július 2018-ban bevezetett módosításokat.

## <a name="lightning-fast-query-performance"></a>Villámgyors kibővítése a lekérdezési teljesítmény
[Az Azure SQL Data Warehouse](https://aka.ms/sqldw) állítja be, amely javítja a shuffle operations azonnali adatelérési bevezetésével új teljesítményteszteket. Azonnali adatelérési az adatátviteli műveletek csökkenti az SQL Server műveletekhez natív közvetlen SQL Server használatával. A SQL Server-motorral, közvetlenül az adatok áthelyezése az integráció azt jelenti, hogy az SQL Data Warehouse mostantól **67 %-kal gyorsabb, mint az Amazon Redshift** számítási feladatok használata a jól ismert iparági szabványnak számító származó [TPC H (TPC-H) benchmark™](http://www.tpc.org/tpch/).

![Az Azure SQL Data Warehouse a gyorsabb és olcsóbb, mint az Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>forrás: [Gigaom kutatási elemzői jelentés: A felhő teljesítményteszt Data warehouse-bA](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Modul teljesítményének, túl a [Gigaom kutatási](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) jelentést is mérni a USD költséget adott számítási feladatok számlálása ár-teljesítmény aránya. Az SQL Data Warehouse volt **legalább 23 %-kal olcsóbb** , mint a Redshift 30 TB számítási feladatokhoz. Rugalmasan méretezheti a számítási erőforrásokat, valamint szüneteltetése és folytatása a számítási feladatok SQL Data Warehouse lehetővé teszi, az ügyfeleknek kell fizetniük csak akkor, ha van használatban, további csökkenő költségeiket.
![Az Azure SQL Data Warehouse a gyorsabb és olcsóbb, mint az Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>forrás: [Gigaom kutatási elemzői jelentés: A felhő teljesítményteszt Data warehouse-bA](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>Lekérdezés egyidejűség
Az SQL Data warehouse-ba is biztosítja, hogy adatok elérhetők a szervezetek között. A Microsoft támogató 128 lekérdezést, hogy több felhasználó lekérdezheti az adatbázist, és nem küldött egyéb kérések által blokkolt továbbfejlesztett. Ezzel szemben az Amazon Redshift korlátozza a maximális párhuzamos lekérdezések 50, korlátozza az adatok elérését a szervezeten belül.

SQL Data Warehouse függetlenített tárolási és számítási az egyedi architektúrája ezek lekérdezési teljesítmény és a lekérdezés egyidejűségi nyereség bármely ár növekedés és az épület nélkül kínál.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Kifinomultabb részletességgel több régióban és a kiszolgáló visszaállítása
Most már visszaállíthatja régiók és földrajzi redundáns biztonsági másolatokat, amelyeket a rendszer 24 óránként kiválasztása helyett bármely visszaállítási pont használó kiszolgálók között. Több régióban és a kiszolgáló visszaállítása mindkét felhasználó által vagy automatikus visszaállítási pontok további adatvédelemre kifinomultabb részletességgel engedélyezése esetén támogatottak. Az elérhető a további helyreállítási pontok biztos lehet abban is, hogy az adattárház logikailag konzisztens lesz régióban visszaállításakor.

![A restore parancs – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![visszaállítási lehetőségek – Azure SQL Data warehouse-bA](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

További információkért lásd: a [gyorsított és rugalmas visszaállítási pontok](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogbejegyzést.

## <a name="20-minute-restorations"></a>20 perces visszaállítását
Most kínál az SQL Data Warehouse, visszaállítás bármely adatraktár **kevesebb mint 20 perc alatt** ugyanabban a régióban, az adatbázis méretétől függetlenül. Rövidebb idő alatt vonatkozik-e a visszaállítást, de akár egy másik logikai kiszolgáló ugyanazon a régión belül. Emellett a pillanatkép-folyamat lett továbbfejlesztve, hogy kevesebb időt vesz igénybe egy visszaállítási pont létrehozása. Az alsó teljesítményszintek (alsó DWU-beállítást), a javítására van egy *2 x csökkentési* időpontot a pillanatkép létrehozása.

További információkért lásd: a [gyorsított és rugalmas visszaállítási pontok](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogbejegyzést.

## <a name="custom-restoration-configurations"></a>Egyéni helyreállítás konfigurációk
Most módosíthatja a teljesítményszint (dwu Használatát), az Azure Portalon visszaállításakor. Egy frissített Gen2 data warehouse-ba is helyreállíthatja. Általános 2 példányra visszaállítással most kiértékelheti előtt Gen2 hatásának [az Gen1 data warehouse frissítése](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Egyéni helyreállítás konfigurálása – az Azure SQL Data warehouse-bA](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
A [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) tárolt eljárás gyakran eszközök beszerzésére használatos Transact-SQL kötegben szereplő paraméterek metaadatait. Az eljárást minden paraméter egy sort a batch-a ítélte típussal kapcsolatos információk arra a paraméterre vonatkozóan adja vissza. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Az eredményhalmaz kapcsolatos metaadatokat tartalmaz a `@id` paraméter (részleges eredményeket látható)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
A [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) tárolt eljárás frissíti az adatbázis-objektum metaadatait, ha az alapul szolgáló metaadatokat vált az alapul szolgáló objektum változásai miatt elavult. Ez akkor fordulhat elő, ha egy nézet alaptáblát változnak, és a nézet még nem lett újra létre kell hozni. Ez menti, elvetését, majd újra létre kellene hoznia a függőségi viszonyban lévő objektumok lépését.

Az alábbi példa elavulhatnak alapul szolgáló tábla változása miatt a nézet jeleníti meg. Láthatja, hogy az adatok helyesek-e az első oszlop változás (1-Mollie), de az oszlop neve érvénytelen, és a második oszlopban nem szerepel. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data warehouse, megtudhatja, hogyan lehet gyorsan [hozzon létre egy SQL Data Warehouse][create a SQL Data Warehouse]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
