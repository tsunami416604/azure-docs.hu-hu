---
title: Helyszíni SSIS számítási feladatok migrálása a Azure Data Factoryba való SSIS | Microsoft Docs
description: Telepítse át a helyszíni SSIS számítási feladatokat az ADF-SSIS.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 3bf5ddebd59c95d00d0d3270f0e8e1a2d29b379a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968470"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Helyszíni SSIS számítási feladatok migrálása az ADF-SSIS

## <a name="overview"></a>Áttekintés

Amikor áttelepíti az adatbázis-munkaterheléseket a helyszíni SQL Serverról az Azure Database Servicesre, azaz Azure SQL Database vagy Azure SQL Database felügyelt példányra, az ETL számítási feladatait a SQL Server Integration Services (SSIS) az elsődleges értékkel kiegészítve a szolgáltatásokat is át kell telepíteni.

A Azure Data Factory (ADF) Azure-SSIS Integration Runtime (IR) támogatja a SSIS-csomagok futtatását. A Azure-SSIS IR kiépítése után a csomagok üzembe helyezéséhez és futtatásához használhatja a jól ismert eszközöket, például a SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) és a parancssori segédeszközöket, például a dtinstall/dtutil/dtexec. További információ: az [Azure SSIS lift-and-SHIFT áttekintése](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Ebből a cikkből megtudhatja, hogy a helyszíni SSIS származó ETL számítási feladatok áttelepítési folyamata az ADF-ben SSIS. Az áttelepítési folyamat két fázisból áll: **Értékelés** és **áttelepítés**.

## <a name="assessment"></a>Értékelés

A teljes áttelepítési terv létrehozásához alapos értékelés segít azonosítani a forrásként szolgáló SSIS-csomagokkal kapcsolatos problémákat, amelyek megakadályozhatják a sikeres áttelepítést.

A Data Migration Assistant (DMA) egy ingyenesen letölthető eszköz erre a célra, amelyet helyileg lehet telepíteni és végrehajtani. Az **integrációs szolgáltatások** típusú DMA-értékelési projekt hozható létre a kötegekben lévő SSIS-csomagok értékeléséhez, valamint a következő kategóriákban bemutatott kompatibilitási problémák azonosításához:

- Áttelepítési blokkolók: Ezek olyan kompatibilitási problémák, amelyek letiltják az áttelepítési forrás csomagjainak futtatását Azure-SSIS IRon. A DMA útmutatást nyújt a problémák megoldásához.

- Informatív problémák: Ezek részben támogatott vagy elavult funkciók, amelyek a forrás csomagjaiban használatosak. A DMA a javaslatok átfogó készletét, az Azure-ban elérhető alternatív megközelítéseket és a megoldási lépések enyhítését teszi lehetővé.

### <a name="four-storage-types-for-ssis-packages"></a>Négy tárolási típus a SSIS-csomagokhoz

- SSIS-katalógus (SSISDB). Ez a SQL Server 2012-es kiadásban jelent meg, és a SSIS-projektek/csomagok kezeléséhez használt tárolt eljárásokat, nézeteket és táblázat értékű függvényeket tartalmaz.
- Fájlrendszer.
- SQL Server rendszeradatbázis (MSDB).
- SSIS-csomag tárolója. Ez a csomag-felügyeleti réteg két altípuson felül van:
  - A MSDB, amely a SSIS-csomagok tárolására használt SQL Server rendszeradatbázisa.
  - Felügyelt fájlrendszer, amely a SSIS-csomagok tárolására szolgáló SQL Server telepítési útvonal adott mappája.

A DMA jelenleg támogatja a **fájlrendszerbeli** tárolóban tárolt csomagok kötegelt értékelését a **DMA v 4.5 verzió**óta.

Szerezze be a [DMA](https://docs.microsoft.com/sql/dma/dma-overview)-t, és [végezze el a csomag értékelését](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Áttelepítés

[A forrás](#four-storage-types-for-ssis-packages) SSIS csomagjainak és az adatbázis-munkaterhelések áttelepítési célhelyének függvényében a **SSIS-csomagok** áttelepítésének lépései, valamint a SSIS-csomagok végrehajtásának elvégzésére szolgáló **SQL Server Agent feladatok** eltérőek lehetnek. Két forgatókönyv létezik:

- [**Felügyelt példány Azure SQL Database** adatbázis-munkaterhelés célhelyként](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** adatbázis-munkaterhelés célhelyként](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Felügyelt példány Azure SQL Database** adatbázis-munkaterhelés célhelyként

| **Csomag tárolási típusa** |SSIS-csomagok áttelepítésének módja|SSIS-feladatok kötegelt áttelepítésének módja|
|-|-|-|
|SSISDB|[**SSISDB** migrálása](scenario-ssis-migration-ssisdb-mi.md)|[SSIS-feladatok migrálása Azure SQL Database felügyelt példány ügynökének](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Fájlrendszer|Az dtinstall/dtutil/manuális másolással, vagy a fájlrendszerben a VNet/saját üzemeltetésű integrációs modulon keresztüli eléréshez való hozzáférés érdekében telepítse újra azokat a fájlmegosztás/Azure Files számára. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportálja őket a fájlrendszerekbe/fájlmegosztásba/Azure Filesba a SSMS/dtutil használatával. További információ: SSIS- [csomagok exportálása](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Package Store|Exportálja őket a fájlrendszerek/fájlmegosztás/Azure Files SSMS/dtutil keresztül, vagy telepítse azokat a fájlmegosztás/Azure Files a dtinstall/dtutil/manuális másolással, vagy tartsa azokat a fájlrendszerekben a VNet/saját üzemeltetésű integrációs modulon keresztül való hozzáféréshez. További információ: dtutil segédprogram. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** adatbázis-munkaterhelés célhelyként

| **Csomag tárolási típusa** |SSIS-csomagok áttelepítésének módja|Feladatok áttelepítésének módja|
|-|-|-|
|SSISDB|Újból üzembe helyezhető az Azure-SSISDB a SSDT/SSMS használatával. További információ: [SSIS-csomagok üzembe helyezése az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Fájlrendszer|Az dtinstall/dtutil/manuális másolással, vagy a fájlrendszerben a VNet/saját üzemeltetésű integrációs modulon keresztüli eléréshez való hozzáférés érdekében telepítse újra azokat a fájlmegosztás/Azure Files számára. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportálja őket a fájlrendszerekbe/fájlmegosztásba/Azure Filesba a SSMS/dtutil használatával. További információ: SSIS- [csomagok exportálása](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Package Store|Exportálja őket a fájlrendszerek/fájlmegosztás/Azure Files SSMS/dtutil keresztül, vagy telepítse azokat a fájlmegosztás/Azure Files a dtinstall/dtutil/manuális másolással, vagy tartsa azokat a fájlrendszerekben a VNet/saját üzemeltetésű integrációs modulon keresztül való hozzáféréshez. További információ: dtutil segédprogram. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>További források

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [A SSIS számítási feladatainak átemelése és áthelyezése a felhőbe](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS-csomagok migrálása Azure SQL Database felügyelt példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Csomagok újbóli üzembe helyezése Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>További lépések

- [Az Azure-ba telepített SSIS-csomagok ellenőrzése](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Az Azure-ban üzembe helyezett SSIS-csomagok futtatása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Az Azure-SSIS Integration Runtime monitorozása](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [SSIS-csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
