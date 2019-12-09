---
title: Azure-SSIS Integration Runtime konfigurálása SQL Database feladatátvételhez
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Azure-SSIS Integration Runtime Azure SQL Database geo-replikációval és feladatátvételsel a SSISDB-adatbázishoz
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/14/2018
ms.openlocfilehash: 92f7d25a9c19409b220b6a71fba87da91e51a415
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928501"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>A Azure-SSIS Integration Runtime konfigurálása Azure SQL Database geo-replikációval és feladatátvételsel

Ez a cikk azt ismerteti, hogyan konfigurálható a Azure-SSIS Integration Runtime Azure SQL Database geo-replikációval a SSISDB-adatbázishoz. Feladatátvétel esetén gondoskodhat arról, hogy a Azure-SSIS IR továbbra is működjön a másodlagos adatbázissal.

A SQL Database földrajzi replikálásával és feladatátvételével kapcsolatos további információkért lásd [: az Active geo-replikáció és az automatikus feladatátvételi csoportok áttekintése](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>1\. forgatókönyv – Azure-SSIS IR az írási-olvasási figyelő végpontra mutat.

### <a name="conditions"></a>Feltételek

Ez a szakasz az alábbi feltételek teljesülése esetén érvényes:

- A Azure-SSIS IR a feladatátvételi csoport írási-olvasási figyelő végpontjának mutat.

  ÉS

- A SQL Database-kiszolgáló *nincs konfigurálva a* virtuális hálózati szolgáltatás végponti szabályával.

### <a name="solution"></a>Megoldás

Feladatátvétel esetén a Azure-SSIS IR átlátszó. A Azure-SSIS IR automatikusan csatlakozik a feladatátvételi csoport új elsődlegeséhez.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>2\. forgatókönyv – Azure-SSIS IR az elsődleges kiszolgálói végpontra mutat

### <a name="conditions"></a>Feltételek

Ez a szakasz akkor érvényes, ha a következő feltételek valamelyike teljesül:

- A Azure-SSIS IR a feladatátvételi csoport elsődleges kiszolgálói végpontján mutat. Ez a végpont megváltozik a feladatátvétel bekövetkeztekor.

  VAGY

- A Azure SQL Database-kiszolgáló a virtuális hálózati szolgáltatás végponti szabályával van konfigurálva.

  VAGY

- Az adatbázis-kiszolgáló egy SQL Database felügyelt példány, amely virtuális hálózattal van konfigurálva.

### <a name="solution"></a>Megoldás

Feladatátvétel esetén a következő műveleteket kell végrehajtania:

1. Állítsa le a Azure-SSIS IR.

2. Konfigurálja újra az IR-t úgy, hogy az új elsődleges végpontra és az új régióban lévő virtuális hálózatra mutasson.

3. Indítsa újra az IR-t.

A következő szakaszok részletesebben ismertetik ezeket a lépéseket.

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy engedélyezte a vész-helyreállítást a Azure SQL Database-kiszolgálónál abban az esetben, ha a kiszolgáló egyidejű leállás miatt leáll. További információ: [az üzletmenet folytonosságának áttekintése Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Ha az aktuális régióban virtuális hálózatot használ, akkor az Azure-SSIS integrációs moduljának összekapcsolásához egy másik virtuális hálózatot kell használnia az új régióban. További információ: [Az Azure SSIS Integration Runtime csatlakoztatása egy virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md).

- Ha egyéni telepítést használ, előfordulhat, hogy elő kell készítenie egy másik SAS URI-t a blob-tárolóhoz, amely az egyéni telepítési parancsfájlt és a kapcsolódó fájlokat tárolja, így a leállás során továbbra is elérhető marad. További információ: [Egyéni telepítő konfigurálása Azure-SSIS Integration Runtime-on](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Lépések

Kövesse az alábbi lépéseket a Azure-SSIS IR leállításához, az IR átváltásához egy új régióra, majd indítsa el újra.

1. Állítsa le az IR-t az eredeti régióban.

2. A következő parancs meghívásával frissítse az IR-t az új beállításokkal.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    További információ erről a PowerShell-parancsról: [Az Azure-SSIS integrációs modul létrehozása a Azure Data Factory-ben](create-azure-ssis-integration-runtime.md)

3. Indítsa újra az IR-t.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>3\. forgatókönyv – meglévő SSISDB (SSIS-katalógus) csatolása egy új Azure-SSIS IRhoz

Ha az aktuális régióban egy ADF-vagy Azure-SSIS IR-katasztrófa következik be, a SSISDB továbbra is dolgozhat egy új Azure-SSIS IR egy új régióban.

### <a name="prerequisites"></a>Előfeltételek

- Ha az aktuális régióban virtuális hálózatot használ, akkor az Azure-SSIS integrációs moduljának összekapcsolásához egy másik virtuális hálózatot kell használnia az új régióban. További információ: [Az Azure SSIS Integration Runtime csatlakoztatása egy virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md).

- Ha egyéni telepítést használ, előfordulhat, hogy elő kell készítenie egy másik SAS URI-t a blob-tárolóhoz, amely az egyéni telepítési parancsfájlt és a kapcsolódó fájlokat tárolja, így a leállás során továbbra is elérhető marad. További információ: [Egyéni telepítő konfigurálása Azure-SSIS Integration Runtime-on](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Lépések

Kövesse az alábbi lépéseket a Azure-SSIS IR leállításához, az IR átváltásához egy új régióra, majd indítsa el újra.

1. A tárolt eljárás végrehajtása **\<new_data_factory_name\>** vagy **\<new_integration_runtime_name\>hoz** csatolt SSISDB létrehozásához.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Hozzon létre egy új, **\<new_data_factory_name\>** nevű adatelőállítót az új régióban. További információt az adat-előállító létrehozása című témakörben talál.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    További információ erről a PowerShell-parancsról: [Azure-beli adat-előállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)

3. Hozzon létre egy **\<new_integration_runtime_name\>** nevű új Azure-SSIS IRt az új régióban Azure PowerShell használatával.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    További információ erről a PowerShell-parancsról: [Az Azure-SSIS integrációs modul létrehozása a Azure Data Factory-ben](create-azure-ssis-integration-runtime.md)

4. Indítsa újra az IR-t.

## <a name="next-steps"></a>Következő lépések

Vegye figyelembe a Azure-SSIS IR következő konfigurációs beállításait:

- [A Azure-SSIS Integration Runtime konfigurálása nagy teljesítményhez](configure-azure-ssis-integration-runtime-performance.md)

- [Az Azure-SSIS Integration Runtime telepítésének testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Enterprise Edition kiépítése a Azure-SSIS Integration Runtime számára](how-to-configure-azure-ssis-ir-enterprise-edition.md)
