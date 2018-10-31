---
title: Csoportok az Azure SQL Database-adatbázisok kezelése |} A Microsoft Docs
description: Végig-rugalmas feladat létrehozása és kezelése.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: e036bb8b32ab81c63767d4a26fea103cf56b6a66
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242096"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Létrehozása és kezelése az Azure SQL-adatbázisok, rugalmas feladatok (előzetes verzió) használatával horizontálisan


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Elastic Database-feladatok** úgy, hogy felügyeleti műveleteket, mint a sémamódosítások, a hitelesítő adatok kezelése, a referenciaadatok frissítése, a teljesítményadat-gyűjtés vagy a bérlő (ügyfél) telemetriai végrehajtja az adatbázisok csoportok kezelésének egyszerűsítéséhez gyűjtemény. Elastic Database-feladatok az Azure portal és a PowerShell-parancsmagok jelenleg érhető el. Azonban az Azure portal felületek csökkenteni a funkciók között található összes adatbázis végrehajtási legfeljebb egy [rugalmas készlet](sql-database-elastic-pool.md). Keresztül férhetnek hozzá a további funkciók és parancsprogramok egyénileg definiált gyűjteménye vagy egy szegmens például adatbáziscsoportok beállítása (használatával létrehozott [Elastic Database-ügyfélkódtár](sql-database-elastic-scale-introduction.md)), lásd: [létrehozása és kezelése PowerShell-lel projektek](sql-database-elastic-jobs-powershell.md). -Feladatokkal kapcsolatos további információkért lásd: [Elastic Database-feladatok áttekintése](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Az ingyenes próbaverzióra, lásd: [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Rugalmas készlet. Lásd: [készletekkel kapcsolatos](sql-database-elastic-pool.md).
* A rugalmas feladat szolgáltatás-összetevők telepítése. Lásd: [a rugalmas feladatokat végző szolgáltatás telepítése](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Feladatok létrehozása
1. Használatával a [az Azure portal](https://portal.azure.com), a meglévő rugalmas feladat adatbáziskészlet, kattintson az **létrehozása feladat**.
2. Írja be a felhasználónevet és jelszót az adatbázis-rendszergazda (a feladatok a telepítés során létrehozott), a feladatok adatbázis vezérlése (metaadat-tároló-feladatok).
   
    ![Nevezze el a feladatot, írja be vagy illessze be a kódot, és kattintson a Futtatás][1]
3. Az a **feladat létrehozása** panelen írja be a feladat nevét.
4. Írja be a felhasználónevet és jelszót a céladatbázisokhoz parancsfájl végrehajtása sikeres végrehajtásához szükséges engedélyekkel.
5. Illessze be, vagy írja be a T-SQL parancsfájl.
6. Kattintson a **mentése** majd **futtatása**.
   
    ![Feladatok létrehozása és futtatása][5]

## <a name="run-idempotent-jobs"></a>Idempotens feladatok futtatása
Adatbázisok összevetéssel parancsfájl futtatásakor a arról, hogy a parancsfájl idempotensnek kell lennie. Azt jelenti a parancsfájl kell futtatható többször, még akkor is, ha nem tudott előtt hiányos állapotban. Például, ha a parancsfájl futása sikertelen, a feladat automatikusan megpróbálja replikációig (határokon belül, mint az újrapróbálkozási logika végül megszűnik az újrapróbálkozás). Ennek a legegyszerűbb módja a egy "IF létezik" záradékkal és a egy új objektum létrehozása előtt minden található példány törlése. Itt egy példa látható:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Másik megoldásként használhatja az "IF NOT EXISTS" záradék előtt hozzon létre egy új példányt:

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

Ez a szkript frissíti a korábban létrehozott táblát.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Feladat állapotának ellenőrzése
Miután a feladat elindult, a folyamat előrehaladását ellenőrzéséhez.

1. A rugalmas készlet oldalon kattintson **feladatok kezelése**.
   
    ![Kattintson a "Kezelés jobs"][2]
2. Kattintson a nevére (a) a feladat. A **állapot** lehet "Completed" vagy "Nem sikerült." A feladat részletei (b) a dátumot és időt létrehozását és futtatását a jelennek meg. Az alábbi listán (c) az, hogy a készlet jogosultságot ad a dátum és idő részletek a parancsfájl minden adatbázison a folyamatot mutatja meg.
   
    ![Egy befejezett feladat ellenőrzése][3]

## <a name="checking-failed-jobs"></a>Feladat ellenőrzése nem sikerült.
Ha egy feladat sikertelen, a végrehajtási napló tekintheti meg. Kattintson a nevére, a sikertelen feladatot, a részletek megtekintéséhez.

![Tekintse meg a sikertelen feladat][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


