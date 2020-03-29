---
title: Adatok áthelyezése Azure SQL-adatbázisba – Csapatadat-elemzési folyamat
description: Adatok áthelyezése egysíkú fájlokból (CSV- vagy TSV-formátumok) vagy a helyszíni SQL Server ben tárolt adatokból egy Azure SQL-adatbázisba.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722458"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Adatok áthelyezése egy Azure SQL-adatbázisba az Azure Machine Learning számára

Ez a cikk ismerteti az adatok áthelyezése a sík fájlok (CSV vagy TSV-formátumok) vagy a helyszíni SQL Server egy Azure SQL-adatbázis tárolt adatok. Ezek a feladatok az adatok áthelyezése a felhőbe részét képezik a csapat adatelemzési folyamat.

Az adatok helyszíni SQL Server for Machine Learning szolgáltatásba való áthelyezésének lehetőségeit ismertető témakör az [Adatok áthelyezése az SQL Server kiszolgálóra egy Azure virtuális gépen című témakörben](move-sql-server-virtual-machine.md)olvashat.

Az alábbi táblázat összegzi az Adatok Azure SQL-adatbázisba való áthelyezésének lehetőségeit.

| <b>Forrás</b> | <b>RENDELTETÉSI HELY: Azure SQL-adatbázis</b> |
| --- | --- |
| <b>Sík fájl (CSV vagy TSV formátumú)</b> |[SQL-lekérdezés tömeges beszúrása](#bulk-insert-sql-query) |
| <b>Helyszíni SQL Server</b> |1.[Exportálás egyfájlba](#export-flat-file)<br> 2. [SQL-adatbázis áttelepítése varázsló](#insert-tables-bcp)<br> 3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Előfeltételek
Az itt vázolt eljárások megkövetelik, hogy:

* **Egy Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure-tárfiók.** Az oktatóanyagban található adatok tárolására azure-tárfiókot használ. Ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket. Miután létrehozta a tárfiókot, be kell szereznie a tároló eléréséhez használt fiókkulcsot. Lásd: [Tárfiók hozzáférési kulcsainak kezelése.](../../storage/common/storage-account-keys-manage.md)
* Hozzáférés egy **Azure SQL-adatbázishoz.** Ha be kell állítania egy Azure SQL-adatbázist, [a Microsoft Azure SQL Database első lépései](../../sql-database/sql-database-get-started.md) tájékoztatást nyújt az Azure SQL-adatbázis új példányának kiépítéséről.
* Helyileg telepített és konfigurált **Azure PowerShell.** További információt az [Azure PowerShell telepítése és konfigurálása című témakörben talál.](/powershell/azure/overview)

**Adat**: Az áttelepítési folyamatok a NYC Taxi adatkészlet használatával kerülnek [bemutatásra](https://chriswhong.com/open-data/foil_nyc_taxi/). A NYC Taxi adatkészlet információkat tartalmaz az utazási adatokról és vásárokról, és elérhető az Azure blob storage: [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). A minta és leírása ezeket a fájlokat a [nyc taxi utak adatkészlet leírása](sql-walkthrough.md#dataset).

Az itt leírt eljárásokat saját adataihoz igazíthatja, vagy követheti a NYC Taxi adatkészlet használatával leírt lépéseket. A NYC Taxi adatkészlet nek a helyszíni SQL Server adatbázisba való feltöltéséhez kövesse a [Tömeges adatok importálása az SQL Server adatbázisba](sql-walkthrough.md#dbload)című dokumentumban ismertetett eljárást. Ezek az utasítások egy Azure virtuális gépen lévő SQL Server, de a helyszíni SQL Server-re való feltöltési eljárás ugyanaz.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a>Adatok áthelyezése egy sima fájlforrásból egy Azure SQL-adatbázisba
A sík fájlokban (CSV vagy TSV-formátumú) lévő adatok áthelyezhetők egy Azure SQL-adatbázisba egy tömeges beszúrási SQL-lekérdezés használatával.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>SQL-lekérdezés tömeges beszúrása
Az eljárás lépései a tömeges beszúrása SQL-lekérdezés hasonlóak az adatok áthelyezése egy sima fájlforrásból az SQL Server egy Azure virtuális gép. További információt az [SQL-lekérdezés tömeges beszúrása című](move-sql-server-virtual-machine.md#insert-tables-bulkquery)témakörben talál.

## <a name="moving-data-from-on-premises-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a>Adatok áthelyezése a helyszíni SQL Server kiszolgálóról egy Azure SQL-adatbázisba
Ha a forrásadatok egy helyszíni SQL Server ben tárolódnak, számos lehetőség van az adatok Azure SQL-adatbázisba való áthelyezésére:

1. [Exportálás egyfájlba](#export-flat-file)
2. [SQL-adatbázis áttelepítése varázsló](#insert-tables-bcp)
3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)
4. [Azure-adatgyár](#adf)

Az első három lépései hasonlóak az [adatok áthelyezése az SQL Serverbe egy Azure virtuális gépre,](move-sql-server-virtual-machine.md) amely ugyanezeket az eljárásokat tartalmazza. A témakör megfelelő szakaszaira mutató hivatkozásokat az alábbi utasítások tartalmazják.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportálás egyfájlba
A sima fájlba való exportálás lépései hasonlóak az [Export to Flat File](move-sql-server-virtual-machine.md#export-flat-file)című fájlba című tervben szereplő utasításokhoz.

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>SQL-adatbázis áttelepítése varázsló
Az SQL Database Migration wizard használatának lépései hasonlóak az [SQL Database Migration wizard](move-sql-server-virtual-machine.md#sql-migration)programban található utasításokhoz.

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Adatbázis biztonsági mentése és visszaállítása
Az adatbázis biztonsági mentésének és visszaállításának lépései hasonlóak az [Adatbázis biztonsági mentési és visszaállítási](move-sql-server-virtual-machine.md#sql-backup)segédprogramjában felsorolt utasításokhoz.

### <a name="azure-data-factory"></a><a name="adf"></a>Azure-adatgyár
Ebből a témakörből megtudhatja, hogy miként helyezhet át adatokat egy Azure Data Database (ADF) használatával az Azure Data Factory (ADF) használatával, [amely nek tárja fel az adatokat egy helyszíni SQL-kiszolgálóról az SQL Azure-ba az Azure Data Factory segítségével.](move-sql-azure-adf.md) Ez a témakör bemutatja, hogyan használhatja az ADF segítségével adatok áthelyezése egy helyszíni SQL Server-adatbázis egy Azure SQL-adatbázis az Azure Blob Storage-on keresztül.

Fontolja meg az ADF használatát, ha az adatokat folyamatosan át kell telepíteni hibrid helyszíni és felhőbeli forrásokkal.  Az ADF akkor is segítséget nyújt, ha az adatokátalakításra van szükség, vagy új üzleti logikára van szükség az áttelepítés során. Az ADF lehetővé teszi a feladatok ütemezését és figyelését egyszerű JSON-parancsfájlok használatával, amelyek rendszeres időközönként kezelik az adatok mozgását. Az ADF más képességekkel is rendelkezik, például összetett műveletek támogatásával.
