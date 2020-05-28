---
title: Adatok szinkronizálása az Azure SQL Edge-ből (előzetes verzió) SQL-adatszinkronizálás használatával
description: Tudnivalók az Azure SQL Edge (előzetes verzió) adatainak az Azure SQL-adatszinkronizálás használatával történő szinkronizálásáról
keywords: SQL Edge, adatok szinkronizálása az SQL Edge-ből, az SQL Edge adatszinkronizálása
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: b269c42258a813f7c08e1c3e9f2065b8b5255ed9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018276"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Oktatóanyag: adatok szinkronizálása az SQL Edge-ből a Azure SQL Databasere SQL-adatszinkronizálás használatával


Ebből az oktatóanyagból megtudhatja, hogyan használhat Azure SQL-adatszinkronizálás *szinkronizálási csoportot* az adatok Azure SQL Edge-ből Azure SQL Databaseba való növekményes szinkronizálásához. A SQL-adatszinkronizálás Azure SQL Database-on alapuló szolgáltatás, amely lehetővé teszi, hogy a Azure SQL Database és SQL Server példányokban több adatbázison is szinkronizálja az Ön által választott adatokat. További információ a SQL-adatszinkronizálásről: [Azure SQL-adatszinkronizálás](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Mivel az SQL Edge az [SQL Server adatbázismotor](/sql/sql-server/sql-server-technical-documentation/)legújabb verziójára épül, a helyszíni SQL Server példányra alkalmazható adatszinkronizálási mechanizmusok is használhatók az adatok szinkronizálására egy Edge-eszközön futó SQL Edge-példányon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyaghoz az [Azure SQL-adatszinkronizálás adatszinkronizálási ügynökével](../azure-sql/database/sql-data-sync-agent-overview.md)konfigurált Windows-számítógép szükséges.

## <a name="before-you-begin"></a>Előkészületek

* Hozzon létre egy Azure SQL-adatbázist. Az Azure SQL Database Azure Portal használatával történő létrehozásával kapcsolatos információkért lásd: [önálló adatbázis létrehozása Azure SQL Databaseban](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Hozza létre a táblákat és az egyéb szükséges objektumokat a Azure SQL Database üzemelő példányában.

* Hozza létre a szükséges táblákat és objektumokat az Azure SQL Edge üzemelő példányában. További információ: [SQL Database DAC-csomagok használata az SQL Edge használatával](deploy-dacpac.md).

* Regisztrálja az Azure SQL Edge-példányt az Azure SQL-adatszinkronizálás adatszinkronizálási ügynökével. További információ: [helyszíni SQL Server-adatbázis hozzáadása](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Az Azure SQL Database és az SQL Edge közötti adatszinkronizálás

Az Azure SQL Database-adatbázisok és az SQL Edge-példányok SQL-adatszinkronizálás használatával történő szinkronizálásának beállítása három fő lépésből áll:  


1. Szinkronizálási csoport létrehozásához használja a Azure Portal. További információ: [szinkronizálási csoport létrehozása](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Egyetlen *központi* adatbázis használatával több szinkronizálási csoportot is létrehozhat, hogy a különböző SQL Edge-példányokból származó adatokat a Azure SQL Database egy vagy több adatbázisába szinkronizálja. 

2. Szinkronizált Tagok hozzáadása a szinkronizálási csoporthoz. További információ: [szinkronizálási Tagok hozzáadása](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Állítsa be úgy a szinkronizálási csoportot, hogy kiválassza a szinkronizálás részét képező táblákat. További információ: [szinkronizálási csoport konfigurálása](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Az előző lépések elvégzése után egy szinkronizálási csoporttal rendelkezik, amely tartalmaz egy Azure SQL Database-adatbázist és egy SQL Edge-példányt.

A SQL-adatszinkronizálásról további információt a következő cikkekben talál:

* [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Ajánlott eljárások](../azure-sql/database/sql-data-sync-best-practices.md) és [az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](../azure-sql/database/sql-data-sync-monitor-sync.md)

* [A szinkronizálási séma frissítése a Transact-SQL vagy a](../azure-sql/database/sql-data-sync-update-sync-schema.md) [PowerShell használatával](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>További lépések


* [Azure SQL Database és az Azure SQL Edge közötti szinkronizáláshoz használja a PowerShellt](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). Ebben az oktatóanyagban cserélje le az `OnPremiseServer` adatbázis adatait az Azure SQL Edge részleteire.
