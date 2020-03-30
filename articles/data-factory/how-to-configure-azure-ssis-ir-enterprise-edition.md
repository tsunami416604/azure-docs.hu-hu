---
title: Enterprise Edition kiépítése az Azure-SSIS-integrációs futásórához
description: Ez a cikk ismerteti az Enterprise Edition szolgáltatásait az Azure-SSIS integrációs futásidejűhez, és hogyan építheti ki
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 8096da955da0266f3727197f21d67c33d099aa4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74922650"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Enterprise Edition kiépítése az Azure-SSIS-integrációs futásórához

Az Azure-SSIS-integrációs futásidő Enterprise Edition szolgáltatása lehetővé teszi a következő speciális és prémium funkciók használatát:
-   Adatrögzítési (CDC) összetevők módosítása
-   Oracle, Teradata és SAP BW-összekötők
-   SQL Server Analysis Services (SSAS) és Az Azure Analysis Services (AAS) összekötők és átalakítások
-   Fuzzy csoportosítás és fuzzy lookup átalakítások
-   Kifejezéskinyerési és kifejezés-keresgazokat

Néhány ilyen funkciók megkövetelik, hogy további összetevőket telepíteni az Azure-SSIS IR testreszabásához. További összetevők telepítéséről az [Azure-SSIS-integrációs futásidejű egyéni beállítás című témakörben](how-to-configure-azure-ssis-ir-custom-setup.md)talál további információt.

## <a name="enterprise-features"></a>Vállalati funkciók

| **Vállalati szolgáltatások** | **Leírások** |
|---|---|
| CDC alkatrészek | A CDC-forrás, a vezérlőfeladat és az osztóátalakítás elő van telepítve az Azure-SSIS IR Enterprise Edition-en. Az Oracle-hez való csatlakozáshoz telepítenie kell a CDC Designer and Service szolgáltatást egy másik számítógépre is. |
| Oracle csatlakozók | Az Oracle Connection Manager, Source és Destination előtelepített az Azure-SSIS IR Enterprise Edition. Telepítenie kell az Oracle Call Interface (OCI) illesztőprogramját is, és szükség esetén konfigurálnia kell az Oracle Transport Network Substrate (TNS) hálózatot az Azure-SSIS IR-en. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-összekötők | Telepítenie kell a Teradata Connection Manager, Source és Destination, valamint a Teradata párhuzamos átvitel (TPT) API-t és a Teradata ODBC illesztőprogramot az Azure-SSIS IR Enterprise Edition-re. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW csatlakozók | Az SAP BW Connection Manager, Source és Destination előtelepített az Azure-SSIS IR Enterprise Edition. Az SAP BW-illesztőprogramot is telepítenie kell az Azure-SSIS IR-re. Ezek az összekötők támogatják az SAP BW 7.0 vagy korábbi verziókat. Az SAP BW vagy más SAP-termékek későbbi verzióihoz való csatlakozáshoz megvásárolhatja és telepítheti az SAP-összekötőket külső független szoftverszállítóktól az Azure-SSIS IR-n. További összetevők telepítéséről az [Azure-SSIS-integrációs futásidejű egyéni beállítás című témakörben](how-to-configure-azure-ssis-ir-custom-setup.md)talál további információt. |
| Analysis Services összetevői               | Az adatbányászati modell képzési cél, a dimenziófeldolgozási cél, és a partíciófeldolgozási cél, valamint az adatbányászati lekérdezés átalakítása, előtelepített az Azure-SSIS IR Enterprise Edition. Ezek az összetevők mindegyike támogatja az SQL Server Analysis Services (SSAS), de csak a partíciófeldolgozási cél támogatja az Azure Analysis Services (AAS). Az SSAS-hez való csatlakozáshoz a Windows hitelesítési hitelesítő adatait is [konfigurálnia kell az SSISDB rendszerben.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) Ezeken az összetevőkön kívül az Analysis Services DDL-feladat végrehajtása, az Analysis Services Processing Task és az Adatbányászati lekérdezési feladat is elő van telepítve az Azure-SSIS IR Standard/Enterprise Edition.In addition to these components, the Analysis Services Execute DDL Task, the Analysis Services Processing Task, and the Data Mining Query Task is preinstalled on the Azure-SSIS IR Standard/Enterprise Edition. |
| Fuzzy csoportosítás és fuzzy lookup átalakítások  | A Fuzzy csoportosítás és a Fuzzy Lookup átalakítások előre telepítve vannak az Azure-SSIS IR Enterprise Edition. Ezek az összetevők támogatják az SQL Server és az Azure SQL Database számára a referenciaadatok tárolását. |
| Kifejezéskinyerési és kifejezés-keresgazokat | A kifejezés kinyerése és a kifejezéskeresés átalakítások elővannak telepítve az Azure-SSIS IR Enterprise Edition. Ezek az összetevők támogatják az SQL Server és az Azure SQL Database számára a referenciaadatok tárolását. |

## <a name="instructions"></a>Utasítások

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Töltse le és telepítse [az Azure PowerShellt.](/powershell/azure/install-az-ps)

2.  Amikor kiépíti vagy újrakonfigurálja az Azure-SSIS IR-t a PowerShell használatával, futtassa `Set-AzDataFactoryV2IntegrationRuntime` az **Enterprise-t** az **Azure-SSIS** IR indítása előtt az Edition paraméter értékeként. Itt van egy minta script:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>További lépések

-   [Egyéni beállítás az Azure-SSIS integrációs futásórához](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Fizetős vagy licencelt egyéni összetevők fejlesztése az Azure-SSIS integrációs futásidejéhez](how-to-develop-azure-ssis-ir-licensed-components.md)
