<properties
    pageTitle="Csatlakozás az SQL Database-hez – SQL Server Management Studio | Microsoft Azure"
    description="Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban az SQL Server Management Studio (SSMS) használatával. Ezt követően pedig mintalekérdezést futtathat a Transact-SQL (T-SQL) használatával."
    metaCanonical=""
    keywords="connect to sql database,sql server management studio"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sstein;carlrab" />

# Csatlakozás az SQL Database-hez az SQL Server Management Studio segítségével, és T-SQL-mintalekérdezés futtatása

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Az alábbi cikkből megtudhatja, hogyan csatlakozhat az SQL Server Management Studio (SSMS) aktuális verziójával egy Azure SQL-adatbázishoz, valamint hogyan végezhet el egy egyszerű lekérdezést Transact-SQL (T-SQL) nyelvű utasításokkal.

[AZURE.INCLUDE [Sign in](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

A tűzfalszabályokkal kapcsolatos információkért lásd: [Tűzfal beállításainak konfigurálása (Azure SQL Database)](sql-database-configure-firewall-settings.md).

## Mintalekérdezések futtatása

A logikai kiszolgálóhoz való csatlakozás után csatlakozhat az adatbázishoz, és futtathatja a mintalekérdezést. 

1. Az **Object Explorerben** navigáljon a kiszolgálón egy olyan adatbázishoz, amelyhez van engedélye, például az **AdventureWorks** mintaadatbázishoz.
2. Kattintson a jobb gombbal az adatbázisra, majd válassza az **Új lekérdezés** lehetőséget.

    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. A lekérdezésablakban másolja és illessze be a következő kódot.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Kattintson a **Végrehajt** gombra.  Sikeres lekérdezés esetén a következő képernyőfelvételen látható eredmény jelenik meg.

    ![Siker. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Következő lépések

T-SQL-utasításokkal adatbázisokat hozhat létre és kezelhet az Azure-ban, hasonlóképpen mint az SQL Serverrel. Ha tisztában van a T-SQL és az SQL Server együttes használatával, tekintse meg [az Azure SQL Database és a Transact-SQL ismertetőjét](sql-database-transact-sql-information.md), amely összefoglalja a kettő közötti különbségeket.

Ha most ismerkedik a T-SQL-lel, tekintse meg [a Transact-SQL-utasítások írásával kapcsolatos oktatóanyagunkat](https://msdn.microsoft.com/library/ms365303.aspx), valamint a [Transact-SQL-adatbázismotor leírását](https://msdn.microsoft.com/library/bb510741.aspx).

Az adatbázis-felhasználók és adatbázis-felhasználói rendszergazdák létrehozásának kezdő lépéseit a következő cikk ismerteti: [Azure SQL Database – biztonsággal kapcsolatos első lépések](sql-database-get-started-security.md)



<!--HONumber=Jun16_HO2-->


