---
title: "T-SQL-nézetek használata az Azure SQL Data Warehouse |} Microsoft Docs"
description: "Tippek a Transact-SQL-nézetek használata az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: d2a03be810bd7f792876607ec735eb578b65a3b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="views-in-sql-data-warehouse"></a>Az SQL Data Warehouse-nézetek
Nézetek különösen hasznosak az SQL Data Warehouse. A számos különböző módon a megoldás minőségének használható.  Ez a cikk funkciógazdagabbá teheti a megoldás a nézetek, valamint a korlátozásokat, amelyeket figyelembe kell venni néhány példák mutatja be.

> [!NOTE]
> A szintaxis `CREATE VIEW` nem ez a cikk tárgyalja. Tekintse meg a [NÉZET létrehozása] [ CREATE VIEW] cikket az MSDN-en a referencia jellegű információihoz.
> 
> 

## <a name="architectural-abstraction"></a>Az architektúra absztrakciós
Egy nagyon gyakori alkalmazásminta újra létre kell hoznia táblák létrehozása tábla AS kiválasztása (CTAS) egy objektum átnevezése mintát, miközben az adatok betöltése után.

Az alábbi példa új dátum rekordok hozzáadása a dátum dimenzió. Vegye figyelembe, hogyan egy új tabble DimDate_New, először hozzák létre, és lecseréli az eredeti verzióját tartalmazza a majd átnevezték.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Ez a módszer azonban táblák jelenik meg, és egy felhasználó, valamint a "tábla nem létezik" hibaüzenet eltűnik eredményez. Nézetek segítségével biztosít a felhasználók egy egységes megjelenítési réteg az alapul szolgáló objektumok váltják fel. Azáltal, hogy a felhasználók férhetnek hozzá az adatokhoz, a nézetek, azt jelenti, hogy a felhasználók nem látják az alapul szolgáló tábla kell. Ez egységes felhasználói élményt nyújt, győződjön meg arról, hogy az adatraktár tervezők is az adatokat az adatmodellbe fejlődnek és teljesítmény maximalizálása CTAS használatával az adatok betöltése a folyamat során.    

## <a name="performance-optimization"></a>Teljesítményoptimalizálás
Nézetek is kényszeríteni optimalizált teljesítményt illesztést a táblák között állítható be. Például egy nézet építhessék be a redundáns terjesztési kulcs adatmozgás minimalizálása érdekében a csatlakozó feltétel részeként.  Egy másik előnye, hogy egy nézet egy adott lekérdezés vagy csatlakozó mutató kényszerített lehet. Nézetek használata ezen a módon kikapcsolja garantálja, hogy összekapcsolások mindig megtörténik a felhasználóknak jegyezze meg a megfelelő konstruktor a táblákra elkerülése optimális módon.

## <a name="limitations"></a>Korlátozások
Az SQL Data Warehouse nézetek metaadatok csak olyan.  Ennek következtében a következő beállítások nem érhetők el:

* Nincs kötés séma beállítás
* Alaptáblát keresztül a nézet nem frissíthető
* Nézetek nem hozható létre az ideiglenes táblák
* Nem támogatott a KIBONTÁS / NOEXPAND mutató
* Nincsenek az SQL Data Warehouse nem indexelt nézetek

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].
A `CREATE VIEW` tekintse meg szintaxis [NÉZET létrehozása][CREATE VIEW].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
