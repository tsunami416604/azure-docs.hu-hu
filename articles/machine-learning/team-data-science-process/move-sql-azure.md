---
title: Adatáthelyezés Azure SQL Database-csoportos adatelemzési folyamatba
description: Az adatok az egyszerű fájlokból (CSV vagy TSV formátumokból) vagy egy helyszíni SQL Server tárolt adatokból egy Azure SQL Database-adatbázisba helyezhetők át.
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
ms.openlocfilehash: 79ef5a6e4af3e0becc5dbeed9ea377e8ef0eb3d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432154"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Adatok áthelyezése egy Azure SQL-adatbázisba az Azure Machine Learning számára

Ez a cikk az adatok sima fájlokból (CSV vagy TSV formátumokból) vagy egy helyszíni SQL Server tárolt adatokból az Azure SQL Database-be történő áthelyezésének lehetőségeit ismerteti. Az adatok felhőbe való áthelyezésével kapcsolatos feladatok a csoportos adatelemzési folyamat részét képezik.

Egy olyan témakörben, amely az adatáthelyezési lehetőségeket ismerteti egy helyszíni SQL Server Machine Learning számára, tekintse meg az [Azure-beli virtuális gépeken az adatáthelyezés SQL Serverre](move-sql-server-virtual-machine.md)című témakört.

A következő táblázat összefoglalja az adatáthelyezési beállításokat egy Azure SQL Databaseba.

| <b>FORRÁS</b> | <b>CÉL: Azure SQL Database</b> |
| --- | --- |
| <b>Sima fájl (CSV vagy TSV formázva)</b> |[SQL-lekérdezés tömeges beszúrása](#bulk-insert-sql-query) |
| <b>Helyszíni SQL Server</b> |1.[Exportálás a lapos fájlba](#export-flat-file)<br> 2. [SQL Database áttelepítési varázsló](#insert-tables-bcp)<br> 3. [az adatbázis biztonsági mentése és visszaállítása](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Előfeltételek
Az itt leírt eljárásokhoz a következők szükségesek:

* **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure Storage-fiók**. Az ebben az oktatóanyagban tárolt adattároláshoz Azure Storage-fiókot használunk. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-quickstart-create-account.md) című cikket. A Storage-fiók létrehozása után be kell szereznie a tárolóhoz való hozzáféréshez használt fiók kulcsát. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).
* Hozzáférés egy **Azure SQL Databasehoz**. Ha be kell állítania egy Azure SQL Databaset, [első lépések a Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) a Azure SQL Database új példányának kiépítésével kapcsolatos információkat tartalmaz.
* **Azure PowerShell** helyileg telepítve és konfigurálva. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

**Adat**: az áttelepítési folyamatokat a [New York-i taxi adatkészlet](https://chriswhong.com/open-data/foil_nyc_taxi/)mutatja be. A New York-i taxi adatkészlet az utazási adatokról és a vásárokról tartalmaz információkat, és az Azure Blob Storage-ban érhető el: a [NYC-taxi adatai](https://www.andresmh.com/nyctaxitrips/). Ezen fájlok mintáját és leírását a [New York-i taxis adatkészletének leírásában](sql-walkthrough.md#dataset)ismertetjük.

Az itt ismertetett eljárásokat a saját adataihoz igazíthatja, vagy a New York-i taxi adatkészletének használatával követheti a leírt lépéseket. A New York-i taxi-adatkészlet helyszíni SQL Server adatbázisba való feltöltéséhez kövesse az [adatok tömeges importálása SQL Server-adatbázisba](sql-walkthrough.md#dbload)című szakaszban leírt eljárást. Ezek az utasítások az Azure-beli virtuális gépek SQL Serverére vonatkoznak, de a helyszíni SQL Server való feltöltésének eljárása azonos.

## <a name="file-to-azure-sql-database"></a>Adatok áthelyezése egy egyszerű fájlból egy Azure SQL Database-adatbázisba
Az egyszerű fájlokban (CSV vagy TSV formátumú) tárolt adat áthelyezhető egy Azure SQL Database-be egy tömeges INSERT SQL-lekérdezés használatával.

### <a name="bulk-insert-sql-query"></a>SQL-lekérdezés tömeges beszúrása
A tömeges beszúrási SQL-lekérdezést használó eljárás lépései hasonlóak az Azure-beli virtuális gépeken SQL Server az adatok egy sima fájlból való áthelyezéséhez. További részletek: [SQL-lekérdezés tömeges beszúrása](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Adatok áthelyezése helyszíni SQL Serverból egy Azure SQL Database-adatbázisba
Ha a forrásadatok tárolása helyszíni SQL Server történik, számos lehetőség áll rendelkezésre az Azure SQL Database-adatbázisba való áthelyezésre:

1. [Exportálás az egyszerű fájlba](#export-flat-file)
2. [SQL Database áttelepítési varázsló](#insert-tables-bcp)
3. [Adatbázis biztonsági mentése és visszaállítása](#db-migration)
4. [Azure Data Factory](#adf)

Az első három lépések nagyon hasonlóak az [adatáthelyezési műveletek SQL Server egy Azure-beli virtuális gépen](move-sql-server-virtual-machine.md) , amely ezekre az eljárásokra vonatkozik. Az ebben a témakörben található megfelelő szakaszokra mutató hivatkozások az alábbi utasításokban találhatók.

### <a name="export-flat-file"></a>Exportálás az egyszerű fájlba
A lapos fájlba való exportálás lépései hasonlóak az [Exportálás a lapos fájlba](move-sql-server-virtual-machine.md#export-flat-file)művelethez.

### <a name="insert-tables-bcp"></a>SQL Database áttelepítési varázsló
A SQL Database áttelepítési varázsló használatának lépései hasonlóak a [SQL Database Áttelepítési varázslóban](move-sql-server-virtual-machine.md#sql-migration)szereplőknek.

### <a name="db-migration"></a>Adatbázis biztonsági mentése és visszaállítása
Az adatbázis biztonsági mentésének és visszaállításának lépései hasonlóak az [adatbázis biztonsági mentésére és visszaállítására](move-sql-server-virtual-machine.md#sql-backup)vonatkozó lépésekhez.

### <a name="adf"></a>Azure Data Factory
Az adatok Azure SQL Database-adatbázisba Azure Data Factory (ADF) való áthelyezésének eljárását a következő témakör ismerteti: az [adatok áthelyezése egy helyszíni SQL Server-kiszolgálóról a Azure Data Factory-SQL Azureba](move-sql-azure-adf.md). Ez a témakör bemutatja, hogyan helyezhet át egy helyszíni SQL Server-adatbázisból egy Azure SQL-adatbázisba az ADF használatával az Azure Blob Storage segítségével.

A helyszíni és a Felhőbeli erőforrásokhoz egyaránt hozzáférő hibrid forgatókönyvekben, valamint az adatfeldolgozás során, illetve az áttelepítés során az üzleti logikával való kommunikációt követően érdemes az ADF használatát használni. Az ADF lehetővé teszi a feladatok ütemezését és figyelését olyan egyszerű JSON-parancsfájlokkal, amelyek rendszeres időközönként kezelik az adatok áthelyezését. Az ADF más képességekkel is rendelkezik, mint például az összetett műveletek támogatása.
