---
title: 'SQL Database oktatóanyag: Ismerkedés az adatbázis-biztonsággal'
description: Felhasználói fiókok létrehozása az adatbázisok eléréséhez és kezeléséhez.
keywords: ''
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab

---
# SQL Database oktatóanyag: SQL Database felhasználói fiókok létrehozása az adatbázisok eléréséhez és kezeléséhez
> [!div class="op_single_selector"]
> * [Első lépéseket ismertető oktatóanyag](sql-database-get-started-security.md)
> * [Hozzáférés biztosítása](sql-database-manage-logins.md)
> 
> 

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket az SQL Server Management Studio (SSMS) használatával:

* Bejelentkezés az SQL Database szolgáltatásba egyszerű kiszolgálószintű bejelentkezéssel
* SQL Database felhasználói fiók létrehozása
* [Db_owner engedélyek](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) megadása egy SQL Database-felhasználónak.
* Csatlakozás SQL adatbázishoz nem egyszerű, kiszolgálószintű felhasználói fiókkal.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## Következő lépések
Miután végigment ezen az SQL Database oktatóanyagon, és létrehozott egy felhasználói fiókot, illetve DBO engedélyeket adott ki hozzá, készen áll arra, hogy további információkat tudjon meg az [SQL Database adatbázis-biztonsága](sql-database-manage-logins.md) témáról.

<!--HONumber=Sep16_HO4-->


