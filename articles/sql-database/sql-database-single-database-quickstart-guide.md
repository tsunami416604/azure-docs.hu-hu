---
title: Rövid útmutató – önálló adatbázisok
description: Megtudhatja, hogyan kezdheti meg gyorsan az önálló adatbázisok Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 6070b53e5f906bc378402d98275b8f798f57b505
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674426"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Első lépések az önálló adatbázisok Azure SQL Database

Az [önálló adatbázisok](sql-database-single-index.yml) teljes körűen felügyelt, a modern Felhőbeli alkalmazásokhoz ideális tárolási motorként szolgáló DbaaS-szolgáltatásként szolgálnak. Ez a szakasz azt ismerteti, hogyan lehet gyorsan konfigurálni és létrehozni egy önálló adatbázist a SQL Database.

## <a name="quickstart-overview"></a>A gyors üzembe helyezés áttekintése

Ebben a szakaszban az elérhető cikkek áttekintését láthatja, amelyek segítségével gyorsan megkezdheti az önálló adatbázisok használatának megkezdését. A következő rövid útmutatók lehetővé teszik egyetlen adatbázis gyors létrehozását, az adatbázis-kiszolgáló tűzfalszabály konfigurálását, majd egy adatbázis importálását az új önálló adatbázisba egy `.bacpac` fájl használatával:

- [Hozzon létre egyetlen adatbázist a Azure Portal használatával](sql-database-single-database-get-started.md).
- Az adatbázis létrehozása után meg kell [védenie az adatbázist a tűzfalszabályok konfigurálásával](sql-database-server-level-firewall-rule.md).
- Ha az Azure-ba áttelepíteni kívánt SQL Server meglévő adatbázisa van, akkor telepítenie kell a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) szolgáltatást, amely a SQL Server-adatbázisokat elemzi, és olyan problémát talál, amely letiltja az áttelepítést az önálló adatbázis központi telepítésére. Ha nem talál problémát, exportálhatja `.bacpac` fájlként az adatbázist, és [importálhatja azt a Azure Portal vagy a SqlPackage használatával](sql-database-import.md).

## <a name="automating-management-operations"></a>Felügyeleti műveletek automatizálása

Az adatbázis létrehozásához, konfigurálásához és méretezéséhez használhatja a PowerShellt vagy az Azure CLI-t.

- [Önálló adatbázis létrehozása és konfigurálása a PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) vagy az [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md) használatával
- [Önálló adatbázis frissítése és erőforrások méretezése a PowerShell vagy az](scripts/sql-database-monitor-and-scale-database-powershell.md) [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md) használatával

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrálás egyetlen adatbázisba minimális állásidővel

Ezekkel a gyors útmutatókkal gyorsan létrehozhatja vagy importálhatja az adatbázist az Azure-ba egy `.bacpac` fájl használatával. A `.bacpac`-és `.dacpac`-fájlok azonban úgy lettek kialakítva, hogy az adatbázisokat gyorsan át lehessen helyezni a SQL Server és a központi telepítési lehetőségek különböző verzióiban, vagy a folyamatos integrációt a DevOps-folyamaton Azure SQL Database belül. Ez a módszer azonban nem az üzemi adatbázisok minimális állásidővel való áttelepítésére lett tervezve, mert le kell állítania az új adatmennyiséget, várnia kell a forrásadatbázis exportálását egy `.bacpac` fájlba a befejezéshez, majd várnia kell, amíg az importálás befejeződik a Azure SQL Database. Az összes várakozási idő az alkalmazás leállását eredményezi, különösen a nagyméretű adatbázisok esetében. Az éles adatbázis áthelyezéséhez jobb módszerre van szüksége az áttelepítéshez, amely minimális állásidőt garantál. Ehhez használja a [adatáttelepítési szolgáltatást (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) az adatbázis minimális állásidővel való áttelepítéséhez. A DMS ezt úgy hajtja végre, hogy a forrásadatbázis módosításait fokozatosan visszaküldi a visszaállítani kívánt önálló adatbázisra. Így gyorsan válthat az alkalmazás forrásról a cél adatbázisára a minimális állásidővel.

## <a name="hands-on-learning-modules"></a>Gyakorlati tanulási modulok

Az alábbi Microsoft Learn-modulok segítenek a Azure SQL Databaseról való ingyenes tanulásban.

- [Azure SQL-adatbázis kiépítése az alkalmazásadatok tárolására](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Azure SQL Databaset lekérdező ASP.NET-alkalmazás fejlesztése és konfigurálása](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [A Azure SQL Database biztonságossá tétele](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [Azure SQL Database támogatott szolgáltatásainak magas szintű listáját](sql-database-features.md).
- Ismerje meg, hogyan teheti [biztonságosabbá az adatbázisát](sql-database-security-tutorial.md).
- További speciális útmutató: [egyetlen adatbázis használata Azure SQL Databaseban](sql-database-howto-single-database.md).
- További példák a [PowerShell](sql-database-powershell-samples.md) és az [Azure CLI](sql-database-cli-samples.md)-ben írt parancsfájlokra.
- További információ az adatbázisok konfigurálásához használható [felügyeleti API](sql-database-single-databases-manage.md) -ról.
- [Azonosítsa a helyszíni adatbázis megfelelő Azure SQL Database/felügyelt példányának SKU-](/sql/dma/dma-sku-recommend-sql-db/)t.
