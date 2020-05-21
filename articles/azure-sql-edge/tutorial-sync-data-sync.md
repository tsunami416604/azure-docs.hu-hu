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
ms.openlocfilehash: 7971681c3f0c99a11567e6a30e61167c5d42348c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680508"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Oktatóanyag: adatok szinkronizálása az SQL Edge-ből a Azure SQL Databasere SQL-adatszinkronizálás használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhat Azure SQL-adatszinkronizálás *szinkronizálási csoportot* az adatok Azure SQL Edge-ből Azure SQL Databaseba való növekményes szinkronizálásához. A SQL-adatszinkronizálás Azure SQL Database-on alapuló szolgáltatás, amely lehetővé teszi, hogy szinkronizálja a kétirányúan kiválasztott adatokat több SQL-adatbázis és SQL Server-példány között. További információ a SQL-adatszinkronizálásről: [Azure SQL-adatszinkronizálás](../sql-database/sql-database-sync-data.md).

Mivel az SQL Edge az [SQL Server adatbázismotor](/sql/sql-server/sql-server-technical-documentation/)legújabb verziójára épül, a helyszíni SQL Server példányra alkalmazható adatszinkronizálási mechanizmusok is használhatók az adatok szinkronizálására egy Edge-eszközön futó SQL Edge-példányon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyaghoz az [Azure SQL-adatszinkronizálás adatszinkronizálási ügynökével](../sql-database/sql-database-data-sync-agent.md)konfigurált Windows-számítógép szükséges.

## <a name="before-you-begin"></a>Előkészületek

* Hozzon létre egy Azure SQL-adatbázist. Az Azure SQL Database Azure Portal használatával történő létrehozásával kapcsolatos információkért lásd: [önálló adatbázis létrehozása Azure SQL Databaseban](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Hozza létre a táblákat és az egyéb szükséges objektumokat a Azure SQL Database üzemelő példányában.

* Hozza létre a szükséges táblákat és objektumokat az Azure SQL Edge üzemelő példányában. További információ: [SQL Database DAC-csomagok használata az SQL Edge használatával](deploy-dacpac.md).

* Regisztrálja az Azure SQL Edge-példányt az Azure SQL-adatszinkronizálás adatszinkronizálási ügynökével. További információ: [helyszíni SQL Server-adatbázis hozzáadása](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Az Azure SQL Database és az SQL Edge közötti adatszinkronizálás

Az Azure SQL Database-adatbázisok és az SQL Edge-példányok SQL-adatszinkronizálás használatával történő szinkronizálásának beállítása három fő lépésből áll:  

1. Szinkronizálási csoport létrehozásához használja a Azure Portal. További információ: [szinkronizálási csoport létrehozása](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Egyetlen *központi* adatbázis használatával több szinkronizálási csoportot is létrehozhat, amelyek különböző SQL Edge-példányokból származó adatokat szinkronizálnak egy vagy több Azure-beli SQL-adatbázisba.

2. Szinkronizált Tagok hozzáadása a szinkronizálási csoporthoz. További információ: [szinkronizálási Tagok hozzáadása](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Állítsa be úgy a szinkronizálási csoportot, hogy kiválassza a szinkronizálás részét képező táblákat. További információ: [szinkronizálási csoport konfigurálása](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Az előző lépések elvégzése után egy szinkronizálási csoporttal rendelkezik, amely tartalmaz egy Azure SQL Database-adatbázist és egy SQL Edge-példányt.

A SQL-adatszinkronizálásról további információt a következő cikkekben talál:

* [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](../sql-database/sql-database-data-sync-agent.md)

* [Ajánlott eljárások](../sql-database/sql-database-best-practices-data-sync.md) és [az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](../sql-database/sql-database-sync-monitor-oms.md)

* [A szinkronizálási séma frissítése a Transact-SQL vagy a](../sql-database/sql-database-update-sync-schema.md) [PowerShell használatával](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Következő lépések

* [Azure SQL Database és az Azure SQL Edge közötti szinkronizáláshoz használja a PowerShellt](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Ebben az oktatóanyagban cserélje le az `OnPremiseServer` adatbázis adatait az Azure SQL Edge részleteire.
