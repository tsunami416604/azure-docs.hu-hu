---
title: Az Azure-SSIS-integrációs futásidő teljesítményének konfigurálása
description: Ismerje meg, hogyan konfigurálható az Azure-SSIS-integrációs futásidő tulajdonságai nagy teljesítményre
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: ca88e42438c7cb48b062aa67d82053afbb9244bf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418286"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurálja az Azure-SSIS integrációs futástime-ot a nagy teljesítmény érdekében

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Ez a cikk ismerteti, hogyan konfigurálhatja az Azure-SSIS-integrációs futásidejű (IR) a nagy teljesítményű. Az Azure-SSIS IR lehetővé teszi az SQL Server Integration Services (SSIS) csomagok üzembe helyezését és futtatását az Azure-ban. Az Azure-SSIS IR-ről további információt [az Integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime) cikk ben talál. Az SSIS-csomagok Azure-beli telepítéséről és futtatásáról az [SQL Server Integration Services számítási feladatainak felemelése és a felhőbe való átcsoportosítása](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)című témakörben talál.

> [!IMPORTANT]
> Ez a cikk az SSIS fejlesztői csapat tagjai által végzett, házon belüli tesztelés teljesítményeredményeit és észrevételeit tartalmazza. Az eredmények eltérőek lehetnek. Végezze el a saját tesztelést a konfigurációs beállítások véglegesítése előtt, amelyek mind a költségeket, mind a teljesítményt befolyásolják.

## <a name="properties-to-configure"></a>Konfigurálandó tulajdonságok

A konfigurációs parancsfájl következő része azokat a tulajdonságokat mutatja be, amelyeket az Azure-SSIS-integrációs futásidő létrehozásakor konfigurálhat. A teljes PowerShell-parancsfájlt és -leírást az [SQL Server Integration Services-csomagok telepítése az Azure-ba](tutorial-deploy-ssis-packages-azure-powershell.md)című témakörben ismerteti.

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

## <a name="azuressislocation"></a>AzureSSIS-elhelyezés
**Az AzureSSISLocation** az integrációs futásidejű feldolgozó-csomópont helye. A munkavégző csomópont állandó kapcsolatot tart fenn az SSIS katalógus adatbázisával (SSISDB) egy Azure SQL-adatbázison. Állítsa be az **AzureSSISLocation-t** ugyanarra a helyre, mint az SSISDB-t tároló SQL Database-kiszolgáló, amely lehetővé teszi, hogy az integrációs futásidő a lehető leghatékonyabban működjön.

## <a name="azuressisnodesize"></a>AzureSSISNodeMéret
A Data Factory, beleértve az Azure-SSIS IR-t, a következő lehetőségeket támogatja:
-   A4-es\_\_szabvány v2
-   Standard\_A8\_v2
-   Szabványos\_D1\_v2
-   Szabványos\_D2\_v2
-   Szabványos\_D3\_v2
-   Szabványos\_D4\_v2
-   Szabványos\_D2\_v3
-   Szabványos\_D4\_v3
-   Szabványos\_D8\_v3
-   Szabványos\_D16\_v3
-   Szabványos\_D32\_v3
-   Szabványos\_D64\_v3
-   Szabványos\_E2\_v3
-   Szabványos\_E4\_v3
-   Szabványos\_E8\_v3
-   Szabványos\_E16\_v3
-   Szabványos\_E32\_v3
-   Szabványos\_E64\_v3

Az SSIS mérnöki csapat nem hivatalos házon belüli tesztelésében a D sorozat alkalmasabbnak tűnik az SSIS csomag végrehajtására, mint az A sorozat.

-   A D sorozat teljesítmény/ár aránya magasabb, mint az A sorozaté, és a v3-as sorozat teljesítmény/ár aránya magasabb, mint a v2 sorozaté.
-   A D sorozat átviteli fazeka magasabb, mint az A sorozat ugyanazon az áron, és az átviteli a v3-as sorozat magasabb, mint a v2 sorozat ugyanazon az áron.
-   Az Azure-SSIS IR v2-sorozatú csomópontjai nem alkalmasak egyéni beállításra, ezért használja inkább a v3-as sorozatú csomópontokat. Ha már használja a v2 sorozat csomópontok, kérjük, váltson a v3-as sorozat csomópontok a lehető leghamarabb.
-   Az E sorozat memóriaoptimalizált virtuális gépméretek, amelyek nagyobb memória-CPU arányt biztosítnak, mint más gépek. Ha a csomag sok memóriát igényel, érdemes lehet az E sorozatú virtuális gép kiválasztását.

### <a name="configure-for-execution-speed"></a>Konfigurálás a végrehajtási sebességhez
Ha nem sok csomagot futtathat, és gyorsan szeretné futtatni a csomagokat, az alábbi diagramon szereplő információk alapján válasszon a forgatókönyvnek megfelelő virtuálisgép-típust.

Ezek az adatok egyetlen csomagvégrehajtás-végrehajtást jelentenek egyetlen munkavégző csomóponton. A csomag 3 millió rekordot tölt be keresztnévvel és vezetéknévoszlopokkal az Azure Blob Storage-ból, létrehoz egy teljes név oszlopot, és írja a rekordokat, amelyek teljes neve 20 karakternél hosszabb az Azure Blob Storage-ba.

![SSIS-integrációs futásidejű csomag végrehajtási sebessége](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurálás a teljes átviteli hanghoz

Ha sok csomagot futtat, és a teljes átviteli rendszer érdekli a leginkább, használja az alábbi diagramon szereplő információkat a forgatókönyvnek megfelelő virtuálisgép-típus kiválasztásához.

![SSIS-integrációs futásidejű maximális teljes átviteli teljesítmény](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**Az AzureSSISNodeNumber** az integrációs futásidő méretezhetőségét állítja be. Az integrációs futásidő átviteli ideje arányos az **AzureSSISNodeNumber számmal.** Állítsa be az **AzureSSISNodeNumber** egy kis értéket először, figyelje az átviteli mertét az integrációs futásidő, majd állítsa be az értéket a forgatókönyvhöz. A munkavégző csomópontok számának újrakonfigurálásához olvassa [el az Azure-SSIS-integrációs futásidő kezelése című témakört.](manage-azure-ssis-integration-runtime.md)

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Ha már egy hatékony feldolgozócsomópontot használ a csomagok futtatásához, az **AzureSSISISMaxParallelExecutionsPerNode** növelése növelheti az integrációs futásidő teljes átviteli sebességét. A Standard_D1_v2 csomópontok esetén csomópontonként 1-4 párhuzamos végrehajtás támogatott. Minden más típusú csomópont esetében 1-max(2 x magok száma, 8) párhuzamos végrehajtások csomópontonként támogatottak. Ha azt szeretné, hogy az **AzureSSISMaxParallelExecutionsPerNode** az általunk támogatott maximális értéken túl, megnyithat egy támogatási jegyet, és növelhetjük a maximális értéket, és ezt követően az Azure Powershell t kell használnia **az AzureSSISMaxParallelExecutionsPerNode**frissítéséhez.
A csomag költsége és a munkavégző csomópontok következő konfigurációi alapján becsülheti meg a megfelelő értéket. További információ: [Általános célú virtuális gépméretek](../virtual-machines/windows/sizes-general.md).

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Szabványos\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Szabványos\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Szabványos\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Szabványos\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| A4-es\_\_szabvány v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| Szabványos\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Szabványos\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Szabványos\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Szabványos\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Szabványos\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Szabványos\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Szabványos\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Szabványos\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Szabványos\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Szabványos\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Szabványos\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Szabványos\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Az alábbiakban az **AzureSSISISMaxParallelExecutionsPerNode** tulajdonság megfelelő értékének beállítására vonatkozó irányelveket olvashatja: 

1. Először állítsa be egy kis értékre.
2. Növelje kis mennyiséggel, és ellenőrizze, hogy a teljes átviteli teljesítmény javult-e.
3. Állítsa le az érték növelését, amikor a teljes átviteli teljesítmény eléri a maximális értéket.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**Az SSISDBPricingTier** az SSIS katalógus adatbázis (SSISDB) tarifacsomagja egy Azure SQL-adatbázisban. Ez a beállítás befolyásolja az infravörös példányban dolgozó dolgozók maximális számát, a csomagvégrehajtás várólistára kerülésének sebességét és a végrehajtási napló betöltésének sebességét.

-   Ha nem érdekli a csomag végrehajtásának és a végrehajtási napló betöltésének sebessége, kiválaszthatja a legalacsonyabb adatbázis-tarifacsomagot. Az Azure SQL Database alapszintű díjszabással támogatja a 8 dolgozót egy integrációs futásidejű példányban.

-   Válasszon egy hatékonyabb adatbázist, mint az alapszintű, ha a feldolgozók száma több, mint 8, vagy az alapvető száma több mint 50. Ellenkező esetben az adatbázis az integrációs futásidejű példány szűk keresztmetszeté válik, és az általános teljesítmény negatívhatással van.

-   Válasszon egy hatékonyabb adatbázist, például az s3-at, ha a naplózási szint részletesre van állítva. Nem hivatalos házon belüli tesztelésünk szerint az s3 tarifacsomag 2 csomóponttal, 128 párhuzamos számmal és részletes naplózási szinttel támogatja az SSIS csomagvégrehajtását.

Az adatbázis-díjszabási szint az Azure Portalon elérhető [adatbázis-tranzakciós egység](../sql-database/sql-database-what-is-a-dtu.md) (DTU) használati információk alapján is módosítható.

## <a name="design-for-high-performance"></a>Nagy teljesítményű rendszer tervezése
Az Azure-ban futtatandó SSIS-csomagok tervezése eltér a helyszíni végrehajtáshoz szükséges csomag tervezésétől. Ahelyett, hogy több független feladatot kombinálna ugyanabban a csomagban, válassza el őket több csomagra az Azure-SSIS IR hatékonyabb végrehajtása érdekében. Hozzon létre egy csomag végrehajtásminden csomag, így nem kell várni a másik befejezni. Ez a megközelítés az Azure-SSIS-integrációs futásidő méretezhetősége előnyeit élvezi, és javítja a teljes átviteli szintet.

## <a name="next-steps"></a>További lépések
További információ az Azure-SSIS-integrációs futásóráról. Lásd: [Azure-SSIS-integrációs futásidő.](concepts-integration-runtime.md#azure-ssis-integration-runtime)
