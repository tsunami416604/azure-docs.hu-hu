---
title: Rövid útmutató – az Azure SQL Database önálló adatbázisok |} A Microsoft Docs
description: Ismerje meg gyorsan az Azure SQL Database önálló adatbázisok használatába
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
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464810"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Ismerkedés az Azure SQL Database önálló adatbázisok

[Önálló adatbázis](sql-database-single-index.yml) az Azure SQL Database teljes körűen felügyelt PaaS-adatbázis (DbaaS) szolgáltatás, amely ideális tárolási motor a felhőben keletkezett modern alkalmazásokhoz. Ebben a szakaszban megtudhatja, gyorsan konfigurálásáról és az SQL-adatbázis létrehozása.

## <a name="quickstart-overview"></a>A rövid útmutató – áttekintés

Ebben a szakaszban láthatja a rendelkezésre álló cikkeket, amelyek segítségével gyorsan hozzáláthat az önálló adatbázisok áttekintése. A legegyszerűbben úgy, hogy az első SQL-adatbázis létrehozása az, hogy használja [az Azure Portalon](sql-database-get-started-portal.md) ahol konfigurálhatja a szükséges paramétereket.
A létrehozás után kell [tűzfalszabályainak konfigurálása az adatbázis biztonságossá](sql-database-get-started-portal-firewall.md). 

Ha egy meglévő adatbázist az Azure-bA migrálni kívánt SQL Server-kiszolgálón, telepítenie kell [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , amely elemzi az adatbázisok SQL Server-kiszolgálón, és keresse meg a konfigurált replikához való migrálás nem blokkolja-e bármilyen probléma Az adatbázis. Ha nem találja a problémát, exportálhatja az adatbázis- `.bacpac` fájl és [importálja azt az Azure portal vagy az SqlPackage használatával](sql-database-import.md).

Ezek az útmutatók lehetővé teszi gyorsan konfigurálhatja, létrehozása és importálása az adatbázisok az Azure-felhőben.

## <a name="automating-management-operations"></a>Műveletek automatizálása

Az Azure portal lehetővé teszi egyszerű létrehozása és módosítása az egyetlen adatbázis. Alternatív megoldásként használható [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md), vagy [Azure CLI-vel](scripts/sql-database-create-and-configure-database-cli.md) adatbázisok létrehozására.
Az egyetlen adatbázis és a méretezési csoport-erőforrások használatával is frissítheti [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) vagy [Azure CLI-vel](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrálás minimális állásidővel önálló adatbázis

Ezen rövid útmutatók cikkeit engedélyezése, hogy gyorsan hozzon létre vagy az adatbázis importálása az Azure-bA `.bacpac`. Azonban `.bacpac` és `.dacpack` fájlokat pedig áthelyezheti különböző verzióiban az SQL Server és az Azure SQL Database (önálló adatbázis, a rugalmas készlet vagy felügyelt példány) adatbázis vagy megvalósításához a folyamatos integráció a devops készültek. folyamat. Azonban ez a módszer nem készült áttelepítési az éles adatbázisok minimális állásidővel, mert meg kellene várnia a forrásadatbázis, mint exportálni `.bacpac` fájlt, és importálja azt az Azure SQL Database-remapping némi állásidővel, a alkalmazás, különösen akkor, ha az adatbázis nagyobb méretű. Az éles adatbázis áthelyezéséhez, valószínűleg kell áttelepíteni egy hatékony megoldást, amely garantálja a migrálás minimális állásidővel. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) egy szolgáltatás, amely az adatbázis a minimális állásidővel telepíthet át. Így gyorsan válthat a minimális állásidővel céladatbázis forrásból alkalmazását.

## <a name="next-steps"></a>További lépések

* Keresse meg a [támogatott funkció az Azure SQL Database magas szintű listájának](sql-database-features.md). 
* Ismerje meg, hogyan javíthatja a [biztonságosabbá adatbázis](sql-database-security-tutorial.md). 
* Oktatóanyagok a Speciális keresés [című](sql-database-howto-single-database.md). 
* Keresse meg a további mintaszkriptek írt [PowerShell](sql-database-powershell-samples.md) és [Azure CLI-vel](sql-database-cli-samples.md). 
* Tudjon meg többet a [felügyeleti API](sql-database-single-databases-manage.md) , hogy az adatbázisok konfigurálásához használhatja. 
