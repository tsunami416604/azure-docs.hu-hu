---
title: Adatbázisközi lekérdezések (vertikális partíciók) – első lépések |} A Microsoft Docs
description: függőlegesen particionált adatbázisok rugalmas adatbázis-lekérdezés használata
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 07/03/2018
ms.openlocfilehash: e665e00c9f769f4ee1f6e468ee6a03ee28271d5b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239782"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Adatbázisközi lekérdezések (vertikális partíciók) használatának első lépései (előzetes verzió)

Elastic database-lekérdezés (előzetes verzió) az Azure SQL Database lehetővé teszi, hogy több adatbázis egyetlen kapcsolódási pontot használ a T-SQL lekérdezések futtatását. Ez a cikk vonatkozik [függőlegesen particionált adatbázisok](sql-database-elastic-query-vertical-partitioning.md).  

Befejezésekor a rendszer: ismerje meg, hogyan konfigurálhatja és használhatja az Azure SQL Database több kapcsolódó adatbázisra kiterjedő lekérdezések végrehajtásához.

A rugalmas adatbázis-lekérdezés szolgáltatással kapcsolatos további információkért lásd: [Azure SQL Database rugalmas adatbázis-lekérdezések áttekintése](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Az ALTER ANY külső ADATFORRÁS-engedély is szükséges. Ez az engedély megtalálható az ALTER DATABASE engedéllyel. Az ALTER ANY EXTERNAL DATA SOURCE engedélyekre van szükség, tekintse meg az alapul szolgáló adatforrás.

## <a name="create-the-sample-databases"></a>A minta-adatbázisok létrehozása

Első lépésként hozzon létre két adatbázis **ügyfelek** és **rendelések**, vagy az azonos vagy eltérő logikai kiszolgálókon.

A következő lekérdezéseket hajt végre a **rendelések** hozhat létre adatbázist a **OrderInformation** táblát, és adjon meg a mintaadatokat.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Most, hajtsa végre a következő lekérdezést a a **ügyfelek** hozhat létre adatbázist a **CustomerInformation** táblát, és adjon meg a mintaadatokat.

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

### <a name="database-scoped-master-key-and-credentials"></a>Adatbázishoz kötődő főkulcs és a hitelesítő adatok

1. Nyissa meg az SQL Server Management Studio vagy SQL Server Data Tools a Visual Studióban.
2. Csatlakozzon a rendelési adatbázisba, és hajtsa végre a következő T-SQL-parancsokat:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    A "username" és "password" kell lennie a felhasználónevet és az ügyfelek adatbázisba történő bejelentkezéshez használt jelszó.
    Hitelesítés Azure Active Directory az elastic Database-lekérdezések jelenleg nem támogatott.

### <a name="external-data-sources"></a>Külső adatforrások

Hozzon létre egy külső adatforrást, hajtsa végre a következő parancsot a rendelési adatbázisban található:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Külső táblák

A rendelési adatbázisban, amely megfelel a CustomerInformation tábla definícióját a külső tábla létrehozása:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Egy mintául szolgáló elastic database-T-SQL-lekérdezés végrehajtása

Miután meghatározta a külső adatforrásban és a külső táblák, a külső táblák lekérdezése mostantól használhatja a T-SQL. Hajtsa végre a lekérdezést a rendelési adatbázisban található:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Költségek

A rugalmas adatbázis-lekérdezés funkció jelenleg be az Azure SQL Database költségét tartalmazza.  

Díjszabási információkért tekintse meg a [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>További lépések

* Rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés – áttekintés](sql-database-elastic-query-overview.md).
* Függőlegesen particionált adatok szintaxis és a minta lekérdezéseket, lásd: [lekérdezése függőlegesen particionált adatok)](sql-database-elastic-query-vertical-partitioning.md)
* Horizontális particionálást (sharding) foglalkozó oktatóanyagért lásd: [rugalmas lekérdezés horizontális particionálást (sharding) – első lépések](sql-database-elastic-query-getting-started.md).
* A szintaxist és a minta lekérdezésekhez vízszintesen particionált adatok, lásd: [lekérdezése vízszintesen particionált adatok)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy távoli Azure SQL Database vagy adatbázisok horizontális particionálási séma kidolgozásához szegmensek szolgáló készletét.
