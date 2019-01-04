---
title: Az Azure-SSIS integrációs modul Enterprise Edition kiépítése |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure-SSIS integrációs modul, és hogyan helyezhet üzembe, Enterprise Edition funkcióját
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: c475a9c820e577a321491b87d806ed08ddff8693
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013877"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS integrációs modul üzembe helyezése Enterprise Edition

Az Azure-SSIS integrációs modul Enterprise kiadását lehetővé teszi a következő fejlett és a prémium szintű funkciók:
-   Adatok rögzítése (CDC) összetevők módosítása
-   SAP BW, Oracle és Teradata-összekötők
-   Az SQL Server Analysis Services (SSAS) és az Azure Analysis Serviceshez (AAS) összekötőket és átalakítások
-   Az intelligens csoportosítási és intelligens keresési átalakítások
-   Kifejezés kinyerése és a keresési kifejezés átalakítások

Ezek a funkciók némelyike szükségessé telepítenek további összetevőket testreszabása az Azure-SSIS integrációs modult. További összetevő telepítésével kapcsolatos további információkért lásd: [az Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>A vállalati szolgáltatások

| **A vállalati szolgáltatások** | **Leírások** |
|---|---|
| CDC-összetevők | A CDC forrás, az ellenőrzési feladat és a felosztó átalakítása az Azure-SSIS integrációs modul Enterprise Edition előtelepítve. Szeretne csatlakozni az Oracle, akkor is kell a CDC Designer és a szolgáltatás telepítése egy másik számítógépre. |
| Oracle-összekötők | A Csatlakozáskezelő Oracle, a forrás és a cél az Azure-SSIS integrációs modul Enterprise Edition előtelepítve. Emellett az Oracle hívás Interface (OCI) illesztőprogramot telepítse, és szükség esetén konfigurálja az Oracle átviteli hálózat hordozó (TNS) az Azure-SSIS integrációs modult. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-összekötők | A Teradata-kezelő, a forrás, és a cél, valamint a Teradata párhuzamos szállító (Tpt-vel) API- és Teradata ODBC-illesztőprogram telepítése az Azure-SSIS integrációs modul Enterprise Edition kell. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Az SAP BW-összekötők | Az SAP BW Csatlakozáskezelő, a forrás és a cél az Azure-SSIS integrációs modul Enterprise Edition előtelepítve. Emellett telepítenie kell az SAP BW-illesztőprogram a az Azure-SSIS integrációs modult. Ezek az összekötők támogatják az SAP BW 7.0-s vagy régebbi verziójú. SAP BW- vagy egyéb SAP-termék újabb verzióit csatlakozni beszerzési és SAP-összekötők telepítése az Azure-SSIS integrációs modult. a külső ISV-k További összetevő telepítésével kapcsolatos további információkért lásd: [az Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services-összetevők               | Adatbányászati modell betanítási adatok cél, a dimenzió feldolgozási cél és a partíció feldolgozása cél, valamint az adatbányászati lekérdezés átalakítását, az Azure-SSIS integrációs modul Enterprise Edition előtelepítve. Ezek az összetevők támogatja az SQL Server Analysis Services (SSAS), de csak a partíció feldolgozása cél támogatja az Azure Analysis Serviceshez (AAS). Az SSAS csatlakozni szükség [Windows hitelesítő adatok beállítása az SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Ezek az összetevők mellett a Analysis Services hajtsa végre a DDL feladat, az Analysis Services feldolgozása feladat és az adatok adatbányászati lekérdezés feladat vannak is bejelentésünk az Azure-SSIS integrációs modul Standard vagy Enterprise kiadás. |
| Az intelligens csoportosítási és intelligens keresési átalakítások  | Az Azure-SSIS integrációs modul Enterprise Edition intelligens csoportosítási és intelligens keresési átalakítások van előtelepítve. Ezek az összetevők referencia-adatok tárolására szolgáló SQL Server és az Azure SQL Database támogatja. |
| Kifejezés kinyerése és a keresési kifejezés átalakítások | Az Azure-SSIS integrációs modul Enterprise Edition kifejezés kinyerése és a keresési kifejezés átalakítások van előtelepítve. Ezek az összetevők referencia-adatok tárolására szolgáló SQL Server és az Azure SQL Database támogatja. |

## <a name="instructions"></a>Utasítások

1.  Töltse le és telepítse [Azure PowerShell (5.4 vagy újabb verzió)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  Amikor kiépítése, vagy konfigurálja újra a PowerShell-lel az Azure-SSIS integrációs modul, futtassa `Set-AzureRmDataFactoryV2IntegrationRuntime` a **vállalati** értékeként a **Edition** paraméter ahhoz, hogy az Azure-SSIS integrációs modult. Itt látható egy minta parancsfájlt:

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

-   [Az Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Fejlesztés a fizetős verzióra, vagy egyéni összetevők az Azure-SSIS integrációs modul licencelt](how-to-develop-azure-ssis-ir-licensed-components.md)
