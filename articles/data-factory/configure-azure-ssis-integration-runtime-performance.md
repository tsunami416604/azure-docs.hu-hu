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
ms.openlocfilehash: 42c69653a002446552da998320a43730dfdaadf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232540"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>A nagy teljesítményű, az Azure-SSIS integrációs modul konfigurálása

Ez a cikk ismerteti, hogyan konfigurálhatja egy Azure-SSIS integrációs modul (IR) a magas teljesítmény. Az Azure-SSIS integrációs modul üzembe helyezése és az SQL Server Integration Services (SSIS)-csomagok futtatása az Azure-ban lehetővé teszi. Az Azure-SSIS integrációs modul kapcsolatos további információkért lásd: [integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime) cikk. További információ a központi telepítéséhez és futtatásához az SSIS-csomagok az Azure-ban: [átemeléses SQL Server integrációs szolgáltatások számítási feladatok felhőbe](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ez a cikk a teljesítmény és a belső fejlesztésű tesztelése az SSIS-fejlesztői csapat tagjai által végzett megfigyelések tartalmazza. Az eredmények eltérőek lehetnek. Ezt a saját tesztelési a konfigurációs beállítások, amelyek befolyásolják a költségek és a teljesítmény véglegesítése előtt.

## <a name="properties-to-configure"></a>Tulajdonságok konfigurálása

A konfigurációs parancsfájlt a következő részét, amelyeket konfigurálhat egy Azure-SSIS integrációs modul létrehozásakor tulajdonságait mutatja. A teljes PowerShell-szkript és a leírás, lásd: [üzembe helyezése az SQL Server Integration Services-csomagok az Azure-bA](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
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
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

A munkaköröket külsős informatikusok belső tesztjei az SSIS-mérnöki csapata a D sorozat jelenik meg jobban működik, az SSIS-csomag végrehajtása, mint az A sorozatú.

-   A teljesítmény és ár arány, a D sorozat nagyobb, mint az A sorozatú és a teljesítmény és ár arány a v3-as sorozat, magasabb, mint a v2 sorozat.
-   Az átviteli sebességet, a D sorozat nagyobb, mint az A sorozatú ugyanazon az áron pedig az átviteli sebességet a v3-as sorozat nagyobb, mint a v2 sorozat ugyanazon az áron.
-   Az Azure-SSIS integrációs modul v2 sorozat csomópontjai nem alkalmasak egyéni telepítés, ezért használja inkább a v3-as sorozat csomópontjai. Ha már használja a v2 sorozat csomópontjai, váltson a v3-as sorozat csomópontjai minél hamarabb használja.
-   A E-sorozat képviseli, nagyobb memória – Processzor memóriaarányt, mint a többi gép biztosító Virtuálisgép-méretek a memóriaoptimalizált. Ha a csomag igényel sok memóriát, érdemes lehet E sorozatú virtuális gépek kiválasztása.

### <a name="configure-for-execution-speed"></a>A végrehajtási sebesség konfigurálása
Ha nem kell futtatni a csomagok számát, és azt szeretné, hogy gyorsan futhassanak-csomagok, az információk segítségével az alábbi táblázat a forgatókönyvnek megfelelő virtuális gép típusának kiválasztása.

Ezeket az adatokat egy egyetlen csomag végrehajtása egy egyetlen munkavégző csomóponton jelöli. A csomag 3 millió rekordot az utónév és a legutóbbi nevet tartalmazó oszlopot tölt be az Azure Blob Storage, létrehoz egy teljes neve oszlop, és írja a rekordokat, amelyek rendelkeznek a teljes fájlvisszaállítási név hosszabb 20 karakternél, az Azure Blob Storage.

![SSIS integrációs modul csomag végrehajtási sebességét](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Teljes átviteli sebesség beállítása

Ha, örömét fogja lelni csomagot kell futtatni, és az Ön számára legfontosabb általános, az információk segítségével az alábbi táblázat a forgatókönyvnek megfelelő virtuális gép típusának kiválasztása.

![SSIS integrációs modul maximális teljes átviteli sebesség](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** Itt állíthatja be az integrációs modul méretezhetőségét. Az integrációs modul arányos a **AzureSSISNodeNumber**. Állítsa be a **AzureSSISNodeNumber** először egy kisebb értékre, az átviteli sebességet, az integrációs modul monitorozása, majd módosítsa az értékét a forgatókönyvhöz. Konfigurálja újra a munkavégző csomópontok száma, lásd: [egy Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Hatékony munkavégző csomópont használatával már-csomagok futtatása, ha növekvő **AzureSSISMaxParallelExecutionsPerNode** előfordulhat, hogy általános növeléséhez, az integrációs modul. Standard D1 v2 csomópontok 1 – 4 párhuzamos végrehajtások száma csomópontonként támogatottak. A csomópontok minden más típusú 1 – max(2 x number of cores, 8) párhuzamos végrehajtások száma csomópontonként támogatottak. Ha azt szeretné, **AzureSSISMaxParallelExecutionsPerNode** meghaladja a maximális értéknél, hogy a támogatott, nyithat egy támogatási jegyet, és növelhetjük a maximális érték, és után, amely az Azure Powershell használatával frissíteni kell  **AzureSSISMaxParallelExecutionsPerNode**.
A megfelelő értéket a csomag és a következő konfigurációk a munkavégző csomópontok alapján meg tudja becsülni. További információkért lásd: [általános célú virtuális gépek méretei](../virtual-machines/windows/sizes-general.md).

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Adatlemezek max. száma / átviteli sebesség: IO | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32 / 48 x 500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Az alábbiakban az irányelvek a megfelelő értéket a **AzureSSISMaxParallelExecutionsPerNode** tulajdonság: 

1. Állítsa be, először egy kisebb értékre.
2. Ellenőrizze, hogy a teljes átviteli sebesség növelése kis mértékben nő.
3. Állítsa le az érték növelése, ha általános eléri a maximális értéket.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** az SSIS-katalógusadatbázist (SSISDB) az Azure SQL-adatbázis tarifacsomagjának van. Ez a beállítás hatással van a végrehajtási naplóból betöltése az integrációs modul példányt, a várólistára-csomag végrehajtása, és a sebesség a feldolgozók maximális száma.

-   Ha nem érdeklik a sebesség, várólista-csomag végrehajtása és betölteni a végrehajtási naplóból, kiválaszthatja a legalacsonyabb az adatbázis tarifacsomagjának. Az Azure SQL Database az alapszintű díjszabás az integrációsmodul-példány 8 feldolgozó támogatja.

-   Az alapszintűnél nagyobb teljesítményű adatbázis akkor válassza, ha a feldolgozók száma több mint 8, vagy a magok száma 50-nél nagyobb. Ellenkező esetben az adatbázis az integration runtime-példány szűk keresztmetszetté válik, és legyen negatív hatással az általános teljesítménye.

-   Például S3 szintű hatékonyabb adatbázis kiválasztása, ha a naplózási szint van beállítva a részletes. Megfelelő munkaköröket külsős informatikusok házon belüli tesztelés, s3 tarifacsomagra 2 csomópontot, a 128 párhuzamos számát és a részletes naplózás szintjét az SSIS-csomag végrehajtása is támogatja.

Is beállíthatja az adatbázis alapján tarifacsomag [adatbázis-tranzakciós egységek](../sql-database/sql-database-what-is-a-dtu.md) (DTU) használati adatokhoz az Azure Portalon.

## <a name="design-for-high-performance"></a>Nagy teljesítményű rendszer tervezése
Tervezése futtathatják az Azure-SSIS-csomag eltér a helyi végrehajtásra csomag tervezése. Ugyanaz a csomag több független feladatokat ötvöző helyett válassza el őket, több csomagot az hatékonyabb végrehajtása az Azure-SSIS integrációs modult. Hozzon létre egy csomag végrehajtása az egyes csomagok, így nem kell egymással a befejezéshez várja. Ez a megközelítés számos előnyt biztosít az az Azure-SSIS integrációs modul méretezhetőségét, és javítja az általános.

## <a name="next-steps"></a>További lépések
További információ az Azure-SSIS integrációs modul. Lásd: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).
