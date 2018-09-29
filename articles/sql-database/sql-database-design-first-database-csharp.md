---
title: Az első Azure SQL Database-adatbázis megtervezése – C#| Microsoft Docs
description: Tudnivalók arról, hogyan lehet megtervezni az első Azure SQL Database-adatbázist és csatlakozni hozzá egy C# programmal az ADO.NET használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 06/07/2018
ms.openlocfilehash: 65a9bde6fa086dc56809df9619ceee1c5b417e31
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063301"
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Azure SQL Database-adatbázis tervezése és csatlakozás az adatbázishoz C# és ADO.NET használatával

Az Azure SQL Database a Microsoft Cloudon (Azure) alapuló, szolgáltatásként nyújtott relációs adatbázis (DBaaS). Ez az oktatóanyag bemutatja, hogyan végezheti el a következőket az Azure Portal, valamint az ADO.NET és a Visual Studio használatával: 

> [!div class="checklist"]
> * Adatbázis létrehozása az Azure Portalon
> * Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
> * Csatlakozás az adatbázishoz az ADO.NET és a Visual Studio használatával
> * Táblázatok létrehozása az ADO.NET-tel
> * Adatok beszúrása, frissítése és törlése az ADO.NET-tel 
> * Adatok lekérdezése – ADO.NET

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A [Visual Studio Community 2017, a Visual Studio Professional 2017 vagy a Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/) telepítése.

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az alapvető adatbázis-feladatokat, mint például az adatbázisok és táblák létrehozását, az adatok betöltését és lekérdezését, valamint az adatbázisok korábbi időpontra való visszaállítását. Megismerte, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Adatbázis létrehozása
> * Tűzfalszabály beállítása
> * Csatlakozás az adatbázishoz a [Visual Studio és a C#](sql-database-connect-query-dotnet-visual-studio.md) használatával
> * Táblázatok létrehozása
> * Adatok beszúrása, frissítése és törlése
> * Adatok lekérdezése

Folytassa a következő oktatóanyaggal, amely az adatok migrálását ismerteti.

> [!div class="nextstepaction"]
> [SQL Server-adatbázis migrálása az Azure SQL Database szolgáltatásba](sql-database-migrate-your-sql-server-database.md)

