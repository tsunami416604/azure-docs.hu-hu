---
title: Az Azure-SSIS integrációs modul konfigurálása az SQL Database feladatátvételi |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure-SSIS integrációs modul konfigurálása az Azure SQL Database georeplikációja és a feladatátvételt az SSISDB-adatbázis
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f0612a688bb1e0fd79325b9a1f9b43731a210d10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399241"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Az Azure SQL Database georeplikációja és a feladatátvétel az Azure-SSIS integrációs modul konfigurálása

Ez a cikk ismerteti az Azure-SSIS integrációs modul konfigurálása az Azure SQL Database georeplikáció az SSISDB-adatbázisba. A feladatátvétel esetén biztosítható, hogy az Azure-SSIS integrációs modul használata a másodlagos adatbázis követi.

Georeplikáció és feladatátvétel az SQL Database kapcsolatos további információkért lásd: [áttekintése: Aktív georeplikáció és automatikus feladatátvételi csoportok](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>1\. forgatókönyv – Azure-SSIS integrációs modul mutat-e írási-olvasási figyelői végpont

### <a name="conditions"></a>Feltételek

Ez a szakasz vonatkozik, az alábbi feltételek teljesülése esetén:

- Az Azure-SSIS integrációs modul az írási-olvasási figyelői végpont a feladatátvételi csoport mutat.

  ÉS

- Az SQL Database-kiszolgáló *nem* konfigurálva a virtuális hálózati szolgáltatási végpont szabályhoz.

### <a name="solution"></a>Megoldás

Amikor feladatátvételt hajt végre, nem látja, az Azure-SSIS integrációs modult. Az Azure-SSIS integrációs modul automatikusan csatlakozik az új elsődleges, a feladatátvételi csoport.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>2\. forgatókönyv – Azure-SSIS integrációs modul mutató elsődleges kiszolgálói végpont

### <a name="conditions"></a>Feltételek

Ez a szakasz vonatkozik, amikor a következő feltételek valamelyike teljesül:

- Az Azure-SSIS integrációs modul a feladatátvételi csoport az elsődleges kiszolgáló végpontra mutat. Ezt a végpontot megváltozik, amikor feladatátvételt hajt végre.

  VAGY

- Az Azure SQL Database-kiszolgáló úgy van konfigurálva, a virtuális hálózati szolgáltatási végpont szabállyal.

  VAGY

- Az adatbázis-kiszolgáló egy SQL Database felügyelt példánya és a virtuális hálózat konfigurálva.

### <a name="solution"></a>Megoldás

Amikor feladatátvételt hajt végre, hogy tegye a következőket:

1. Állítsa le az Azure-SSIS integrációs modult.

2. Konfigurálja újra az integrációs modul, mutasson az új elsődleges végpont és a egy virtuális hálózatot az új régióban.

3. Indítsa újra az integrációs

A következő szakaszok ismertetik részletesebben ezeket a lépéseket.

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy engedélyezte a vészhelyreállítás az Azure SQL Database-kiszolgáló abban az esetben a kiszolgáló rendelkezik egy kimaradás egyszerre. További információ: [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](../sql-database/sql-database-business-continuity.md).

- A jelenlegi régióban egy virtuális hálózatot használ, ha egy másik virtuális hálózatot használja az új régióban az Azure-SSIS integrációs modul csatlakozni szeretne. További információ: [egy Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat](join-azure-ssis-integration-runtime-virtual-network.md).

- Ha egy egyéni telepítés használ, szükség lehet a SAS URI-t egy másik előkészítése a blob-tároló, amely tárolja az egyéni telepítési parancsfájl és az azokhoz tartozó fájlokat, így továbbra is szolgáltatáskiesés esetén érhető el. További információ: [konfigurálni egy Azure-SSIS integrációs modul egyéni telepítés](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Lépések

Állítsa le az Azure-SSIS integrációs modul, az integrációs modul váltson egy új régióban, és indítsa el az alábbi lépéseket követve.

1. Állítsa le az integrációs modul az eredeti régióban.

2. Hívja meg a következő parancsot a PowerShellben az integrációs modul frissítéséhez az új beállításokkal.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    A PowerShell-paranccsal kapcsolatos további információkért lásd: [az Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](create-azure-ssis-integration-runtime.md)

3. Indítsa újra az integrációs modul.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>3\. forgatókönyv – egy új Azure-SSIS integrációs modul egy meglévő SSISDB (SSIS-katalógus) csatolása

Amikor egy ADF vagy az Azure-SSIS integrációs modul katasztrófa történik az aktuális régióban, az SSISDB tartja dolgozik egy új Azure-SSIS integrációs modul egy új régióban is felvehető.

### <a name="prerequisites"></a>Előfeltételek

- A jelenlegi régióban egy virtuális hálózatot használ, ha egy másik virtuális hálózatot használja az új régióban az Azure-SSIS integrációs modul csatlakozni szeretne. További információ: [egy Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat](join-azure-ssis-integration-runtime-virtual-network.md).

- Ha egy egyéni telepítés használ, szükség lehet a SAS URI-t egy másik előkészítése a blob-tároló, amely tárolja az egyéni telepítési parancsfájl és az azokhoz tartozó fájlokat, így továbbra is szolgáltatáskiesés esetén érhető el. További információ: [konfigurálni egy Azure-SSIS integrációs modul egyéni telepítés](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Lépések

Állítsa le az Azure-SSIS integrációs modul, az integrációs modul váltson egy új régióban, és indítsa el az alábbi lépéseket követve.

1. Hajtsa végre a tárolt eljárás végrehajtása csatolt SSISDB **\<new_data_factory_name\>** vagy  **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Hozzon létre egy új data factoryt **\<new_data_factory_name\>** az új régióban. További információkért tekintse meg az adat-előállító létrehozása.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    A PowerShell-paranccsal kapcsolatos további információkért lásd: [PowerShell-lel az Azure data factory létrehozása](quickstart-create-data-factory-powershell.md)

3. Hozzon létre egy új Azure-SSIS integrációs modul nevű **\<new_integration_runtime_name\>** az Azure PowerShell használatával új régióban.

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

    A PowerShell-paranccsal kapcsolatos további információkért lásd: [az Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](create-azure-ssis-integration-runtime.md)

4. Indítsa újra az integrációs modul.

## <a name="next-steps"></a>További lépések

Vegye figyelembe ezeket az Azure-SSIS integrációs modul az egyéb konfigurációs lehetőségek:

- [A nagy teljesítményű, az Azure-SSIS integrációs modul konfigurálása](configure-azure-ssis-integration-runtime-performance.md)

- [Az Azure-SSIS Integration Runtime telepítésének testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Az Azure-SSIS integrációs modul üzembe helyezése Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
