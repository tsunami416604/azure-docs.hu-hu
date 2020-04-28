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
ms.date: 04/09/2020
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188722"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>A Azure-SSIS Integration Runtime konfigurálása Azure SQL Database geo-replikációval és feladatátvételsel

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan konfigurálható a Azure-SSIS Integration Runtime Azure SQL Database geo-replikációval a SSISDB-adatbázishoz. Feladatátvétel esetén gondoskodhat arról, hogy a Azure-SSIS IR továbbra is működjön a másodlagos adatbázissal.

A SQL Database földrajzi replikálásával és feladatátvételével kapcsolatos további információkért lásd [: az Active geo-replikáció és az automatikus feladatátvételi csoportok áttekintése](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Feladatátvétel Azure-SSIS IR Azure SQL Database felügyelt példánnyal

### <a name="prerequisites"></a>Előfeltételek
1. Futtassa az alábbi parancsot az elsődleges példány SSISDB. Ez a lépés új titkosítási jelszót ad hozzá.
```sql
  ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
```

2. Feladatátvételi csoport létrehozása Azure SQL Database felügyelt példányon.

3. Futtassa **sp_control_dbmasterkey_password** a másodlagos példányon az új titkosítási jelszó használatával.
```sql
  EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
    @password = N'<password>', @action = N'add';  
  GO
```

### <a name="solution"></a>Megoldás
Ha feladatátvétel történik, ha a meglévő Azure-SSIS IR szeretné használni az elsődleges régióban:
1. Azure-SSIS IR leállítása az elsődleges régióban.

2. Azure-SSIS IR szerkesztése a másodlagos példány új régiójával, végponttal és VNET kapcsolatos adataival.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Azure-SSIS IR újraindítása.

4. Módosítsa a kiszolgáló nevét a SSIS-csomagok **ConnectionManager** a másodlagos példány kiszolgálójának nevével, majd telepítse újra a csomagokat, és futtassa a parancsot.

Ha új Azure-SSIS IR szeretne kiépíteni a másodlagos régióban:
> [!NOTE]
> A 4. lépés (az IR létrehozása) PowerShell használatával kell elvégezni. Azure Portal egy hibaüzenetet küld, amely szerint a SSISDB már létezik.
1. Azure-SSIS IR leállítása az elsődleges régióban.

2. Tárolt eljárás végrehajtása a metaadatok SSISDB való frissítéséhez ** \<new_data_factory_name\> ** és ** \<new_integration_runtime_name\>** kapcsolatainak fogadására.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Hozzon létre egy új, ** \<new_data_factory_name\> ** nevű adatelőállítót az új régióban. További információt az adat-előállító létrehozása című témakörben talál.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
```
  További információ erről a PowerShell-parancsról: [Azure-beli adat-előállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)

4. Hozzon létre egy ** \<new_integration_runtime_name\> ** nevű új Azure-SSIS IR az új régióban Azure PowerShell használatával.

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

5. Módosítsa a kiszolgáló nevét a SSIS-csomagok **ConnectionManager** a másodlagos példány kiszolgálójának nevével, majd telepítse újra a csomagokat, és futtassa a parancsot.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Feladatátvétel Azure-SSIS IR Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>1. forgatókönyv – Azure-SSIS IR az írási-olvasási figyelő végpontra mutat.

#### <a name="conditions"></a>Feltételek

Ez a szakasz az alábbi feltételek teljesülése esetén érvényes:

- A Azure-SSIS IR a feladatátvételi csoport írási-olvasási figyelő végpontjának mutat.

  AND

- A SQL Database-kiszolgáló *nincs konfigurálva a* virtuális hálózati szolgáltatás végponti szabályával.

#### <a name="solution"></a>Megoldás

Feladatátvétel esetén a Azure-SSIS IR átlátszó. A Azure-SSIS IR automatikusan csatlakozik a feladatátvételi csoport új elsődlegeséhez.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>2. forgatókönyv – Azure-SSIS IR az elsődleges kiszolgálói végpontra mutat

#### <a name="conditions"></a>Feltételek

Ez a szakasz akkor érvényes, ha a következő feltételek valamelyike teljesül:

- A Azure-SSIS IR a feladatátvételi csoport elsődleges kiszolgálói végpontján mutat. Ez a végpont megváltozik a feladatátvétel bekövetkeztekor.

  VAGY

- A Azure SQL Database-kiszolgáló a virtuális hálózati szolgáltatás végponti szabályával van konfigurálva.


#### <a name="solution"></a>Megoldás

1. Azure-SSIS IR leállítása az elsődleges régióban.

2. Azure-SSIS IR szerkesztése a másodlagos példány új régiójával, végponttal és VNET kapcsolatos adataival.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Azure-SSIS IR újraindítása.

4. Módosítsa a kiszolgáló nevét a SSIS-csomagok **ConnectionManager** a másodlagos példány kiszolgálójának nevével, majd telepítse újra a csomagokat, és futtassa a parancsot.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>3. forgatókönyv – meglévő SSISDB (SSIS-katalógus) csatolása egy új Azure-SSIS IRhoz

Ha az aktuális régióban egy ADF-vagy Azure-SSIS IR-katasztrófa következik be, a SSISDB továbbra is dolgozhat egy új Azure-SSIS IR egy új régióban.

#### <a name="solution"></a>Megoldás

> [!NOTE]
> A 4. lépés (az IR létrehozása) PowerShell használatával kell elvégezni. Azure Portal egy hibaüzenetet küld, amely szerint a SSISDB már létezik.

1. Azure-SSIS IR leállítása az elsődleges régióban.

2. Tárolt eljárás végrehajtása a metaadatok SSISDB való frissítéséhez ** \<new_data_factory_name\> ** és ** \<new_integration_runtime_name\>** kapcsolatainak fogadására.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Hozzon létre egy új, ** \<new_data_factory_name\> ** nevű adatelőállítót az új régióban. További információt az adat-előállító létrehozása című témakörben talál.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
```
  További információ erről a PowerShell-parancsról: [Azure-beli adat-előállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)

4. Hozzon létre egy ** \<new_integration_runtime_name\> ** nevű új Azure-SSIS IR az új régióban Azure PowerShell használatával.

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

5. Módosítsa a kiszolgáló nevét a SSIS-csomagok **ConnectionManager** a másodlagos példány kiszolgálójának nevével, majd telepítse újra a csomagokat, és futtassa a parancsot.


## <a name="next-steps"></a>További lépések

Vegye figyelembe a Azure-SSIS IR következő konfigurációs beállításait:

- [A Azure-SSIS Integration Runtime konfigurálása nagy teljesítményhez](configure-azure-ssis-integration-runtime-performance.md)

- [Az Azure-SSIS Integration Runtime telepítésének testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Enterprise Edition kiépítése a Azure-SSIS Integration Runtime számára](how-to-configure-azure-ssis-ir-enterprise-edition.md)
