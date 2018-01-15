---
title: "Az első Azure SQL adatbázis - C# kialakítása |} Microsoft Docs"
description: "Ismerje meg, az első Azure SQL-adatbázis megtervezése, és egy C# programban ADO.NET használatával kapcsolódni hozzá."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 01/12/2018
ms.author: genemi
ms.openlocfilehash: d99456b488302c9b3fa44510af84641876861afe
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Azure SQL-adatbázis megtervezése, és csatlakozzon a C & #x23; és ADO.NET

Az Azure SQL-adatbázis egy relációs adatbázis-mint – a szolgáltatás, a Microsoft Cloud (Azure) a rendszer. Ebben az oktatóanyagban elsajátíthatja a Visual Studio segítségével az Azure portál és az ADO.NET használatával: 

> [!div class="checklist"]
> * Adatbázis létrehozása az Azure-portálon
> * Állítson be egy kiszolgálószintű tűzfalszabályt az Azure-portálon
> * Kapcsolódni az adatbázishoz az ADO.NET és a Visual Studio
> * Az ADO.NET táblák létrehozása
> * Beszúrási, frissítési és törlési ADO.NET adatok 
> * Az ADO.NET adatait

Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiók létrehozását](https://azure.microsoft.com/free/) megkezdése előtt.

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

Ebben az oktatóanyagban megismerte az alapvető adatbázis-feladatok például egy adatbázis és a táblák létrehozása, betölteni és kérdezhet le adatokat, és állítsa vissza az adatbázist egy korábbi időpontra időben. Megismerte, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Adatbázis létrehozása
> * A tűzfalszabályok beállítása
> * Kapcsolódás az adatbázishoz [Visual Studio és a C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Táblázatok létrehozása
> * INSERT, update és adatok törlése
> * Adatok lekérdezése

Előzetes tudnivalók az adatok áttelepítését a következő oktatóanyagot.

> [!div class="nextstepaction"]
>[Az SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-migrate-your-sql-server-database.md)

