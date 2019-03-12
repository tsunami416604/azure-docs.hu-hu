---
title: Az Azure-SSIS integrációs modul teljesítményének konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a tulajdonságait, a nagy teljesítményű Azure-SSIS integrációs modul
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 271da0a6ff443fcee28bc870821f4222b3018c91
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576871"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>A nagy teljesítményű, az Azure-SSIS integrációs modul konfigurálása

Ez a cikk ismerteti, hogyan konfigurálhatja egy Azure-SSIS integrációs modul (IR) a magas teljesítmény. Az Azure-SSIS integrációs modul üzembe helyezése és az SQL Server Integration Services (SSIS)-csomagok futtatása az Azure-ban lehetővé teszi. Az Azure-SSIS integrációs modul kapcsolatos további információkért lásd: [integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime) cikk. További információ a központi telepítéséhez és futtatásához az SSIS-csomagok az Azure-ban: [átemeléses SQL Server integrációs szolgáltatások számítási feladatok felhőbe](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ez a cikk a teljesítmény és a belső fejlesztésű tesztelése az SSIS-fejlesztői csapat tagjai által végzett megfigyelések tartalmazza. Az eredmények eltérőek lehetnek. Ezt a saját tesztelési a konfigurációs beállítások, amelyek befolyásolják a költségek és a teljesítmény véglegesítése előtt.

## <a name="properties-to-configure"></a>Tulajdonságok konfigurálása

A konfigurációs parancsfájlt a következő részét, amelyeket konfigurálhat egy Azure-SSIS integrációs modul létrehozásakor tulajdonságait mutatja. A teljes PowerShell-szkript és a leírás, lásd: [üzembe helyezése az SQL Server Integration Services-csomagok az Azure-bA](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** meg az integration runtime feldolgozó csomóponton. A munkavégző csomópont az SSIS-katalógusadatbázist (SSISDB) az Azure SQL-adatbázis állandó kapcsolatot tart fenn. Állítsa be a **AzureSSISLocation** ugyanarra a helyre, amelyen az SSISDB SQL adatbázis-kiszolgálóként, amely lehetővé teszi, hogy az integrációs modul működjön a lehető leghatékonyabb.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
A Data Factory, többek között az Azure-SSIS integrációs modul támogatja a következő beállításokat:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2.

A munkaköröket külsős informatikusok belső tesztjei az SSIS-mérnöki csapata a D sorozat jelenik meg jobban működik, az SSIS-csomag végrehajtása, mint az A sorozatú.

-   A D sorozat a teljesítmény és ár arány, magasabb, mint az A sorozatú.
-   A D-sorozat az átviteli sebesség nagyobb, mint az A sorozatú ugyanazon az áron.

### <a name="configure-for-execution-speed"></a>A végrehajtási sebesség konfigurálása
Ha nem kell futtatni a csomagok számát, és azt szeretné, hogy gyorsan futhassanak-csomagok, az információk segítségével az alábbi táblázat a forgatókönyvnek megfelelő virtuális gép típusának kiválasztása.

Ezeket az adatokat egy egyetlen csomag végrehajtása egy egyetlen munkavégző csomóponton jelöli. A csomag 10 millió rekordot az utónév és a legutóbbi nevet tartalmazó oszlopot tölt be az Azure Blob Storage, egy teljes neve oszlopot hoz létre, és írja a rekordokat, amelyek rendelkeznek a teljes fájlvisszaállítási név hosszabb 20 karakternél, az Azure Blob Storage.

![SSIS integrációs modul csomag végrehajtási sebességét](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Teljes átviteli sebesség beállítása

Ha, örömét fogja lelni csomagot kell futtatni, és az Ön számára legfontosabb általános, az információk segítségével az alábbi táblázat a forgatókönyvnek megfelelő virtuális gép típusának kiválasztása.

![SSIS integrációs modul maximális teljes átviteli sebesség](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** Itt állíthatja be az integrációs modul méretezhetőségét. Az integrációs modul arányos a **AzureSSISNodeNumber**. Állítsa be a **AzureSSISNodeNumber** először egy kisebb értékre, az átviteli sebességet, az integrációs modul monitorozása, majd módosítsa az értékét a forgatókönyvhöz. Konfigurálja újra a munkavégző csomópontok száma, lásd: [egy Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Hatékony munkavégző csomópont használatával már-csomagok futtatása, ha növekvő **AzureSSISMaxParallelExecutionsPerNode** előfordulhat, hogy általános növeléséhez, az integrációs modul. Standard D1 v2 csomópontok 1 – 4 párhuzamos végrehajtások száma csomópontonként támogatottak. A csomópontok minden más típusú 1 – 8 párhuzamos végrehajtások száma csomópontonként támogatottak.
A megfelelő értéket a csomag és a következő konfigurációk a munkavégző csomópontok alapján meg tudja becsülni. További információkért lásd: [általános célú virtuális gépek méretei](../virtual-machines/windows/sizes-general.md).

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Adatlemezek max. száma / átviteli sebesség: IO | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |

Az alábbiakban az irányelvek a megfelelő értéket a **AzureSSISMaxParallelExecutionsPerNode** tulajdonság: 

1. Állítsa be, először egy kisebb értékre.
2. Ellenőrizze, hogy a teljes átviteli sebesség növelése kis mértékben nő.
3. Állítsa le az érték növelése, ha általános eléri a maximális értéket.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** az SSIS-katalógusadatbázist (SSISDB) az Azure SQL-adatbázis tarifacsomagjának van. Ez a beállítás hatással van a végrehajtási naplóból betöltése az integrációs modul példányt, a várólistára-csomag végrehajtása, és a sebesség a feldolgozók maximális száma.

-   Ha nem érdeklik a sebesség, várólista-csomag végrehajtása és betölteni a végrehajtási naplóból, kiválaszthatja a legalacsonyabb az adatbázis tarifacsomagjának. Az Azure SQL Database az alapszintű díjszabás az integrációsmodul-példány 8 feldolgozó támogatja.

-   Az alapszintűnél nagyobb teljesítményű adatbázis akkor válassza, ha a feldolgozók száma több mint 8, vagy a magok száma 50-nél nagyobb. Ellenkező esetben az adatbázis az integration runtime-példány szűk keresztmetszetté válik, és legyen negatív hatással az általános teljesítménye.

Is beállíthatja az adatbázis alapján tarifacsomag [adatbázis-tranzakciós egységek](../sql-database/sql-database-what-is-a-dtu.md) (DTU) használati adatokhoz az Azure Portalon.

## <a name="design-for-high-performance"></a>Nagy teljesítményű rendszer tervezése
Tervezése futtathatják az Azure-SSIS-csomag eltér a helyi végrehajtásra csomag tervezése. Ugyanaz a csomag több független feladatokat ötvöző helyett válassza el őket, több csomagot az hatékonyabb végrehajtása az Azure-SSIS integrációs modult. Hozzon létre egy csomag végrehajtása az egyes csomagok, így nem kell egymással a befejezéshez várja. Ez a megközelítés számos előnyt biztosít az az Azure-SSIS integrációs modul méretezhetőségét, és javítja az általános.

## <a name="next-steps"></a>További lépések
További információ az Azure-SSIS integrációs modul. Lásd: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).
