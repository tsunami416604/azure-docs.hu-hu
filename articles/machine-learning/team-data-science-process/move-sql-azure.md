---
title: Adatok áthelyezése az Azure SQL Database az Azure Machine Learning |} Microsoft Docs
description: SQL-tábla és az adatok betöltése az SQL-tábla létrehozása
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2018
ms.author: deguhath
ms.openlocfilehash: ce349aedc6b733d34ab61eb2e23b378727e01800
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Adatok áthelyezése egy Azure SQL-adatbázisba az Azure Machine Learning számára
Ez a témakör bemutatja a beállítások megköveteli az adatok strukturálatlan fájlból (CSV vagy TSV formátumban), vagy az Azure SQL-adatbázis egy helyi SQL Server tárolt adatok. Ezeket a feladatokat az adatok áthelyezését a felhőbe az Team tudományos folyamat részét képezik.

Ez a témakör bemutatja a Machine Learning megköveteli az adatok egy helyi SQL Server-beállításait, lásd: [adatok áthelyezése az SQL Server Azure virtuális géphez](move-sql-server-virtual-machine.md).

A következő **menü** betöltik az adatokat tároló környezetekben, ahol az adatok is tárolhatók és feldolgozhatók, a csapat adatok tudományos folyamat (TDSP) során módját leíró témakörök hivatkozásait.

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

A következő táblázat összefoglalja az adatok áthelyezése az Azure SQL Database beállításai.

| <b>FORRÁS</b> | <b>CÉL: Az Azure SQL-adatbázis</b> |
| --- | --- |
| <b>Egybesimított fájl (CSV vagy formázott TSV)</b> |[A tömeges beszúrás SQL-lekérdezés](#bulk-insert-sql-query) |
| <b>A helyszíni SQL Server</b> |1.[Egybesimított fájl exportálása](#export-flat-file)<br> 2. [SQL-adatbázis áttelepítése varázsló](#insert-tables-bcp)<br> 3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Előfeltételek
Az itt leírt eljárások szükség van:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure storage-fiók**. Egy Azure storage-fiókot használ az adatok tárolása az oktatóanyag. Ha egy Azure storage-fiók nem rendelkezik, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account) cikk. Miután létrehozta a tárfiókot, be kell szereznie a tároló elérésére használt fiókot kulcs. Lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* A hozzáférést egy **Azure SQL adatbázis**. Ha be kell állítania egy Azure SQL Database [Ismerkedés a Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) bemutatja, hogy miként lehet kiépíteni egy Azure SQL adatbázis új példányát.
* Telepített és konfigurált **Azure PowerShell** helyileg. Útmutatásért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

**Adatok**: az áttelepítési folyamat egy használatával a [NYC Taxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/). A következőt: Taxi dataset út adatokkal és vásárokon információkat tartalmazza, és elérhető az Azure blob storage: [NYC Taxi adatok](http://www.andresmh.com/nyctaxitrips/). A minta és az ezek a fájlok leírása szerepelnek [NYC Taxi Utazgatással adatkészlet leírása](sql-walkthrough.md#dataset).

A saját adatok az itt leírt eljárásokat igazítja, vagy hajtsa végre a következőt: Taxi adatkészlet szerint. Töltse fel a következőt: Taxi dataset a helyszíni SQL Server-adatbázisba, kövesse az ismertetett eljárás [tömeges adatok importálása az SQL Server-adatbázisba](sql-walkthrough.md#dbload). Ezek az utasítások az Azure virtuális gép az SQL Server, de a helyszíni SQL Server feltöltését eljárás megegyezik.

## <a name="file-to-azure-sql-database"></a> Egybesimított fájl forrásból származó adatok Azure SQL-adatbázis áthelyezése
A tömeges beszúrás SQL lekérdezést használva Azure SQL-adatbázis (CSV vagy TSV formázott) egybesimított fájlok adatainak helyezheti át.

### <a name="bulk-insert-sql-query"></a> A tömeges beszúrás SQL-lekérdezés
A tömeges beszúrás SQL lekérdezést használva eljárás lépései hasonlóak az adatok áthelyezése egy egybesimított fájl forrásból SQL Server egy Azure virtuális gépen szakaszaiban ismertetett. További információkért lásd: [tömeges beszúrás SQL-lekérdezés](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Adatok áthelyezése a helyszíni SQL Server az Azure SQL-adatbázis
Az adatok egy helyi SQL Server tárolja, ha van az adatok áthelyezése az Azure SQL Database különböző lehetőségeit:

1. [Egybesimított fájl exportálása](#export-flat-file)
2. [SQL-adatbázis áttelepítése varázsló](#insert-tables-bcp)
3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)
4. [Azure Data Factory](#adf)

Az első három lépésekre nagyon hasonló, így ezek a szakaszok a [adatok áthelyezése az SQL Server Azure virtuális géphez](move-sql-server-virtual-machine.md) , amelyek ugyanezeket az eljárásokat vonatkoznak. A témakör megfelelő részeiben mutató hivatkozásokat tartalmaz az alábbi utasítások szerepelnek.

### <a name="export-flat-file"></a>Egybesimított fájl exportálása
Ez egy egyszerű fájlba exportálása lépései hasonlóak a kezelt [Egybesimított fájl exportálása](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>SQL-adatbázis áttelepítése varázsló
Az SQL-adatbázis áttelepítése varázsló használatához vezető lépések hasonlóak a kezelt [SQL-adatbázis áttelepítése varázsló](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Adatbázis biztonsági mentése és visszaállítása
A lépések adatbázis biztonsági mentése és visszaállítása hasonlóak a kezelt [adatbázis biztonsági mentése és visszaállítása](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Az Azure Data Factory
Az adatok áthelyezése az Azure SQL adatbázis Azure Data Factory (ADF) eljárását a következő témakörben találhatók [tárolt adatok mozgatása egy helyi SQL server SQL Azure és az Azure Data Factory](move-sql-azure-adf.md). Ez a témakör bemutatja, hogyan tárolt adatok mozgatása egy helyi SQL Server-adatbázis Azure SQL adatbázis Azure Blob Storage segítségével ADF használatával.

Érdemes lehet ADF Ha adatokat folyamatosan telepíthető egy hibrid forgatókönyvben, aki hozzáfér a helyszíni és felhőalapú erőforrásaihoz, és az adatok tranzakcióalapú van, vagy módosítani, vagy üzleti logikát, ha az áttelepítés alatt álló hozzáadott rendelkezik. ADF lehetővé teszi, hogy az ütemezés és rendszeres időközönként adatok mozgása kezelő egyszerű JSON-parancsfájlokat használó feladatok figyelését. ADF is rendelkeznek egyéb képességeit, például a összetett műveletek támogatása.
