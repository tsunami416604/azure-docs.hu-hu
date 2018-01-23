---
title: "Az Azure-SSIS integrációs futásidejű tartozó nagyteljesítményű konfigurálása |} Microsoft Docs"
description: "Az Azure-SSIS integrációs futásidejű tartozó nagyteljesítményű tulajdonságainak konfigurálása"
services: data-factory
ms.date: 11/29/2017
ms.topic: article
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: daa0595fa8c1666cce5c762e0d41559411cae1b6
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Az Azure-SSIS integrációs futásidejű tartozó nagyteljesítményű konfigurálása

Ez a cikk ismerteti az Azure-SSIS integrációs futásidejű (IR) tartozó nagyteljesítményű konfigurálása. Az Azure-SSIS infravörös lehetővé teszi, telepítéséhez és futtatásához az SQL Server Integration Services (SSIS) csomag az Azure-ban. Azure-SSIS-IR kapcsolatos további információkért lásd: [integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime) cikk. További információ a központi telepítéséhez és futtatásához SSIS-csomagok Azure: [növekedési és shift a felhőbe az SQL Server Integration Services-munkaterhelések](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ez a cikk a teljesítményeredmények és a belső fejlesztésű tesztelése a SSIS-fejlesztési csapat által elvégzett megfigyelések tartalmazza. Az eredmény változhat. A saját tesztelés előtt véglegesítése a konfigurációs beállításokat, mind a költség, pedig a teljesítményt befolyásoló végrehajtásához.

## <a name="properties-to-configure"></a>Tulajdonságok konfigurálása

A következő része egy konfigurációs parancsfájl jeleníti meg a tulajdonságokat, amelyeket konfigurálhat egy Azure-SSIS-integrációs futásidejű létrehozásakor. A teljes PowerShell-parancsfájlt és a leírás [Azure csomagok központi telepítése az SQL Server Integration Services](tutorial-deploy-ssis-packages-azure.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** a integrációs futásidejű munkavégző csomópont helye. A munkavégző csomópont SSIS-katalógus adatbázis (SSISDB) Azure SQL-adatbázis állandó kapcsolatot tart fenn. Állítsa be a **AzureSSISLocation** ugyanarra a helyre, amelyen az SSISDB SQL adatbázis-kiszolgálóként, amely lehetővé teszi, hogy az integrációs futásidejű lehető leghatékonyabb módon működnek.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Azure Data Factory 2, az Azure-SSIS-IR, beleértve a nyilvános előzetes verzió támogatja a következő beállításokat:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2.

A hivatalos belső fejlesztésű alá vonni a SSIS mérnöki csapat által, a D sorozat úgy tűnik, hogy megfelelőbbek SSIS-csomag végrehajtásra, mint az A-sorozatú.

-   A teljesítmény/ár arány a D sorozat értéke magasabb, mint az A-sorozatú.
-   Az átviteli sebesség a D sorozat nagyobb, mint az A-sorozatú ugyanazon az áron.

### <a name="configure-for-execution-speed"></a>A végrehajtási sebességű konfigurálása
Ha nincs futtatásához csomagok számát, és azt szeretné, hogy a csomagok gyorsan futtatásához, olvassa el az alábbi táblázat a virtuális gép a forgatókönyvnek megfelelő típus.

Ezek az adatok egy egyetlen csomag végrehajtását egyetlen munkavégző csomóponton jelöli. A csomag 10 millió rekordot Utónév és a név utolsó oszlopok betölti az Azure Blob Storage, állít elő, a teljes oszlopa, és írja a rekordokat, amelyek rendelkeznek a teljes név hosszabb, mint az Azure Blob Storage 20 karakter.

![SSIS-integrációs futásidejű csomag végrehajtási sebesség](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>A teljes teljesítményt konfigurálása

Ha sok csomagot kell futtatni, és Ön számára legfontosabb a teljes teljesítményt, olvassa el az alábbi táblázat a virtuális gép a forgatókönyvnek megfelelő típus.

![SSIS-integrációs futásidejű maximális teljes átviteli sebesség](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** integrációs futásidejű méretezhetőségét módosíthatja. Az átviteli sebesség integrációs futásidejű azzal arányos, hogy a **AzureSSISNodeNumber**. Állítsa be a **AzureSSISNodeNumber** először kisebb értékre, az átviteli sebesség integrációs futásidejű figyelése, majd módosítsa a helyzetnek értékét. Konfigurálja újra a munkavégző csomópontok száma, lásd: [kezelése az Azure-SSIS-integrációs futásidejű](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Ha már használ egy hatékony munkavégző csomópont csomagok futtatásához, növekvő **AzureSSISMaxParallelExecutionsPerNode** növelheti a teljes teljesítményt integrációs futásidejű. Standard D1 v2 típusú csomópontok 1-4 párhuzamos végrehajtások csomópontonként támogatottak. Bármilyen típusú csomópontok 1-8 párhuzamos végrehajtások csomópontonként esetén támogatottak.
A megfelelő értéket, a csomag és a következő beállításokat, az ezen csomópontokhoz tartozó költsége alapján megbecsülheti. További információkért lásd: [általános célú virtuálisgép-méretek](../virtual-machines/windows/sizes-general.md).

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |

Az alábbiakban a megfelelő értékének beállításakor irányelveiről a **AzureSSISMaxParallelExecutionsPerNode** tulajdonság: 

1. Állítsa az értékét először kis értéket.
2. Ellenőrizze, hogy a teljes átviteli sebesség növelése kis mennyiséggel növeléséhez azt.
3. Állítsa le az érték növelése, ha a teljes teljesítményt eléri a maximális érték.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** SSIS-katalógus adatbázis (SSISDB) Azure SQL-adatbázis tarifacsomagjának van. Ez a beállítás hatással van a munkavállalók az IR-példány, a a várólistába helyezni egy csomag végrehajtását, és a sebesség betöltése a végrehajtási napló maximális számát.

-   Nem érdeklik a sebesség, várólista csomag végrehajtását, és a végrehajtási naplóban betölteni, ha a legalacsonyabb az adatbázis árképzési szintjének választhat. Az Azure SQL-adatbázisok alapszintű árképzési 8 munkavállalók integrációs futásidejű példány támogatja.

-   Alapszintű-nál nagyobb teljesítményű adatbázis akkor válassza, ha a feldolgozó száma legfeljebb 8, vagy a magok száma nagyobb, mint 50. Ha nem az adatbázis méretének integrációs futásidejű példány szűk, és legyen negatív hatással az általános teljesítményét.

Az adatbázis árképzési szint alapján is módosíthatja [adatbázis tranzakciós egység](../sql-database/sql-database-what-is-a-dtu.md) (DTU) használati információk érhetők el az Azure portálon.

## <a name="design-for-high-performance"></a>Nagy teljesítményű rendszer tervezése
Egy SSIS-csomag futtatásához Azure tervezése eltér a helyszíni végrehajtási csomagok megtervezése. Ahelyett, hogy ugyanaz a csomag több független feladatokat kombinálásával, külön őket az az Azure-SSIS infravörös hatékonyabb végrehajtása több csomagot Hozzon létre egy csomag végrehajtását minden csomag esetében, így nem rendelkeznek várjon egymástól befejezéséhez. Ezt a módszert használja az Azure-SSIS-integrációs futásidejű méretezhetőségét előnyeivel, és növeli az általános teljesítményt.

## <a name="next-steps"></a>További lépések
További tudnivalók az Azure-SSIS-integrációs futásidejű. Lásd: [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime).