---
title: Csatlakozás az SQL Database-hez – SQL Server Management Studio | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban az SQL Server Management Studio (SSMS) használatával. Ezt követően pedig mintalekérdezést futtathat a Transact-SQL (T-SQL) használatával.
metacanonical: ''
keywords: csatlakozás sql database-hez,sql server management studio
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein;carlrab

---
# Csatlakozás az SQL Database-hez az SQL Server Management Studio segítségével, és T-SQL-mintalekérdezés futtatása
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Ebből a cikkből megtudhatja, hogyan csatlakozhat egy SQL Database adatbázishoz az SQL Server Management Studio (SSMS) használatával. A sikeres csatlakozás után futtatunk egy egyszerű Transact-SQL (T-SQL) lekérdezést az adatbázissal való kommunikáció ellenőrzéséhez.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[!INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

## Mintalekérdezések futtatása
A kiszolgálóhoz való csatlakozás után csatlakozhat egy adatbázishoz, és futtathat egy mintalekérdezést. Ha nem ismeri a lekérdezések írásának módját, tekintse meg a [Writing Transact-SQL Statements](https://msdn.microsoft.com/library/ms365303.aspx) (Transact-SQL-utasítások írása) című témakört.

1. Az **Object Explorerben** lépjen a kiszolgáló egyik adatbázisához, például az **AdventureWorks** mintaadatbázisához.
2. Kattintson a jobb gombbal az adatbázisra, majd válassza az **Új lekérdezés** lehetőséget:
   
    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)
3. A lekérdezésablakban másolja és illessze be a következőt:
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
4. Kattintson a **Végrehajt** gombra:
   
    ![Siker. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Következő lépések
T-SQL-utasításokkal adatbázisokat hozhat létre és kezelhet az Azure-ban, hasonlóképpen mint az SQL Serverrel. Ha tisztában van a T-SQL és az SQL Server együttes használatával, tekintse meg [az Azure SQL Database és a Transact-SQL ismertetőjét](sql-database-transact-sql-information.md), amely összefoglalja a kettő közötti különbségeket.

Ha most ismerkedik a T-SQL-lel, tekintse meg [a Transact-SQL-utasítások írásával kapcsolatos oktatóanyagunkat](https://msdn.microsoft.com/library/ms365303.aspx), valamint a [Transact-SQL-adatbázismotor leírását](https://msdn.microsoft.com/library/bb510741.aspx).

Az adatbázis-felhasználók és adatbázis-felhasználói rendszergazdák létrehozásának kezdő lépéseit a következő cikk ismerteti: [Azure SQL Database – biztonsággal kapcsolatos első lépések](sql-database-get-started-security.md)

További tudnivalók az SSMS-ről: [Use SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (Az SQL Server Management Studio használata).

<!--HONumber=Sep16_HO4-->


