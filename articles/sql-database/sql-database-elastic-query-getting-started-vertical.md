---
title: Ismerkedés a kereszt-adatbázis-lekérdezések (a vertikális particionálás) |} Microsoft Docs
description: függőleges particionált adatbázisok rugalmas adatbázis-lekérdezés használata
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c7bf6816b457f7e193f53336c48f5e205722067e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Első lépések (a vertikális particionálás) közötti adatbázis-lekérdezések (előzetes verzió)
A rugalmas adatbázis-lekérdezés (előzetes verzió) Azure SQL-adatbázis, amelyek több adatbázis egyetlen kapcsolódási pontot használ több T-SQL-lekérdezések futtatása teszi lehetővé. Ez a témakör vonatkozik [függőleges particionálva az adatbázisok](sql-database-elastic-query-vertical-partitioning.md).  

Befejezése után lesz: megtudhatja, hogyan konfigurálhatja és használhatja az Azure SQL Database lekérdezések, amelyek több kapcsolódó adatbázis több végrehajtásához. 

A rugalmas adatbázis-lekérdezés szolgáltatással kapcsolatos további információkért lásd: [Azure SQL Database rugalmas adatbázis-lekérdezés áttekintése](sql-database-elastic-query-overview.md). 

## <a name="prerequisites"></a>Előfeltételek

Az ALTER ANY külső ADATFORRÁS engedéllyel kell rendelkeznie. Ez az engedély megtalálható az ALTER DATABASE engedéllyel. Az ALTER ANY külső ADATFORRÁS engedélyekre van szükség az alapul szolgáló adatforrásban hivatkozik.

## <a name="create-the-sample-databases"></a>A minta adatbázisok létrehozása
Először létre kell hoznunk két adatbázis **ügyfelek** és **rendelések**, vagy a ugyanazon vagy másik logikai kiszolgálókon.   

A következő lekérdezéseket hajt végre a **rendelések** adatbázis létrehozása a **OrderInformation** tábla, és adja meg a minta-adatokat. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Most, végrehajtani a következő lekérdezés a **ügyfelek** adatbázis létrehozása a **CustomerInformation** tábla, és adja meg a minta adatokat. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Adatbázis-objektumok létrehozása
### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis hatókörű főkulcs és a hitelesítő adatok
1. Nyissa meg az SQL Server Management Studio vagy Visual Studio SQL Server Data Tools összetevővel.
2. Csatlakozás a rendeléseket adatbázishoz, és a következő T-SQL-parancsok:
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    A "felhasználónév" és a "password" kell, a használt felhasználónév és jelszó használatát a bejelentkezéshez az ügyfelek adatbázisba.
    Hitelesítés az Azure Active Directoryval rugalmas lekérdezési jelenleg nem támogatott.

### <a name="external-data-sources"></a>Külső adatforrások
Hozzon létre egy külső adatforrásból, hajtható végre a következő parancsot a rendeléseket adatbázison: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Külső táblák
Létrehoz egy külső táblát a rendelések adatbázis, amely megfelel a CustomerInformation tábla definícióját:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Egy minta rugalmas adatbázis T-SQL-lekérdezés végrehajtása
A külső adatforrást és a külső táblák meghatározása után lekérdezni a külső táblák most már használhatja a T-SQL. Hajtsa végre a lekérdezést a rendelések adatbázison: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Költségek
A rugalmas adatbázis-lekérdezés szolgáltatása jelenleg szerepel az az Azure SQL-adatbázis költségeit.  

Díjszabási információkért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database). 

## <a name="next-steps"></a>További lépések

* Rugalmas lekérdezési áttekintését lásd: [rugalmas lekérdezési áttekintése](sql-database-elastic-query-overview.md).
* A szintaxis és a minta lekérdezések függőleges particionált adatok, lásd: [adatok lekérdezése függőleges particionálva)](sql-database-elastic-query-vertical-partitioning.md)
* Vízszintes particionálás (horizontális) oktatóanyagért lásd a [Ismerkedés a vízszintes particionálására (horizontális) rugalmas lekérdezési](sql-database-elastic-query-getting-started.md).
* A szintaxis és a minta lekérdezések vízszintesen particionált adatok, lásd: [adatok vízszintesen lekérdezése particionálva)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714) tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy egyetlen távoli Azure SQL Database vagy az adatbázisok egy vízszintes particionálási sémát a szilánkok szolgál.
