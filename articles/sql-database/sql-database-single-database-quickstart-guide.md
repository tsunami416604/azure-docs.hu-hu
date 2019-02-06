---
title: Rövid útmutató – az Azure SQL Database önálló adatbázisok |} A Microsoft Docs
description: Ismerje meg gyorsan az Azure SQL Database önálló adatbázisok használatába
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 0dc9d18ea4dae6ea0f45d1a91392eb1994e6e80e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757412"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Ismerkedés az Azure SQL Database önálló adatbázisok

[Önálló adatbázis](sql-database-single-index.yml) teljes körűen felügyelt PaaS-adatbázis (DbaaS) szolgáltatás, amely ideális tárolási motor a felhőben keletkezett modern alkalmazásokhoz. Ebben a szakaszban megtudhatja, gyorsan konfigurálása és a egy önálló adatbázis létrehozása az SQL Database-ben.

## <a name="quickstart-overview"></a>A rövid útmutató – áttekintés

Ebben a szakaszban láthatja a rendelkezésre álló cikkeket, amelyek segítségével gyorsan hozzáláthat az önálló adatbázisok áttekintése. Az alábbi rövid útmutatókat engedélyezése, hogy gyorsan létrehozhat egy adatbázist, egy adatbázis-kiszolgáló tűzfalszabály konfigurálása és egy adatbázis importálja az új önálló adatbázisok használata egy `.bacpac` fájlt:

- [Az Azure portal használatával egy önálló adatbázis létrehozása](sql-database-single-database-get-started.md).
- Az adatbázis létrehozása után kell [tűzfalszabályainak konfigurálása az adatbázis biztonságossá](sql-database-server-level-firewall-rule.md).
- Ha egy meglévő adatbázist az Azure-bA migrálni kívánt SQL Server-kiszolgálón, telepítenie kell [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , amely elemzi az adatbázisok SQL Server-kiszolgálón, és bármilyen probléma, hogy nem blokkolja-e az egyszeri áttelepítés található adatbázis-telepítési beállítás. Ha nem találja a problémát, exportálhatja az adatbázis- `.bacpac` fájl és [importálja azt az Azure portal vagy az SqlPackage használatával](sql-database-import.md).

## <a name="automating-management-operations"></a>Műveletek automatizálása

PowerShell vagy az Azure CLI használatával létrehozása, konfigurálása és az adatbázis méretezése.

- [Létrehozhat és konfigurálhat egy önálló adatbázis PowerShell-lel](scripts/sql-database-create-and-configure-database-powershell.md)
- [Létrehozhat és konfigurálhat egy önálló adatbázis, Azure CLI használatával](scripts/sql-database-create-and-configure-database-cli.md)
- [Frissítse az egyetlen adatbázis és a méretezési csoport-erőforrások PowerShell-lel](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Az Azure CLI-vel önálló adatbázis és a méretezési csoport erőforrások frissítése](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrálás minimális állásidővel önálló adatbázis

Útmutatókból engedélyezése, hogy gyorsan hozzon létre vagy az adatbázis importálása az Azure-bA egy `.bacpac` fájlt. Azonban `.bacpac` és `.dacpac` fájlok úgy tervezték, gyorsan át adatbázisokat az SQL Server és az Azure SQL Database változatokhoz különböző verzióin átívelő, vagy megvalósítása a folyamatos integráció a DevOps-folyamatok az. Azonban ez a módszer nem készült áttelepítési az éles adatbázisok minimális állásidővel, mert állítsa le az új adatok felvétele, várjon, amíg az Exportálás a forrás-adatbázisnak kell egy `.bacpac` végrehajtásához fájlt, és ezután Várjon, amíg az importálás be Az Azure SQL Database végrehajtásához. A várakozó mindegyikének különösen a nagy adatbázisok az alkalmazás állásidőt eredményez. Az éles adatbázis áthelyezéséhez kell áttelepíteni egy hatékony megoldást, amely garantálja a migrálás minimális állásidővel. Ehhez használja a [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) telepíthet át az adatbázist a minimális állásidővel... A DMS úgy hajtja ezt végre növekményes leküldése az egyetlen adatbázis a forrásadatbázis végzett módosításokat. Így gyorsan válthat a minimális állásidővel céladatbázis forrásból alkalmazását.

## <a name="next-steps"></a>További lépések

- Keresse meg a [támogatott funkció az Azure SQL Database magas szintű listájának](sql-database-features.md).
- Ismerje meg, hogyan javíthatja a [biztonságosabbá adatbázis](sql-database-security-tutorial.md).
- Hogyan speciális keresés-a-k [egy önálló adatbázis használata az Azure SQL Database](sql-database-howto-single-database.md).
- Keresse meg a további mintaszkriptek írt [PowerShell](sql-database-powershell-samples.md) és [Azure CLI-vel](sql-database-cli-samples.md).
- Tudjon meg többet a [felügyeleti API](sql-database-single-databases-manage.md) , hogy az adatbázisok konfigurálásához használhatja.
