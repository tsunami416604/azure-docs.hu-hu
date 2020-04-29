---
title: Adatok szinkronizálása Azure SQL Database Edge-ből SQL-adatszinkronizálás használatával | Microsoft Docs
description: Tudnivalók az adatok Azure SQL Database Edge használatával történő szinkronizálásáról az Azure SQL-adatszinkronizálás
keywords: SQL Database Edge, adatok szinkronizálása az SQL Database Edge-ből, az SQL Database Edge adatszinkronizálása
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74384183"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Oktatóanyag: adatok szinkronizálása SQL Database Edge-ből Azure SQL Database a SQL-adatszinkronizálás használatával

Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure SQL-adatszinkronizálás *Sync Group* az adatok növekményes szinkronizálására Azure SQL Database Edge-ből Azure SQL Databasere. A SQL-adatszinkronizálás Azure SQL Database-on alapuló szolgáltatás, amely lehetővé teszi, hogy szinkronizálja a kétirányúan kiválasztott adatokat több SQL-adatbázis és SQL Server-példány között. További információ a SQL-adatszinkronizálásről: [Azure SQL-adatszinkronizálás](../sql-database/sql-database-sync-data.md).

Mivel az SQL Database Edge az [SQL Server adatbázismotor](/sql/sql-server/sql-server-technical-documentation/)legújabb verziójára épül, a helyszíni SQL Server példányra alkalmazható adatszinkronizálási mechanizmusok is használhatók az adatok szinkronizálására egy Edge-eszközön futó SQL Database Edge-példányon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyaghoz az [Azure SQL-adatszinkronizálás adatszinkronizálási ügynökével](../sql-database/sql-database-data-sync-agent.md)konfigurált Windows-számítógép szükséges.

## <a name="before-you-begin"></a>Előkészületek

* Hozzon létre egy Azure SQL-adatbázist. Az Azure SQL Database Azure Portal használatával történő létrehozásával kapcsolatos információkért lásd: [önálló adatbázis létrehozása Azure SQL Databaseban](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Hozza létre a táblákat és az egyéb szükséges objektumokat a Azure SQL Database üzemelő példányában.

* Hozza létre a szükséges táblákat és objektumokat a Azure SQL Database Edge-telepítésben. További információ: [SQL Database DAC-csomagok használata SQL Database Edge használatával](stream-analytics.md).

* Regisztrálja az Azure SQL Database Edge-példányt az Azure SQL-adatszinkronizálás adatszinkronizálási ügynökével. További információ: [helyszíni SQL Server-adatbázis hozzáadása](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Az Azure SQL Database és a SQL Database Edge közötti adatszinkronizálás

Az Azure SQL Database-adatbázisok és az SQL Database Edge-példányok SQL-adatszinkronizálás használatával történő szinkronizálásának beállítása három fő lépést foglal magában:  

1. Szinkronizálási csoport létrehozásához használja a Azure Portal. További információ: [szinkronizálási csoport létrehozása](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Egyetlen *központi* adatbázis használatával több szinkronizálási csoportot is létrehozhat, hogy a különböző SQL Database peremhálózati példányokból származó adatokat szinkronizálja egy vagy több Azure-beli SQL-adatbázisba.

2. Szinkronizált Tagok hozzáadása a szinkronizálási csoporthoz. További információ: [szinkronizálási Tagok hozzáadása](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Állítsa be úgy a szinkronizálási csoportot, hogy kiválassza a szinkronizálás részét képező táblákat. További információ: [szinkronizálási csoport konfigurálása](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Az előző lépések végrehajtása után egy szinkronizálási csoporttal rendelkezik, amely egy Azure SQL Database-adatbázist és egy SQL Database Edge-példányt tartalmaz.

A SQL-adatszinkronizálásról további információt a következő cikkekben talál:

* [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](../sql-database/sql-database-data-sync-agent.md)

* [Ajánlott eljárások](../sql-database/sql-database-best-practices-data-sync.md) és [az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](../sql-database/sql-database-sync-monitor-oms.md)

* [A szinkronizálási séma frissítése a Transact-SQL vagy a](../sql-database/sql-database-update-sync-schema.md) [PowerShell használatával](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>További lépések

* [Azure SQL Database és Azure SQL Database Edge közötti szinkronizáláshoz használja a PowerShellt](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Ebben az oktatóanyagban cserélje le `OnPremiseServer` az adatbázis részleteit az Azure SQL Database Edge részleteire.
