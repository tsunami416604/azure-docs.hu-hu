---
title: Adatok áthelyezése az Azure SQL Database - csoportos adatelemzési folyamat
description: Helyezze át az adatokat egybesimított fájlok (CSV- vagy TSV formátumokat) vagy egy helyszíni SQL Server egy Azure SQL Database-ban tárolt adatokkal.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/04/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 291872ca25b8674528b47e695c0d5135296e3451
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472745"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Adatok áthelyezése egy Azure SQL-adatbázisba az Azure Machine Learning számára

Ez a cikk ismerteti a lehetőség (CSV- vagy TSV formátumok) egybesimított fájlok vagy egy helyszíni SQL Server egy Azure SQL Database-ban tárolt adatokkal. Adatok áthelyezése a felhőbe ezeket a feladatokat a csoportos adatelemzési folyamat részét képezik.

Ez a témakör ismerteti az adatok áthelyezése egy helyszíni SQL Server Machine Learning a beállításokat, lásd: [adatok áthelyezése SQL Server-beli virtuális gépen](move-sql-server-virtual-machine.md).

A következő táblázat összefoglalja az adatok áthelyezése az Azure SQL Database, a beállításokat.

| <b>FORRÁS</b> | <b>CÉL: Azure SQL Database</b> |
| --- | --- |
| <b>Egybesimított fájl (CSV- vagy formázott TSV)</b> |[Tömeges beszúrás SQL-lekérdezés](#bulk-insert-sql-query) |
| <b>A helyszíni SQL Server</b> |1.[Egybesimított fájl exportálása](#export-flat-file)<br> 2. [Az SQL Database áttelepítése varázsló](#insert-tables-bcp)<br> 3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Előfeltételek
Az itt ismertetett eljárások szükség van:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure storage-fiók**. Ez az oktatóanyag az adatok tárolása Azure storage-fiók használhat. Ha nem rendelkezik Azure storage-fiókkal, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md) cikk. Miután létrehozta a tárfiókot, szerezze be a tárterület elérésére használt fiók kulcsot kell. Lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-account-manage.md#access-keys).
* A hozzáférést egy **az Azure SQL Database**. Ha be kell állítania az Azure SQL Database [Ismerkedés a Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) információt nyújt az kiépítése az Azure SQL Database egy új példányát.
* Telepített és konfigurált **Azure PowerShell-lel** helyileg. Útmutatásért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

**Adatok**: Találja meg az áttelepítési folyamat használja a [NYC Taxi adatkészlet](http://chriswhong.com/open-data/foil_nyc_taxi/). A NYC Taxi adatkészlet útadatok és vásárokon adatait tartalmazza, és érhető el az Azure blob storage-bA: [NYC i taxik adatait](http://www.andresmh.com/nyctaxitrips/). Egy mintát és egy leírást a fájlok találhatók [NYC Taxi lelassítja adatkészlet leírása](sql-walkthrough.md#dataset).

Mindig a saját adatok egy készletét az itt leírt eljárásokat, vagy kövesse a lépéseket, a NYC Taxi adatkészlet használatával leírtak szerint. Töltse fel a NYC Taxi adatkészlet a helyszíni SQL Server-adatbázisba, kövesse a leírt eljárást [tömeges adatimportálás SQL Server-adatbázisba](sql-walkthrough.md#dbload). Ezek az utasítások a egy SQL Server Azure virtuális gépen, de tölt fel a helyszíni SQL Server eljárás megegyezik.

## <a name="file-to-azure-sql-database"></a> Egybesimított fájl forrásból származó adatok áthelyezése az Azure SQL Database-adatbázishoz
Egybesimított fájlok (CSV, TSV vagy formázott) lévő adatok áthelyezhetők a Bulk Insert SQL-lekérdezést használ az Azure SQL Database-adatbázishoz.

### <a name="bulk-insert-sql-query"></a> Tömeges beszúrás SQL-lekérdezés
A következő eljáráshoz a Bulk Insert SQL-lekérdezést használja a lépések hasonlóak a kezelt adatok áthelyezése egy egybesimított fájl forrás SQL Server-beli virtuális gépen a szakaszokban. További információkért lásd: [Bulk Insert SQL-lekérdezés](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Adatok áthelyezése a helyszíni SQL Serverről Azure SQL Database-adatbázishoz
A forrásadatok egy helyszíni SQL Server tárolódnak, van-e az adatok áthelyezése az Azure SQL Database-adatbázishoz különböző lehetőségeit:

1. [Egybesimított fájl exportálása](#export-flat-file)
2. [Az SQL Database áttelepítése varázsló](#insert-tables-bcp)
3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)
4. [Azure Data Factory](#adf)

A lépéseket az első három nagyon hasonlóak a szakaszokat [adatok áthelyezése SQL Server-beli virtuális gépen](move-sql-server-virtual-machine.md) , amelyek ugyanezeket az eljárásokat vonatkoznak. A témakör megfelelő részeiben mutató hivatkozások szerepelnek az alábbi utasításokat.

### <a name="export-flat-file"></a>Egybesimított fájl exportálása
Ez egy egybesimított fájl exportálása lépései hasonlóak a kezelt [Egybesimított fájl exportálása](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Az SQL Database áttelepítése varázsló
Az SQL Database áttelepítése varázsló használatának lépései is hasonlóak a kezelt [SQL adatbázis-Migrálási varázsló](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Adatbázis biztonsági mentése és visszaállítása
A lépések adatbázis biztonsági mentése és visszaállítás hasonlóak a kezelt [adatbázis biztonsági mentése és visszaállítása](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Az Azure Data Factory
Adatok áthelyezése az Azure SQL Database-adatbázishoz az Azure Data Factory (ADF) az eljárás beállításokról a témakör [adatok áthelyezése a helyszíni SQL Serverről az SQL Azure, az Azure Data Factoryvel](move-sql-azure-adf.md). Ez a témakör bemutatja az adatok áthelyezése a helyszíni SQL Server-adatbázisból egy Azure SQL Database-adatbázishoz az Azure Blob Storage-n keresztül az ADF használatával.

Fontolja meg az ADF használatával, ha adatokat kell folyamatosan migrálható egy hibrid forgatókönyvben, amely hozzáfér a helyszíni és felhőbeli erőforrások, illetve ha az adatok tranzakció van, vagy üzleti logikát hozzáadni azt, ha az áttelepítés alatt álló, illetve módosítani kell. Az ADF lehetővé teszi, hogy az ütemezés és a figyelési feladatok használata kezelheti az adatmozgás rendszeres időközönként egyszerű JSON-szkript. Az ADF is tartalmaz egyéb szolgáltatásokat, például az összetett műveletek támogatása.
