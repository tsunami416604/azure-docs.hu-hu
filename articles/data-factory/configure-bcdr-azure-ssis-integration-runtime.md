---
title: Az Azure-SSIS-integrációs futásidő konfigurálása az SQL Database feladatátvételéhez
description: Ez a cikk bemutatja, hogyan konfigurálható az Azure-SSIS-integrációs futásidő az Azure SQL Database georeplikációjával és feladatátvételével az SSISDB-adatbázishoz
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
ms.openlocfilehash: 532258cecd823e10057ddc3536cd24071e444581
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992062"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurálja az Azure-SSIS-integrációs futásidőt az Azure SQL Database georeplikációval és feladatátvételsel

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure-SSIS-integrációs futásidőt az Azure SQL Database georeplikációval az SSISDB-adatbázishoz. Feladatátvétel esetén biztosíthatja, hogy az Azure-SSIS ir továbbra is működjön a másodlagos adatbázissal.

Az SQL Database georeplikációjáról és feladatátvételéről további információt [az Áttekintés: Aktív georeplikációs és automatikus feladatátvételi csoportok](../sql-database/sql-database-geo-replication-overview.md)című témakörben talál.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>1. forgatókönyv – Az Azure-SSIS IR az írás-olvasásfigyelő végpontra mutat

### <a name="conditions"></a>Feltételek

Ez a szakasz akkor érvényes, ha a következő feltételek teljesülnek:

- Az Azure-SSIS IR a feladatátvételi csoport írás-olvasási figyelő végpontjára mutat.

  AND

- Az SQL Database-kiszolgáló *nincs* konfigurálva a virtuális hálózati szolgáltatás végpontszabályával.

### <a name="solution"></a>Megoldás

Feladatátvétel esetén az Azure-SSIS IR transzparens. Az Azure-SSIS ir automatikusan csatlakozik a feladatátvételi csoport új elsődleges.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>2. forgatókönyv – Az Azure-SSIS ir az elsődleges kiszolgálóvégpontra mutat

### <a name="conditions"></a>Feltételek

Ez a szakasz akkor érvényes, ha az alábbi feltételek valamelyike teljesül:

- Az Azure-SSIS ir a feladatátvételi csoport elsődleges kiszolgálóvégpontjára mutat. Ez a végpont megváltozik, ha feladatátvétel történik.

  VAGY

- Az Azure SQL Database-kiszolgáló a virtuális hálózati szolgáltatás végpontszabályával van konfigurálva.

  VAGY

- Az adatbázis-kiszolgáló egy virtuális hálózattal konfigurált SQL-adatbázis által felügyelt példány.

### <a name="solution"></a>Megoldás

Feladatátvétel kor a következő műveleteket kell végeznie:

1. Állítsa le az Azure-SSIS ir.

2. Konfigurálja újra az infravörös rendszert úgy, hogy az új elsődleges végpontra és az új régió ban lévő virtuális hálózatra mutasson.

3. Indítsa újra az infravörös rendszert.

A következő szakaszok részletesebben ismertetik ezeket a lépéseket.

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy engedélyezte a vészhelyreállítást az Azure SQL Database-kiszolgáló, ha a kiszolgáló egy kimaradás egyidejű. További információ: [Az Üzletmenet-folytonosság áttekintése az Azure SQL Database-rel](../sql-database/sql-database-business-continuity.md)című témakörben talál.

- Ha az aktuális régióban virtuális hálózatot használ, az Azure-SSIS-integrációs futásidejű csatlakoztatásához egy másik virtuális hálózatot kell használnia az új régióban. További információ: [Csatlakozás az Azure-SSIS-integrációs futásidőhez virtuális hálózathoz.](join-azure-ssis-integration-runtime-virtual-network.md)

- Ha egyéni telepítést használ, előfordulhat, hogy elő kell készítenie egy másik SAS-URI-t az egyéni beállítási parancsfájlt és a kapcsolódó fájlokat tároló blobtárolóhoz, így továbbra is elérhető lesz egy kimaradás során. További információ: [Egyéni beállítás konfigurálása Azure-SSIS-integrációs futásidőben.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="steps"></a>Lépések

Az alábbi lépésekkel állítsa le az Azure-SSIS ir-t, váltson át az infravörös szolgáltatásra egy új régióra, és indítsa újra.

1. Állítsa le az infravörös szintet az eredeti régióban.

2. Hívja meg a következő parancsot a PowerShellben az infravörös kapcsolat frissítéséhez az új beállításokkal.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Erről a PowerShell-parancsról további információt [az Azure-SSIS-integrációs futásidő létrehozása az Azure Data Factoryban című témakörben talál.](create-azure-ssis-integration-runtime.md)

3. Indítsa újra az infravörös szintet.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>3. forgatókönyv – Meglévő SSISDB (SSIS-katalógus) csatolása egy új Azure-SSIS IR-hez

Ha egy ADF vagy Az Azure-SSIS ir katasztrófa történik az aktuális régióban, beállíthatja, hogy az SSISDB továbbra is egy új Azure-SSIS IR egy új régióban.

### <a name="prerequisites"></a>Előfeltételek

- Ha az aktuális régióban virtuális hálózatot használ, az Azure-SSIS-integrációs futásidejű csatlakoztatásához egy másik virtuális hálózatot kell használnia az új régióban. További információ: [Csatlakozás az Azure-SSIS-integrációs futásidőhez virtuális hálózathoz.](join-azure-ssis-integration-runtime-virtual-network.md)

- Ha egyéni telepítést használ, előfordulhat, hogy elő kell készítenie egy másik SAS-URI-t az egyéni beállítási parancsfájlt és a kapcsolódó fájlokat tároló blobtárolóhoz, így továbbra is elérhető lesz egy kimaradás során. További információ: [Egyéni beállítás konfigurálása Azure-SSIS-integrációs futásidőben.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="steps"></a>Lépések

Az alábbi lépésekkel áthelyezheti az Azure-SSIS-ir-t egy új régióba.
> [!NOTE]
> 3. lépés (az IR létrehozása) kell tenni a PowerShell en keresztül. Az Azure Portal hibát jelez, amely szerint az SSISDB már létezik.

1. A tárolt eljárás végrehajtásával frissítheti az SSISDB ** \<\> metaadatait,** hogy fogadja a new_data_factory_name és ** \<\>new_integration_runtime_name.**
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Hozzon létre egy új adatgyárat, ** \<amelynek neve new_data_factory_name\> ** az új régióban. További információ: Adatgyár létrehozása.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Erről a PowerShell-parancsról további információt a [PowerShell használatával azure-adatgyár létrehozása című témakörben](quickstart-create-data-factory-powershell.md) talál.

3. Hozzon létre egy új Azure-SSIS IR nevű ** \<new_integration_runtime_name\> ** az új régióban az Azure PowerShell használatával.

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

    Erről a PowerShell-parancsról további információt [az Azure-SSIS-integrációs futásidő létrehozása az Azure Data Factoryban című témakörben talál.](create-azure-ssis-integration-runtime.md)

4. Indítsa újra az infravörös szintet.

## <a name="next-steps"></a>További lépések

Vegye figyelembe az Azure-SSIS ir további konfigurációs beállításait:

- [Konfigurálja az Azure-SSIS integrációs futástime-ot a nagy teljesítmény érdekében](configure-azure-ssis-integration-runtime-performance.md)

- [Az Azure-SSIS Integration Runtime telepítésének testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Enterprise Edition kiépítése az Azure-SSIS-integrációs futásórához](how-to-configure-azure-ssis-ir-enterprise-edition.md)
