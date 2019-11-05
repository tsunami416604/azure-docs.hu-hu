---
title: Adatok szinkronizálása Azure SQL Database Edge-ből SQL-adatszinkronizálás használatával | Microsoft Docs
description: Tudnivalók az Azure SQL Database Edge és az Azure közötti adatszinkronizálásról SQL-adatszinkronizálás
keywords: SQL Database Edge, adatok szinkronizálása az SQL Database Edge-ből, az SQL Database Edge adatszinkronizálása
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501315"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Oktatóanyag: adatok szinkronizálása SQL Database Edge-ből Azure SQL Database a SQL-adatszinkronizálás használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja SQL-adatszinkronizálás *szinkronizálási csoportot* az adatok Azure SQL Database Edge-ből Azure SQL Databasere való növekményes szinkronizálásához. A SQL-adatszinkronizálás Azure SQL Database-on alapuló szolgáltatás, amely lehetővé teszi, hogy szinkronizálja a kétirányúan kiválasztott adatokat több SQL-adatbázis és SQL Server-példány között. További információ az Azure SQL-adatszinkronizálásról: [azure SQL-adatszinkronizálás](../sql-database/sql-database-sync-data.md).

Mivel az Azure SQL Database Edge az [Microsoft SQL Server adatbázismotor](/sql/sql-server/sql-server-technical-documentation/)legújabb verzióira épül, a helyszíni SQL Server példányra alkalmazható adatszinkronizálási mechanizmusok is felhasználhatók az adatok egy SQL Database Edge-be vagy-ból való szinkronizálására. peremhálózati eszközön futó példány.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyaghoz az [Azure SQL-adatszinkronizálás-ügynökkel](../sql-database/sql-database-data-sync-agent.md)konfigurált Windows-számítógép szükséges.

## <a name="before-you-begin"></a>Előzetes teendők

* Hozzon létre egy Azure SQL Database. További információ a Azure SQL Database Azure Portal használatával történő létrehozásáról: [önálló adatbázis létrehozása a Azure SQL Database-ben](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Hozza létre a táblákat és az egyéb szükséges objektumokat a Azure SQL Database üzemelő példányában.

* Hozza létre a szükséges táblákat és objektumokat a Azure SQL Database Edge-telepítésben. További információ: [SQL Database DAC-csomagok használata SQL Database Edge használatával](stream-analytics.md).

* Regisztrálja az Azure SQL Database Edge-példányt az Azure SQL-adatszinkronizálás-ügynökkel. További információ: [helyszíni SQL Server-adatbázis hozzáadása](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Azure SQL Database és SQL Database Edge közötti adatszinkronizálás

A Azure SQL Database és az SQL Database Edge-példány közötti szinkronizálás beállítása a SQL-adatszinkronizálás használatával három fő lépésből áll.  

1. Szinkronizálási csoport létrehozásához használja a Azure Portal. A szinkronizálási csoport létrehozásával kapcsolatban tekintse meg a [szinkronizálási csoport létrehozása Azure Portal használatával](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)című témakört. Ugyanaz a *hub* -adatbázis több különböző szinkronizálási csoport létrehozására is használható, amelyek különböző SQL Database peremhálózati példányokból származó adatokat szinkronizálnak egy vagy több Azure-beli SQL-adatbázisba.

2. Szinkronizált Tagok hozzáadása a szinkronizálási csoporthoz. Ha tagokat szeretne felvenni a szinkronizálási csoportba, tekintse meg a [Tagok hozzáadása SQL-adatszinkronizálás csoportot](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Konfigurálja úgy a szinkronizálási csoportot, hogy kiválassza a szinkronizálás részét képező táblákat. A szinkronizálási csoport konfigurálásával kapcsolatban lásd: [szinkronizálási csoport konfigurálása](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

A fenti lépések elvégzése után egy szinkronizálási csoporttal rendelkezik, amely tartalmaz egy Azure SQL Database és egy SQL Database Edge-példányt.

SQL-adatszinkronizálásról a következő cikkekben talál további információt:

* [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](../sql-database/sql-database-data-sync-agent.md)

* [Ajánlott eljárások](../sql-database/sql-database-best-practices-data-sync.md) és [az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](../sql-database/sql-database-sync-monitor-oms.md)

* [A szinkronizálási séma frissítése a Transact-SQL vagy a](../sql-database/sql-database-update-sync-schema.md) [PowerShell használatával](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>További lépések

* [Azure SQL Database és Azure SQL Database Edge közötti szinkronizáláshoz használja a PowerShellt](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Az oktatóanyagban cserélje le a *OnPremiseServer* -adatbázis részleteit az Azure SQL Database Edge részleteire.
