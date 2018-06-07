---
title: Az Azure-SSIS-integrációs futásidejű rendszer Enterprise kiadásában |} Microsoft Docs
description: Ez a cikk ismerteti a szolgáltatások és az Azure-SSIS integrációs futásidejű konfigurálását, Enterprise Edition
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: 5ce6b46a49e3fba8f60311ad82d3e1b77ad6d993
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619303"
---
# <a name="enterprise-edition-of-the-azure-ssis-integration-runtime"></a>Azure-SSIS integrációs futásidejű Enterprise Edition

Az Enterprise kiadás Azure-SSIS integrációs futásidejű lehetővé teszi a következő speciális és a prémium szolgáltatások:
-   Adatok rögzítéséhez (CDC) összetevők módosítása
-   Oracle, a teradata rendszerhez és az SAP BW összekötők
-   SQL Server Analysis Services (SSAS) és az Azure Analysis Services (AAS) összekötőket és átalakítások
-   Intelligens csoportosítás és az intelligens keresési átalakítások
-   Kifejezés kivonása és a keresési kifejezés átalakítások

A felsorolt szolgáltatások némelyikéhez megkövetelik a további összetevők, az Azure-SSIS infravörös testreszabása További összetevők telepítésének módjáról további információk: [egyéni beállítása az Azure-SSIS-integráció futási időben](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>A vállalati szolgáltatások

| **A vállalati szolgáltatások** | **Leírása** |
|---|---|
| Adatváltozás-rögzítés összetevők | Az adatváltozás-rögzítés forrás-, vezérlő feladat, és átalakítása elválasztójának előtelepítve az Azure-SSIS-IR Enterprise kiadás. Szeretne csatlakozni az Oracle, akkor is kell az adatváltozás-rögzítés Designer és a szolgáltatás egy másik számítógépre telepítse. |
| Oracle-összekötők | A Csatlakozáskezelő Oracle, a forrás és a cél az Azure-SSIS-IR Enterprise Edition előtelepítve. Az Oracle Call Interface (OCI) illesztőprogramot telepítse, és szükség esetén konfigurálja az Oracle átviteli hálózati hordozó (TNS), az Azure-SSIS infravörös is kell További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-összekötők | A Teradata Csatlakozáskezelő, a forrás, és a cél, valamint a Teradata párhuzamos szállító (Tpt-vel) API- és Teradata ODBC-illesztőprogram telepítése az Azure-SSIS IR Enterprise Edition verziójára van szüksége. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW-összekötők | A SAP BW Connection Manager, a forrás és a cél az Azure-SSIS-IR Enterprise Edition előtelepítve. Az SAP BW illesztőprogramot telepítse az Azure-SSIS infravörös kell Az összekötők SAP BW 7.0 vagy korábbi verzióit támogatja. Csatlakozás az SAP BW Programhoz vagy más SAP termékek újabb verziójára, beszerzési és SAP összekötők telepítse az Azure-SSIS infravörös a külső ISV-k További összetevők telepítésének módjáról további információk: [egyéni beállítása az Azure-SSIS-integráció futási időben](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services-összetevők               | Adatbányászati modell betanítási adatok célja, a dimenzió feldolgozása cél és a partíció feldolgozása cél, valamint az adatok adatbányászati lekérdezés átalakításában, az Azure-SSIS-IR Enterprise Edition előtelepítve. Ezek az összetevők támogatja az SQL Server Analysis Services (SSAS), de csak a partíció feldolgozása cél támogatja az Azure Analysis Services (AAS). Az SSAS csatlakozni szükség [Windows-hitelesítés hitelesítő adatok beállítása az SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Ezek az összetevők mellett az Analysis Services hajtható végre DDL feladat, az Analysis Services feldolgozása feladat és az adatok adatbányászati lekérdezés feladat is előtelepített az Azure-SSIS IR Standard vagy Enterprise kiadásán. |
| Intelligens csoportosítás és az intelligens keresési átalakítások  | Az intelligens egyeztetésű csoportosítás és az intelligens keresési átalakítások előtelepített az Azure-SSIS-IR vállalati verzióján. Ezeket az összetevőket támogatja az SQL Server és az Azure SQL Database hivatkozás adatainak tárolásához. |
| Kifejezés kivonása és a keresési kifejezés átalakítások | A kifejezés kivonása és a keresési kifejezés átalakítások előtelepített az Azure-SSIS-IR vállalati verzióján. Ezeket az összetevőket támogatja az SQL Server és az Azure SQL Database hivatkozás adatainak tárolásához. |

## <a name="instructions"></a>Utasítások

1.  Töltse le és telepítse [Azure PowerShell (5.4 vagy újabb verzió)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  Ha kiépítése, vagy konfigurálja újra az Azure-SSIS infravörös a PowerShell használatával, `Set-AzureRmDataFactoryV2IntegrationRuntime` a **vállalati** értéke a **Edition** paraméter az Azure-SSIS infravörös megkezdése előtt Íme egy minta parancsfájlt:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>További lépések

-   [Az Azure-SSIS-integrációs futásidejű egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Hogyan fejleszthet a fizetős, vagy az Azure-SSIS-integrációs futásidejű egyéni összetevők licence](how-to-develop-azure-ssis-ir-licensed-components.md)
