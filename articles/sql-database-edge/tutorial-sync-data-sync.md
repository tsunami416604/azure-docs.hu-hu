---
title: Adatok szinkronizálása az Azure SQL Database Edge-ből az SQL Data Sync használatával | Microsoft dokumentumok
description: Ismerje meg az Azure SQL Database Edge adatainak szinkronizálását az Azure SQL Data Sync használatával
keywords: SQL adatbázis széle,adatok szinkronizálása az SQL adatbázis pereméről, sql adatbázis peremhálózati adatszinkronizálása
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74384183"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Oktatóanyag: Adatok szinkronizálása az SQL Database Edge-ről az Azure SQL Database-re az SQL Data Sync használatával

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure SQL Data Sync *szinkronizálási csoportot* az Azure SQL Database Edge-ből az Azure SQL Database-adatbázisból az Azure SQL Database-re vonatkozó adatok növekményes szinkronizálásához. Az SQL Data Sync egy Azure SQL Database-re épülő szolgáltatás, amely lehetővé teszi a kiválasztott adatok kétirányú szinkronizálását több SQL-adatbázis és SQL Server-példány között. Az SQL Data Sync szolgáltatásról az [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md)című témakörben talál további információt.

Mivel az SQL Database Edge az [SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation/)legújabb verzióira épül, a helyszíni SQL Server-példányokra alkalmazható adatszinkronizálási mechanizmus sal is használható az sql database edge-példányok peremhálózati példányai számára történő szinkronizáláshoz.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag hoz szükség egy Windows-számítógépre, amely az [Azure SQL Data Sync adatszinkronizálási adatszinkronizálási ügynökkel](../sql-database/sql-database-data-sync-agent.md)van konfigurálva.

## <a name="before-you-begin"></a>Előkészületek

* Hozzon létre egy Azure SQL-adatbázist. Az Azure SQL-adatbázis Azure Portal on keresztül történő létrehozásáról az [Egyetlen adatbázis létrehozása az Azure SQL Database-ben című témakörben](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)talál további információt.

* Hozza létre a táblákat és más szükséges objektumokat az Azure SQL Database központi telepítésében.

* Hozza létre a szükséges táblákat és objektumokat az Azure SQL Database Edge központi telepítésében. További információt az [SQL Database DAC-csomagok használata az SQL Database Edge-el című témakörben talál.](stream-analytics.md)

* Regisztrálja az Azure SQL Database Edge-példányt az Azure SQL Data Sync adatszinkronizálási adatszinkronizálási ügynökével. További információt a [Helyszíni SQL Server-adatbázis hozzáadása](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)című témakörben talál.

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Adatok szinkronizálása Azure SQL-adatbázis és SQL Database Edge között

Az Azure SQL-adatbázis és az SQL Database Edge-példány közötti szinkronizálás beállítása az SQL Data Sync használatával három kulcsfontosságú lépést foglal magában:  

1. Az Azure Portal segítségével hozzon létre egy szinkronizálási csoportot. További információt a [Szinkronizálási csoport létrehozása](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)című témakörben talál. Egyetlen *központi* adatbázis használatával több szinkronizálási csoportot hozhat létre a különböző SQL Database Edge-példányok adatainak szinkronizálásához az Azure-ban egy vagy több SQL-adatbázissal.

2. Szinkronizálási tagok hozzáadása a szinkronizálási csoporthoz. További információt a [Szinkronizálási tagok hozzáadása című](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)témakörben talál.

3. Állítsa be a szinkronizálási csoportot a szinkronizálás részét játszó táblák kijelöléséhez. További információt a [Szinkronizálási csoport konfigurálása](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)című témakörben talál.

Az előző lépések elvégzése után egy szinkronizálási csoportot, amely tartalmaz egy Azure SQL-adatbázis és egy SQL Database Edge-példányt.

Az SQL Data Sync programról az alábbi cikkekben talál további információt:

* [Adatszinkronizáló ügynök az Azure SQL Data Sync-hez](../sql-database/sql-database-data-sync-agent.md)

* [Gyakorlati tanácsok](../sql-database/sql-database-best-practices-data-sync.md) és [az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival](../sql-database/sql-database-sync-monitor-oms.md)

* [A szinkronizálási séma frissítése transact-SQL vagy](../sql-database/sql-database-update-sync-schema.md) [PowerShell használatával](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>További lépések

* [A PowerShell segítségével szinkronizálhatja az Azure SQL Database és az Azure SQL Database Edge között.](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md) Ebben az oktatóanyagban cserélje le az `OnPremiseServer` adatbázis részleteit az Azure SQL Database Edge részleteire.
