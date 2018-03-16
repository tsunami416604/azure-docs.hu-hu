---
title: "Azure SQL-adatbázisok kezelése |} Microsoft Docs"
description: "Végezze el a létrehozását és kezelését egy rugalmas feladat."
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 77204e167d298ad77b2786b5d0a3dab1b557ccfc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Létrehozásához és kezeléséhez méretezett kimenő Azure SQL Database-adatbázisok rugalmas feladat (előzetes verzió)


**Rugalmas adatbázis-feladatok** egyszerűbbé teheti az adatbázisok csoportok kezelését a következő felügyeleti műveletek, például a sémamódosítások, a hitelesítő adatok kezelése, a hivatkozás frissítéseket, a teljesítményadat-gyűjtés vagy a bérlő (ügyfél) telemetriai futtatásával gyűjtemény. Rugalmas adatbázis-feladatok érhető el jelenleg az Azure portál és a PowerShell-parancsmagok használatával. Azonban az Azure portál felületek csökkenteni az összes adatbázis között a végrehajtás során korlátozott funkciók egy [rugalmas készlet (előzetes verzió)](sql-database-elastic-pool.md). Keresztül férhetnek hozzá a további funkciók és a parancsprogramok végrehajtását egyénileg definiált gyűjteményét, illetve a shard például adatbázisok csoportja beállítása (használatával létrehozott [Elastic Database ügyféloldali kódtárának](sql-database-elastic-scale-introduction.md)), lásd: [létrehozása és kezelése a PowerShell használatával feladatok](sql-database-elastic-jobs-powershell.md). További információ a feladatok: [rugalmas adatbázis-feladatok áttekintése](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ingyenes próbaverzió, lásd: [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* Egy rugalmas készlet. Lásd: [kapcsolatos rugalmas készletek](sql-database-elastic-pool.md).
* Rugalmas feladat szolgáltatás-összetevők telepítését. Lásd: [a rugalmas adatbázis feladat szolgáltatás telepítése](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Feladat létrehozása
1. Használja a [Azure-portálon](https://portal.azure.com), a meglévő rugalmas feladat adatbáziskészlet, kattintson az **létrehozása feladat**.
2. Írja be a felhasználónevét és jelszavát (létre telepítési feladatok) adatbázis-rendszergazda a feladatok vezérlő adatbázis (metaadat-tároló-feladatok).
   
    ![A feladat neve, írja be vagy illessze be a kódját, majd kattintson a Futtatás][1]
3. Az a **feladat létrehozása** panelen adja meg a feladat nevét.
4. Írja be a felhasználónevet és jelszót a céladatbázisokhoz parancsfájl végrehajtása sikeres végrehajtásához szükséges engedélyekkel.
5. Illessze be, vagy írja be a T-SQL-parancsfájlt.
6. Kattintson a **mentése** majd **futtatása**.
   
    ![Feladatok létrehozása és futtatása][5]

## <a name="run-idempotent-jobs"></a>Az idempotent feladatok futtatása
Egy parancsfájl-adatbázisok való összevetéssel futtatásakor meg arról, hogy a parancsfájl az idempotent kell lennie. Ez azt jelenti, hogy a parancsfájl kell lesz futtatható többször, még akkor is, ha nem tudott előtt állapotban. Például, ha a parancsfájl futása sikertelen, a feladat automatikusan megpróbálja replikációig (határértékek közé essen, az újra gombra, logika végül megszűnik az újrapróbálkozás). Ennek módja a egy "IF létezik-e" záradék és az új objektum létrehozása előtt törölje annak példányban sem található. Példa itt:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Másik megoldásként használhatja az "IF nem létezik" záradék előtt egy új példányának létrehozásakor:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Ez a parancsfájl frissíti a korábban létrehozott táblát.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Feladat állapotának ellenőrzése
Miután egy feladat elindult, akkor is ellenőrizhesse a telepítés előrehaladását.

1. A rugalmas készlet lapon kattintson **feladatok kezelése**.
   
    ![Kattintson a "Kezelése feladatok"][2]
2. Kattintson a nevére (a) a feladatok. A **állapot** "Kész" vagy "Sikertelen". A feladat részletei (b) a dátum és idő létrehozása és futtatása jelennek meg. A lista (c) alatt a végrehajtási állapotát tartalmazza a parancsfájl minden adatbázison a tárolókészletben, a dátum és idő adatait.
   
    ![A befejezett feladatok ellenőrzése][3]

## <a name="checking-failed-jobs"></a>Feladatok ellenőrzése nem sikerült.
Ha egy feladat sikertelen lesz, a napló, a futtatása is található. Kattintson a nevére, a sikertelen feladat a részletek megtekintéséhez.

![Ellenőrizze a sikertelen feladat adatait][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


