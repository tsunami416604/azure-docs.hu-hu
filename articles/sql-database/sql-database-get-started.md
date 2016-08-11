<properties
    pageTitle="Oktatóanyag az SQL Database használatához: SQL-adatbázis létrehozása | Microsoft Azure"
    description="Útmutatónkból megtudhatja, hogyan kell SQL Database-alapú logikai kiszolgálót és a hozzá tartozó tűzfalszabályt beállítani, SQL-adatbázist létrehozni és mintaadatokat betölteni, valamint megtanulhatja az ügyféleszközökhöz való kapcsolódás, a felhasználók és az adatbázishoz tartozó tűzfalszabály konfigurálásának módját."
    keywords="sql database tutorial, create a sql database"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/14/2016"
    ms.author="carlrab"/>

# Oktatóanyag az SQL Database használatához: SQL-adatbázis létrehozása pillanatok alatt az Azure-portálon

**Önálló adatbázis**

> [AZURE.SELECTOR]
- [Azure-portál](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Oktatóanyagunkból elsajátíthatja, hogyan hozhatja létre a következőket az Azure-portálon:

- SQL Database-alapú logikai kiszolgáló SQL-adatbázisok üzemeltetéséhez
- SQL-adatbázis adatok nélkül, mintaadatokkal, vagy SQL-adatbázis biztonsági mentéséből származó adatokkal
- Kiszolgálószintű tűzfalszabály egyetlen IP-cím vagy IP-címek tartománya számára.

Ugyanezen feladatokat [C#](sql-database-get-started-csharp.md) nyelven vagy a [PowerShell](sql-database-get-started-powershell.md) segítségével is végrehajthatja.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Következő lépések
Miután áttanulmányozta az SQL Database oktatóanyagát, és létrehozott egy adatbázist mintaadatokkal, feltárhatja az adatokat a megszokott eszközökkel.

- Ha jártas a Transact-SQL és az SQL Server Management Studio használatában, olvassa el a [Connect and query a SQL database with SSMS](sql-database-connect-query-ssms.md) (SQL-adatbázishoz való kapcsolódás és lekérdezés az SSMS használatával) című cikket.

- Ha munkája során az Excelt használja, olvassa el a [Connect to SQL database with Excel](sql-database-connect-excel.md) (SQL-adatbázishoz való kapcsolódás az Excellel) című cikket.

- Ha készen áll a kódolás elkezdésére, válasszon programozási nyelvet a [Connection Libraries for SQL Database and SQL Server](sql-database-libraries.md) (Adatkapcsolattárak az SQL Database-hez az SQL Serverhez) című cikk alapján.

- Ha helyszíni SQL Server-adatbázisait át szeretné helyezni az Azure-ba, olvassa el a [Migrating a database to Azure SQL Database](sql-database-cloud-migrate.md) (Adatbázis migrálása az Azure SQL Database szolgáltatásba) című cikket.


## További információ

[Mi az SQL Database?](sql-database-technical-overview.md)




<!---HONumber=Jun16_HO2-->


