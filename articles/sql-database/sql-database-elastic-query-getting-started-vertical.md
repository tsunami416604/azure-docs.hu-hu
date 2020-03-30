---
title: Első lépések az adatbázisközi lekérdezésekkel
description: rugalmas adatbázis-lekérdezés használata vertikálisan particionált adatbázisokkal
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: af93035766eaf1afa12d124b8379ee55c5567260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823799"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Első lépések az adatbázisközi lekérdezésekkel (függőleges particionálás) (előzetes verzió)

Rugalmas adatbázis-lekérdezés (előzetes verzió) az Azure SQL Database lehetővé teszi, hogy több adatbázist egy kapcsolati pont használatával futó T-SQL-lekérdezések futtatásához. Ez a cikk [vertikálisan particionált adatbázisokra](sql-database-elastic-query-vertical-partitioning.md)vonatkozik.  

Ha elkészült, megtudhatja, hogyan konfigurálhatja és használhatja az Azure SQL-adatbázist több kapcsolódó adatbázisra kiterjedő lekérdezések végrehajtására.

A rugalmas adatbázis-lekérdezési funkcióról az [Azure SQL Database rugalmas adatbázis-lekérdezés – áttekintés című témakörben olvashat bővebben.](sql-database-elastic-query-overview.md)

## <a name="prerequisites"></a>Előfeltételek

Módosítja a külső adatforrás-engedélyt. Ez az engedély az ALTER DATABASE engedély része. Alter Bármely külső adatforrás-engedély szükséges az alapul szolgáló adatforrásra való hivatkozáshoz.

## <a name="create-the-sample-databases"></a>Mintaadatbázisok létrehozása

Először hozzon létre két adatbázist, **az Ügyfelek** és **a Rendelések**adatbázist, vagy azonos vagy különböző SQL Database-kiszolgálókon.

Hajtsa végre a következő lekérdezéseket a **Rendelések** adatbázisban az **OrderInformation** tábla létrehozásához és a mintaadatok beviteléhez.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Most hajtsa végre a következő **lekérdezést** a Vevők adatbázisban a **CustomerInformation** tábla létrehozásához és a mintaadatok beviteléhez.

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

### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis hatókörrel tartozó főkulcsa és hitelesítő adatai

1. Nyissa meg az SQL Server Management Studio vagy az SQL Server Data Tools alkalmazást a Visual Studióban.
2. Csatlakozzon a Rendelések adatbázishoz, és hajtsa végre a következő T-SQL parancsokat:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    A "felhasználónév" és a "jelszó" az Ügyfelek adatbázisába való bejelentkezéshez használt felhasználónév és jelszó.
    Az Azure Active Directory rugalmas lekérdezésekkel történő hitelesítése jelenleg nem támogatott.

### <a name="external-data-sources"></a>Külső adatforrások

Külső adatforrás létrehozásához hajtsa végre a következő parancsot a Rendelések adatbázisban:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Külső táblák

Hozzon létre egy külső táblát a Rendelések adatbázisban, amely megfelel a CustomerInformation tábla definíciójának:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Rugalmas adatbázis-minta végrehajtása T-SQL lekérdezés

Miután definiálta a külső adatforrást és a külső táblákat, most már használhatja a T-SQL-t a külső táblák lekérdezéséhez. A lekérdezés végrehajtása a Rendelések adatbázisban:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Költségek

Jelenleg a rugalmas adatbázis-lekérdezési funkció szerepel az Azure SQL-adatbázis költsége.  

Az árképzésről az [SQL Database Díjszabása](https://azure.microsoft.com/pricing/details/sql-database)című témakörben talál.

## <a name="next-steps"></a>További lépések

* A rugalmas lekérdezés áttekintését a [Rugalmas lekérdezés áttekintése című témakörben találja.](sql-database-elastic-query-overview.md)
* A vertikálisan particionált adatok szintaxisát és mintalekérdezéseit a [Vertikálisan particionált adatok lekérdezése) témakörben találja.](sql-database-elastic-query-vertical-partitioning.md)
* Horizontális particionálási (horizontális) oktatóanyag: [A rugalmas lekérdezés horizontális particionálás (horizontális sáfálás) című témakörben található.](sql-database-elastic-query-getting-started.md)
* A vízszintesen particionált adatok szintaxisát és mintalekérdezéseit [lásd: Horizontálisan particionált adatok lekérdezése)](sql-database-elastic-query-horizontal-partitioning.md)
* [Lásd:\_ \_sp távoli végrehajtása](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL utasításegyetlen távoli Azure SQL-adatbázis vagy egy horizontális particionálási séma szegmensként szolgáló adatbázisok készlete.
