---
title: Üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási javaslatok az Azure-SSIS integrációs modul |} A Microsoft Docs
description: Ez a cikk ismerteti az üzleti folytonossági és vészhelyreállítási helyreállítási javaslatok az Azure-SSIS integrációs modul.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285783"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási javaslatok az Azure-SSIS integrációs modul

Vész-helyreállítási céljából állítsa le az Azure-SSIS integrációs modul az a régió, ahol éppen fut, és indítsa el újra egy másik régiót váltani. Javasoljuk, hogy használjon [Azure párosított régiói](../best-practices-availability-paired-regions.md) erre a célra.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy engedélyezte a vészhelyreállítás az Azure SQL Database-kiszolgáló abban az esetben a kiszolgáló rendelkezik egy kimaradás egyszerre. További információ: [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](../sql-database/sql-database-business-continuity.md).

- A jelenlegi régióban egy virtuális hálózatot használ, ha egy másik virtuális hálózatot használja az új régióban az Azure-SSIS integrációs modul csatlakozni szeretne. További információ: [egy Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat](join-azure-ssis-integration-runtime-virtual-network.md).

- Ha egy egyéni telepítés használ, szükség lehet a SAS URI-t egy másik előkészítése a blob-tároló, amely tárolja az egyéni telepítési parancsfájl és az azokhoz tartozó fájlokat, így továbbra is szolgáltatáskiesés esetén érhető el. További információ: [konfigurálni egy Azure-SSIS integrációs modul egyéni telepítés](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="steps"></a>Lépések

Állítsa le az Azure-SSIS integrációs modul, az integrációs modul váltson egy új régióban, és indítsa el az alábbi lépéseket követve.

1. Állítsa le az integrációs modul az eredeti régióban.

2. Hívja a következő parancsot a PowerShellben az integrációs modul frissítéséhez

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    A PowerShell-paranccsal kapcsolatos további információkért lásd: [az Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](create-azure-ssis-integration-runtime.md)

3. Indítsa újra az integrációs modul.

## <a name="next-steps"></a>További lépések

Vegye figyelembe ezeket az Azure-SSIS integrációs modul az egyéb konfigurációs lehetőségek:

- [A nagy teljesítményű, az Azure-SSIS integrációs modul konfigurálása](configure-azure-ssis-integration-runtime-performance.md)

- [A telepítő az Azure-SSIS integrációs modul testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Az Azure-SSIS integrációs modul üzembe helyezése Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
