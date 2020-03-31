---
title: Rövid útmutató - egyetlen adatbázis
description: Megtudhatja, hogyan kezdheti el gyorsan az egyetlen adatbázisokat az Azure SQL Database-ben
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78674426"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Egyetlen adatbázis – első lépések az Azure SQL Database-ben

[Egyetlen adatbázis](sql-database-single-index.yml) teljes körűen felügyelt PaaS-adatbázis szolgáltatásként (DbaaS), amely ideális tárolómotor a modern felhőben született alkalmazások számára. Ebben a szakaszban megtudhatja, hogyan konfigurálhatja és hozhat létre egyetlen adatbázist az SQL Database-ben.

## <a name="quickstart-overview"></a>Gyors üzembe helyezés áttekintése

Ebben a szakaszban az elérhető cikkek áttekintése látható, amelyek segítségével gyorsan elindíthatja az egyes adatbázisokat. A következő rövid útmutatók lehetővé teszik egyetlen adatbázis gyors létrehozását, az adatbázis-kiszolgáló tűzfalszabályának konfigurálását, majd az új adatbázisba való importálását egy `.bacpac` fájl használatával:

- [Hozzon létre egyetlen adatbázist az Azure Portal használatával.](sql-database-single-database-get-started.md)
- Az adatbázis létrehozása után [tűzfalszabályok konfigurálásával kell biztosítania az adatbázist.](sql-database-server-level-firewall-rule.md)
- Ha van egy meglévő adatbázis az SQL Server, amely szeretné áttelepíteni az Azure-ba, telepítenie kell [adatáttelepítési asszisztens (DMA),](https://www.microsoft.com/download/details.aspx?id=53595) amely elemzi az adatbázisokat az SQL Server, és megtalálja azokat a problémát, amelyek blokkolhatják az áttelepítést az egyetlen adatbázis-telepítési lehetőség. Ha nem talál problémát, exportálhatja az adatbázist fájlként, `.bacpac` és [importálhatja az Azure Portalon vagy az SqlPackage használatával.](sql-database-import.md)

## <a name="automating-management-operations"></a>A felügyeleti műveletek automatizálása

A PowerShell vagy az Azure CLI segítségével hozhat létre, konfigurálhat és skálázhatja az adatbázist.

- [Egyetlen adatbázis létrehozása és konfigurálása a PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) vagy [az Azure CLI](scripts/sql-database-create-and-configure-database-cli.md) használatával
- [Egyetlen adatbázis frissítése és erőforrások méretezése a PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) vagy [az Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md) használatával

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Áttelepítés egyetlen adatbázisba minimális állásidővel

Ezek a rövid útmutatók lehetővé teszik, `.bacpac` hogy gyorsan hozza létre vagy importálja az adatbázist az Azure-ba egy fájl használatával. A `.bacpac` fájlok `.dacpac` azonban gyorsan áthelyezhetik az adatbázisokat az SQL Server különböző verziói és az Azure SQL Database telepítési lehetőségei között, vagy folyamatos integrációt valósíthatnak meg a DevOps-folyamatban. Ez a módszer azonban nem az éles adatbázisok minimális állásidő vel történő áttelepítésére szolgál, mert le kell állítania az új adatok hozzáadását, meg kell várnia a forrásadatbázis `.bacpac` fájlba való exportálását, majd meg kell várnia, amíg az Importálás befejeződik az Azure SQL Database-be. Mindez várakozási eredmények állásidő az alkalmazás, különösen a nagy adatbázisok. Az éles adatbázis áthelyezéséhez jobb an-átagrálási módra van szükség, amely minimális áttelepítési időt garantál. Ehhez használja az [adatáttelepítési szolgáltatást (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) az adatbázis minimális állásidővel történő áttelepítéséhez.. A DMS ezt úgy valósítja meg, hogy a forrásadatbázisban végrehajtott módosításokat fokozatosan a visszaállítandó adatbázisba tolja. Így gyorsan válthat az alkalmazás forrásról a céladatbázisra a minimális állásidővel.

## <a name="hands-on-learning-modules"></a>Gyakorlati tanulási modulok

Az alábbi Microsoft Learn modulok segítségével ingyenesen megismerheti az Azure SQL Database szolgáltatást.

- [Azure SQL-adatbázis kiépítése az alkalmazásadatok tárolásához](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Azure SQL-adatbázislekérdezést lekérdező ASP.NET alkalmazás fejlesztése és konfigurálása](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Az Azure SQL Database védelme](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>További lépések

- Az [Azure SQL Database támogatott szolgáltatásainak magas szintű listájának megkeresése.](sql-database-features.md)
- További információ az [adatbázis biztonságosabbá](sql-database-security-tutorial.md)tétele.
- További speciális útmutatókat talál [egyetlen adatbázis azure SQL Database-ben való használatáról.](sql-database-howto-single-database.md)
- További mintaparancsfájlok a [PowerShellben](sql-database-powershell-samples.md) és az [Azure CLI-ben.](sql-database-cli-samples.md)
- További információ az adatbázisok konfigurálásához használható [felügyeleti API-ról.](sql-database-single-databases-manage.md)
- [Azonosítsa a megfelelő Azure SQL Database/felügyelt példány termékváltozatát a helyszíni adatbázishoz.](/sql/dma/dma-sku-recommend-sql-db/)
