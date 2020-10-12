---
title: Az Azure SSIS Integration Runtime konfigurálása SQL Database feladatátvételhez
description: Ez a cikk azt ismerteti, hogyan konfigurálható az Azure-SSIS Integration Runtime Azure SQL Database geo-replikációval és feladatátvételsel a SSISDB-adatbázishoz
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
ms.date: 04/09/2020
ms.openlocfilehash: e1b70e0e3eb54253972afded1bd37363d1a868e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84195717"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Az Azure-SSIS integrációs modul konfigurálása SQL Database geo-replikációval és feladatátvételsel

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan konfigurálható az Azure-SSIS Integration Runtime (IR) Azure SQL Database geo-replikációval a SSISDB-adatbázishoz. Feladatátvétel esetén gondoskodhat arról, hogy a Azure-SSIS IR továbbra is működjön a másodlagos adatbázissal.

A SQL Database földrajzi replikálásával és feladatátvételével kapcsolatos további információkért lásd [: az Active geo-replikáció és az automatikus feladatátvételi csoportok áttekintése](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Feladatátvétel Azure-SSIS IR SQL felügyelt példánnyal

### <a name="prerequisites"></a>Előfeltételek

Az Azure SQL felügyelt példányai egy *adatbázis főkulcsát (DMK)* használják az adatbázisban tárolt adatok, hitelesítő adatok és kapcsolódási információk védelmére. A DMK automatikus visszafejtésének engedélyezéséhez a kulcs másolatát a *rendszer a kiszolgáló főkulcsán (SMK)* keresztül titkosítja. 

A SMK nem replikálódik feladatátvételi csoportba. A feladatátvételt követően az elsődleges és a másodlagos példányon is hozzá kell adnia egy jelszót a DMK visszafejtéséhez.

1. Futtassa az alábbi parancsot az elsődleges példány SSISDB. Ez a lépés új titkosítási jelszót vesz fel.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Hozzon létre egy feladatátvételi csoportot egy SQL felügyelt példányon.

3. Futtassa **sp_control_dbmasterkey_password** a másodlagos példányon az új titkosítási jelszó használatával.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>1. forgatókönyv: a Azure-SSIS IR írási/olvasási figyelő-végpontra mutat

Ha azt szeretné, hogy a Azure-SSIS IR egy írható/olvasható figyelő végpontra mutasson, először az elsődleges kiszolgáló-végpontra kell mutatnia. A SSISDB feladatátvételi csoportban való elhelyezése után az írási/olvasási figyelő végpontra válthat, és újraindíthatja a Azure-SSIS IR.

#### <a name="solution"></a>Megoldás

Feladatátvétel esetén hajtsa végre a következő lépéseket:

1. Állítsa le a Azure-SSIS IR az elsődleges régióban.

2. Szerkessze a Azure-SSIS IR új régióval, virtuális hálózattal és közös hozzáférésű aláírással (SAS) kapcsolatos URI-információkkal a másodlagos példány egyéni telepítéséhez. Mivel a Azure-SSIS IR egy olvasási/írási figyelőre mutat, és a végpont átlátszó a Azure-SSIS IR számára, nem kell szerkesztenie a végpontot.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Indítsa újra a Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>2. forgatókönyv: a Azure-SSIS IR elsődleges kiszolgálói végpontra mutat

Ez a forgatókönyv akkor megfelelő, ha a Azure-SSIS IR elsődleges kiszolgálói végpontra mutat.

#### <a name="solution"></a>Megoldás

Feladatátvétel esetén hajtsa végre a következő lépéseket:

1. Állítsa le a Azure-SSIS IR az elsődleges régióban.

2. Szerkessze a Azure-SSIS IRt a másodlagos példány új régiójával, végponttal és virtuális hálózati adataival.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database endpoint" `
                    -CatalogAdminCredential "Azure SQL Database admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
        ```

3. Restart the Azure-SSIS IR.

### Scenario 3: Azure-SSIS IR is pointing to a public endpoint of a SQL Managed Instance

This scenario is suitable if the Azure-SSIS IR is pointing to a public endpoint of a Azure SQL Managed Instance and it doesn't join to a virtual network. The only difference from scenario 2 is that you don't need to edit virtual network information for the Azure-SSIS IR after failover.

#### Solution

When failover occurs, take the following steps:

1. Stop the Azure-SSIS IR in the primary region.

2. Edit the Azure-SSIS IR with the new region and endpoint information for the secondary instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Indítsa újra a Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>4. forgatókönyv: meglévő SSISDB-példány (SSIS-katalógus) csatolása egy új Azure-SSIS IRhoz

Ez a forgatókönyv akkor megfelelő, ha azt szeretné, hogy a SSISDB új Azure-SSIS IR új régióban működjön, ha az aktuális régióban Azure Data Factory vagy Azure-SSIS IR katasztrófa következik be.

#### <a name="solution"></a>Megoldás

Feladatátvétel esetén hajtsa végre az alábbi lépéseket.

> [!NOTE]
> Használja a PowerShellt a 4. lépéshez (az IR létrehozása). Ha nem, akkor a Azure Portal hibát jelez, amely szerint a SSISDB már létezik.

1. Állítsa le a Azure-SSIS IR az elsődleges régióban.

2. Futtasson egy tárolt eljárást a metaadatok frissítéséhez a SSISDB-ben, hogy fogadja a és a közötti kapcsolatokat **\<new_data_factory_name\>** **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Hozzon létre egy új, nevű adatelőállítót **\<new_data_factory_name\>** az új régióban.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    További információ erről a PowerShell-parancsról: [Azure-beli adat-előállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md).

4. Hozzon létre egy új Azure-SSIS IR a nevű új **\<new_integration_runtime_name\>** régióban Azure PowerShell használatával.

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

    További információ erről a PowerShell-parancsról: [Az Azure-SSIS integrációs modul létrehozása Azure Data Factoryban](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Feladatátvétel Azure-SSIS IR SQL Database

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>1. forgatókönyv: a Azure-SSIS IR írási/olvasási figyelő-végpontra mutat

Ez a forgatókönyv a következő esetekben alkalmas:

- A Azure-SSIS IR a feladatátvételi csoport írási/olvasási figyelő-végpontjának mutat.
- A SQL Database-kiszolgáló *nincs konfigurálva a* virtuális hálózati szolgáltatás végpontjának szabályával.

Ha azt szeretné, hogy a Azure-SSIS IR egy írható/olvasható figyelő végpontra mutasson, először az elsődleges kiszolgáló-végpontra kell mutatnia. Miután a SSISDB egy feladatátvételi csoportba helyezte, átválthat egy írható/olvasható figyelő végpontra, és újraindíthatja a Azure-SSIS IR.

#### <a name="solution"></a>Megoldás

Feladatátvétel esetén a Azure-SSIS IR átlátszóvá válik. A Azure-SSIS IR automatikusan csatlakozik a feladatátvételi csoport új elsődlegeséhez. 

Ha frissíteni kívánja a régiót vagy más információkat a Azure-SSIS IRban, leállíthatja, szerkesztheti és újraindíthatja.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>2. forgatókönyv: a Azure-SSIS IR elsődleges kiszolgálói végpontra mutat

Ez a forgatókönyv akkor megfelelő, ha a Azure-SSIS IR elsődleges kiszolgálói végpontra mutat.

#### <a name="solution"></a>Megoldás

Feladatátvétel esetén hajtsa végre a következő lépéseket:

1. Állítsa le a Azure-SSIS IR az elsődleges régióban.

2. Szerkessze a Azure-SSIS IRt a másodlagos példány új régiójával, végponttal és virtuális hálózati adataival.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                        -CatalogServerEndpoint "Azure SQL Database endpoint" `
                        -CatalogAdminCredential "Azure SQL Database admin credentials" `
                        -VNetId "new VNet" `
                        -Subnet "new subnet" `
                        -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Indítsa újra a Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>3. forgatókönyv: meglévő SSISDB (SSIS-katalógus) csatolása egy új Azure-SSIS IRhoz

Ez a forgatókönyv akkor megfelelő, ha új Azure-SSIS IR szeretne kiépíteni egy másodlagos régióban. Akkor is megfelelő, ha azt szeretné, hogy a SSISDB egy új régióban új Azure-SSIS IR működjön, ha az aktuális régióban Azure Data Factory vagy Azure-SSIS IR katasztrófa következik be.

#### <a name="solution"></a>Megoldás

Feladatátvétel esetén hajtsa végre az alábbi lépéseket.

> [!NOTE]
> Használja a PowerShellt a 4. lépéshez (az IR létrehozása). Ha nem, akkor a Azure Portal hibát jelez, amely szerint a SSISDB már létezik.

1. Állítsa le a Azure-SSIS IR az elsődleges régióban.

2. Futtasson egy tárolt eljárást a metaadatok frissítéséhez a SSISDB-ben, hogy fogadja a és a közötti kapcsolatokat **\<new_data_factory_name\>** **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Hozzon létre egy új, nevű adatelőállítót **\<new_data_factory_name\>** az új régióban.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    További információ erről a PowerShell-parancsról: [Azure-beli adat-előállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md).

4. Hozzon létre egy új Azure-SSIS IR a nevű új **\<new_integration_runtime_name\>** régióban Azure PowerShell használatával.

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

    További információ erről a PowerShell-parancsról: [Az Azure-SSIS integrációs modul létrehozása Azure Data Factoryban](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>További lépések

Vegye figyelembe a Azure-SSIS IR következő konfigurációs beállításait:

- [Az Azure SSIS Integration Runtime konfigurálása a nagy teljesítmény érdekében](configure-azure-ssis-integration-runtime-performance.md)

- [Az Azure-SSIS Integration Runtime telepítésének testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Enterprise Edition kiépítése az Azure-SSIS Integration Runtime számára](how-to-configure-azure-ssis-ir-enterprise-edition.md)
