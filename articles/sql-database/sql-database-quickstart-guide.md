---
title: Rövid útmutató – Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, hogyan gyorsan hozzáláthat az Azure SQL Database – Egypéldányos
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: dad9f004b49ee98e6efdd7d10b779ec3bd81fa47
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55105956"
---
# <a name="getting-started-with-azure-sql-database-singleton"></a>Ismerkedés az Azure SQL Database egyedülálló

[Az Azure SQL Database - egyszeres](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) vagy önálló adatbázis teljes körűen felügyelt PaaS-adatbázis (DbaaS) szolgáltatás, amely ideális tárolási motor a felhőben keletkezett modern alkalmazásokhoz. Ebben a szakaszban megtudhatja, gyorsan konfigurálásáról és az SQL-adatbázis létrehozása.

## <a name="quickstart-overview"></a>A rövid útmutató – áttekintés

Ebben a szakaszban láthatja a rendelkezésre álló cikkeket, amelyek segítségével gyorsan hozzáláthat az önálló adatbázisok áttekintése. A legegyszerűbben úgy, hogy az első SQL-adatbázis létrehozása az, hogy használja [az Azure Portalon](sql-database-get-started-portal.md) ahol konfigurálhatja a szükséges paramétereket.
A létrehozás után kell [tűzfalszabályainak konfigurálása az adatbázis biztonságossá](sql-database-get-started-portal-firewall.md). 

Ha egy meglévő adatbázist az Azure-bA migrálni kívánt SQL Server-kiszolgálón, telepítenie kell [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , amely elemzi az adatbázisok SQL Server-kiszolgálón, és keresse meg a konfigurált replikához való migrálás nem blokkolja-e bármilyen probléma Az adatbázis. Ha nem találja a problémát, exportálhatja az adatbázis- `.bacpac` fájl és [importálja azt az Azure portal vagy az SqlPackage használatával](sql-database-import.md).

Ezek az útmutatók lehetővé teszi gyorsan konfigurálhatja, létrehozása és importálása az adatbázisok az Azure-felhőben.

## <a name="automating-management-operations"></a>Műveletek automatizálása

Az Azure Portalon egyszerű létrehozása és módosítása az egyetlen Azure Database lehetővé teszi. Alternatív megoldásként használható [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md), vagy [Azure CLI-vel](scripts/sql-database-create-and-configure-database-cli.md) adatbázisok létrehozására.
Az önálló adatbázisok és a méretezési csoport-erőforrások használatával is frissítheti [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) vagy [Azure CLI-vel](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-azure-single-database-with-minimal-downtime"></a>Önálló adatbázis az Azure-ba való migrálás minimális állásidővel

Ezen rövid útmutatók cikkeit engedélyezése, hogy gyorsan hozzon létre vagy az adatbázis importálása az Azure-bA `.bacpac`. Azonban `.bacpac` és `.dacpack` fájlokat pedig áthelyezheti különböző verzióiban az SQL Server és az Azure SQL Database (önálló adatbázist vagy a felügyelt példány) adatbázis vagy megvalósítása a folyamatos integráció a DevOps-folyamatok az úgy tervezték. Azonban ez a módszer nem készült áttelepítési az éles adatbázisok minimális állásidővel, mert meg kellene várnia a forrásadatbázis, mint exportálni `.bacpac` fájlt, és importálja azt az Azure SQL Database-remapping némi állásidővel, a alkalmazás, különösen akkor, ha az adatbázis nagyobb méretű. Az éles adatbázis áthelyezéséhez, valószínűleg kell áttelepíteni egy hatékony megoldást, amely garantálja a migrálás minimális állásidővel. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) egy szolgáltatás, amely az adatbázis a minimális állásidővel telepíthet át. Így gyorsan válthat a minimális állásidővel céladatbázis forrásból alkalmazását.

## <a name="next-steps"></a>További lépések

* Keresse meg a [támogatott funkció az Azure SQL Database magas szintű listájának](sql-database-features.md). 
* Ismerje meg, hogyan javíthatja a [biztonságosabbá adatbázis](sql-database-security-tutorial.md). 
* Oktatóanyagok a Speciális keresés [című](sql-database-howto-single-database.md). 
* Keresse meg a további mintaszkriptek írt [PowerShell](sql-database-powershell-samples.md) és [Azure CLI-vel](sql-database-cli-samples.md). 
* Tudjon meg többet a [felügyeleti API](sql-database-single-databases-manage.md) , hogy az adatbázisok konfigurálásához használhatja. 
