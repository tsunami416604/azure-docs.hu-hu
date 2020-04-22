---
title: Helyszíni SSIS-számítási feladatok áttelepítése ssis-be az Azure Data Factoryban
description: Telepítse át a helyszíni SSIS-számítási feladatokat az ADF-ben található SSIS-be.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 2b23ffec76de3fa644abe3b65876a60c65c05eb8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686006"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Helyszíni SSIS-számítási feladatok áttelepítése az SSIS-be az ADF-ben

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Áttekintés

Amikor az adatbázis-számítási feladatokat a helyszíni SQL Serverről az Azure-adatbázis-szolgáltatásokba, nevezetesen az Azure SQL Database vagy az Azure SQL Database felügyelt példányába telepíti át, az ETL-számítási feladatokat az SQL Server Integration Services (SSIS) szolgáltatásban, mivel az elsődleges értéknövelt szolgáltatások egyikét is át kell telepíteni.

Az Azure-SSIS-integrációs futtatótime (IR) az Azure Data Factoryban (ADF) támogatja az SSIS-csomagok futtatását. Az Azure-SSIS IR kiépítése után ismerős eszközöket használhat, például az SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) és a parancssori segédprogramokat, például a dtinstall/dtutil/dtexec-et a csomagok Azure-ban való üzembe helyezéséhez és futtatásához. További információ: [Azure SSIS lift-and-shift overview](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Ez a cikk kiemeli az ETL-számítási feladatok áttelepítési folyamatát a helyszíni SSIS-ről az ADF-ben lévő SSIS-re. Az áttelepítési folyamat két szakaszból áll: **Értékelés** és **Áttelepítés**.

## <a name="assessment"></a>Értékelés

A teljes áttelepítési terv létrehozásához egy alapos értékelés segít azonosítani a forrás SSIS-csomagokkal kapcsolatos problémákat, amelyek megakadályozzák a sikeres áttelepítést.

Az adatáttelepítési segéd (DMA) egy szabadon letölthető eszköz erre a célra, amely helyileg telepíthető és végrehajtható. **Az Integrációs szolgáltatások** típusú DMA-felmérési projekt létrehozható az SSIS-csomagok kötegekben való értékelésére és a következő kategóriákban bemutatott kompatibilitási problémák azonosítására:

- Áttelepítés-blokkolók: Ezek olyan kompatibilitási problémák, amelyek blokkolják az Azure-SSIS IR-en futtatandó áttelepítési forráscsomagokat. A DMA útmutatást nyújt a problémák megoldásához.

- Informatív problémák: Ezek részben támogatott vagy elavult funkciók, amelyek et a forráscsomagokban használják. A DMA átfogó javaslatokat, az Azure-ban elérhető alternatív megközelítéseket és a megoldáshoz szükséges enyhítő lépéseket biztosít.

### <a name="four-storage-types-for-ssis-packages"></a>Négy tárolási típus SSIS-csomagokhoz

- SSIS katalógus (SSISDB). Ez az SQL Server 2012-vel lett bevezetve, és az SSIS-projektek/csomagok hoz használt tárolt eljárásokat, nézeteket és táblaértékű függvényeket tartalmazza.
- fájlrendszer.
- SQL Server rendszeradatbázis (MSDB).
- SSIS csomagtároló. Ez egy csomagkezelési réteg, amely két altípuson alapul:
  - MSDB, amely az SQL Server rendszeradatbázisa, amely et SSIS-csomagok tárolására használják.
  - Felügyelt fájlrendszer, amely az SQL Server telepítési útvonalának egy adott mappája, amely az SSIS-csomagok tárolására szolgál.

A DMA jelenleg támogatja a **Fájlrendszerben**, a **Csomagtárolóban**és az **SSIS katalógusban** tárolt csomagok kötegelt felmérését a **DMA 5.0-s verziója**óta .

Get [DMA](https://docs.microsoft.com/sql/dma/dma-overview), és [végezze el a csomag értékelése vele.](https://docs.microsoft.com/sql/dma/dma-assess-ssis)

## <a name="migration"></a>Migrálás

A forrás SSIS-csomagok [tárolási típusától](#four-storage-types-for-ssis-packages) és az adatbázis-számítási feladatok áttelepítési céljától függően az **SSIS-csomagok** és az SSIS-csomagok végrehajtását ütemező **SQL Server Agent-feladatok** áttelepítésének lépései eltérőek lehetnek. Két forgatókönyv létezik:

- [**Az Azure SQL Database felügyelt példánya** adatbázis-számítási feladat célként](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Az Azure SQL Database** adatbázis-számítási feladatok célhelyeként](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Az Azure SQL Database felügyelt példánya** adatbázis-számítási feladat célként

| **Csomag tárolási típusa** |Az SSIS-csomagok kötegelt áttelepítése|Az SSIS-feladatok kötegelt áttelepítése|
|-|-|-|
|SSISDB|[**SSISDB** áttelepítése](scenario-ssis-migration-ssisdb-mi.md)|[SSIS-feladatok áttelepítése az Azure SQL Database felügyelt példányügynökébe](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Fájlrendszer|Telepítse őket fájlmegosztásra/Azure Files-ra a dtinstall/dtutil/manuális másolással, vagy tartsa a fájlrendszerekben a virtuális hálózaton/saját üzemeltetésű infravörös hálózaton keresztüli hozzáféréshez. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Áttelepítés [az SSIS-feladatáttelepítés varázslóval az SSMS-ben](how-to-migrate-ssis-job-ssms.md) <li>Alakítsa át őket ADF-folyamatokká/tevékenységekké/eseményindítókká parancsfájlok/SSMS/ADF portál on keresztül. További információ: [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportálja őket fájlrendszerekbe/fájlmegosztásokba/Azure Files-ba SSMS/dtutil segítségével. További információ: [SSIS-csomagok exportálása.](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)|Alakítsa át őket ADF-folyamatokká/tevékenységekké/eseményindítókká parancsfájlok/SSMS/ADF portál on keresztül. További információ: [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Csomagtároló|Exportálja őket fájlrendszerekbe/fájlmegosztásokba/Azure-fájlokba SSMS/dtutil on keresztül, vagy telepítse újra őket fájlmegosztásokra/Azure Files-ra a dtinstall/dtutil/manual copy segítségével, vagy tartsa őket fájlrendszerekben a Virtuálishálózaton/Önkiszolgáló infravörös hálózaton keresztüli eléréséhez. További információ: dtutil segédprogram. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Alakítsa át őket ADF-folyamatokká/tevékenységekké/eseményindítókká parancsfájlok/SSMS/ADF portál on keresztül. További információ: [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Az Azure SQL Database** adatbázis-számítási feladatok célhelyeként

| **Csomag tárolási típusa** |Az SSIS-csomagok kötegelt áttelepítése|Kötegelt áttelepítési feladatok|
|-|-|-|
|SSISDB|Telepítse újra az Azure-SSISDB-re SSDT/SSMS-en keresztül. További információ: [SSIS-csomagok telepítése az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)|Alakítsa át őket ADF-folyamatokká/tevékenységekké/eseményindítókká parancsfájlok/SSMS/ADF portál on keresztül. További információ: [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Fájlrendszer|Telepítse őket fájlmegosztásra/Azure Files-ra a dtinstall/dtutil/manuális másolással, vagy tartsa a fájlrendszerekben a virtuális hálózaton/saját üzemeltetésű infravörös hálózaton keresztüli hozzáféréshez. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Áttelepítés [az SSIS-feladatáttelepítés varázslóval az SSMS-ben](how-to-migrate-ssis-job-ssms.md) <li> Alakítsa át őket ADF-folyamatokká/tevékenységekké/eseményindítókká parancsfájlok/SSMS/ADF portál on keresztül. További információ: [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportálja őket fájlrendszerekbe/fájlmegosztásokba/Azure Files-ba SSMS/dtutil segítségével. További információ: [SSIS-csomagok exportálása.](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)|Alakítsa át őket ADF-folyamatokká/tevékenységekké/eseményindítókká parancsfájlok/SSMS/ADF portál on keresztül. További információ: [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Csomagtároló|Exportálja őket fájlrendszerekbe/fájlmegosztásokba/Azure-fájlokba SSMS/dtutil on keresztül, vagy telepítse újra őket fájlmegosztásokra/Azure Files-ra a dtinstall/dtutil/manual copy segítségével, vagy tartsa őket fájlrendszerekben a Virtuálishálózaton/Önkiszolgáló infravörös hálózaton keresztüli eléréséhez. További információ: dtutil segédprogram. További információ: [dtutil segédprogram](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Alakítsa át őket ADF-folyamatokká/tevékenységekké/eseményindítókká parancsfájlok/SSMS/ADF portál on keresztül. További információ: [SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>További források

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Adatbázis-áttelepítési segéd](https://docs.microsoft.com/sql/dma/dma-overview)
- [Az SSIS-munkaterhelések felemelése és felhőbe való átcsoportosítása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS-csomagok migrálása felügyelt Azure SQL Database-példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Csomagok újratelepítése az Azure SQL Database-be](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>További lépések

- [Az Azure-ba telepített SSIS-csomagok ellenőrzése](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Az Azure-ban telepített SSIS-csomagok futtatása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Az Azure-SSIS-integrációs futásidő figyelése](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [SSIS-csomagok végrehajtásának ütemezése az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
