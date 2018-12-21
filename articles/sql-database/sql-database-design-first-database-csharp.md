---
title: Az első Azure SQL Database-adatbázis megtervezése – C#| Microsoft Docs
description: Tudnivalók arról, hogyan lehet megtervezni az első Azure SQL Database-adatbázist és csatlakozni hozzá egy C# programmal az ADO.NET használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 12/10/2018
ms.openlocfilehash: cf180f6e2970ac4435602f1cceeb98a4dd9e8724
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727165"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Oktatóanyag: Azure SQL Database-adatbázis tervezése és csatlakozás az adatbázishoz C# és ADO.NET használatával

Az Azure SQL Database egy relációs adatbázis-a-szolgáltatás (DBaaS) a Microsoft cloud (Azure). Ez az oktatóanyag bemutatja, hogyan végezheti el a következőket az Azure Portal, valamint az ADO.NET és a Visual Studio használatával:

> [!div class="checklist"]
> * Adatbázis létrehozása az Azure Portalon
> * Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
> * Csatlakozás az adatbázishoz az ADO.NET és a Visual Studio használatával
> * Táblázatok létrehozása az ADO.NET-tel
> * Adatok beszúrása, frissítése és törlése az ADO.NET-tel
> * Adatok lekérdezése – ADO.NET

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Egy telepített [Visual Studio 2017](https://www.visualstudio.com/downloads/)

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az alapvető adatbázis-feladatok például adatbázisok és táblák létrehozása, csatlakozzon az adatbázishoz, adatok betöltése és lekérdezéseket futtathat. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatbázis létrehozása
> * Tűzfalszabály beállítása
> * Csatlakozás az adatbázishoz a [Visual Studio és a C#](sql-database-connect-query-dotnet-visual-studio.md) használatával
> * Táblázatok létrehozása
> * INSERT, update, delete és adatokat kérdezhet le

Folytassa a következő oktatóanyaggal adatok migrálása.

> [!div class="nextstepaction"]
> [SQL Server migrálása felügyelt Azure SQL Database-példányra kapcsolat nélküli üzemmódban, a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md)
