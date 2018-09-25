---
title: Konfigurálja újra az Azure-SSIS integrációs modul |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálható újra egy Azure-SSIS integrációs modul az Azure Data Factoryban, miután már kiépítette azt.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7d4accf7958b93addd1b6d99e2d40a447c32dba9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956591"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Konfigurálja újra az Azure-SSIS integrációs modul
Ez a cikk ismerteti, hogyan konfigurálható újra egy meglévő Azure-SSIS integrációs modult. Egy Azure-SSIS integrációs modul (IR) létrehozása az Azure Data Factoryban: [hozzon létre egy Azure-SSIS integrációs modul](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete 
Data Factory felhasználói felületének használatával állítsa le, szerkesztése vagy átkonfigurálása vagy törlése az Azure-SSIS integrációs modult. 

1. Az a **Data Factory felhasználói felülete**, váltson át a **szerkesztése** fülre. Indítsa el a Data Factory felhasználói felületén, kattintson a **létrehozás és Monitorozás** a data factory kezdőlapján.
2. A bal oldali ablaktáblán kattintson a **kapcsolatok**.
3. A jobb oldali ablaktáblán, váltson a **integrációs modulok**. 
4. A műveletek oszlopban lévő gombokat használhatja **leállítása**, **szerkesztése**, vagy **törlése** az integrációs modul. A **kód** gombra a **műveletek** oszlopban megtekintheti az integration runtime társított JSON-definícióját.  
    
    ![Az Azure SSIS integrációs modul esetén végrehajtandó műveletek](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Egy Azure-SSIS integrációs modul újrakonfigurálása
1. Az integrációs modul leállításához kattintva **leállítása** a a **műveletek** oszlop. A lista nézet frissítéséhez kattintson a **frissítése** az eszköztáron. Miután az integrációs modul le van állítva, láthatja, hogy az első művelet segítségével indítsa el az integrációs 

    ![Az Azure-SSIS integrációs modul – Leállítás utáni műveletek](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Szerkesztés/újrakonfigurálás integrációs modul kattintva **szerkesztése** gombra a **műveletek** oszlop. Az a **Integration Runtime telepítővarázslójának befejező** beállítások módosítása (például a csomópont, a csomópontok vagy a csomópontonkénti maximális párhuzamos végrehajtások száma mérete) ablakban. 
3. Az integrációs modul újraindításához kattintson **Start** gombra a **műveletek** oszlop.     

## <a name="azure-powershell"></a>Azure PowerShell
Miután üzembe helyezése, és indítsa el az Azure-SSIS integrációs modul egy példányát, konfigurálhatja újra a sorozatát futtatja `Stop`  -  `Set`  -  `Start` PowerShell-parancsmagok egymás után. Például a következő PowerShell-szkript módosítja az Azure-SSIS integrációs modul példány öt lefoglalt csomópontok számát.

### <a name="reconfigure-an-azure-ssis-ir"></a>Konfigurálja újra az Azure-SSIS integrációs modul

1. Először állítsa le az Azure-SSIS integrációs modul használatával a [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmagot. Ez a parancs kiadja az összes hozzá tartozó csomópont, és leállítja a számlázás.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Ezután konfigurálja újra az Azure-SSIS integrációs modul használatával a [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmagot. A következő mintaparancs elvégzi a horizontális felskálázást egy Azure-SSIS integrációs modul öt csomóponton.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Ezután indítsa el az Azure-SSIS integrációs modul használatával a [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmagot. Ez a parancs foglal le a csomópontok összes SSIS-csomagok futtatásához.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Egy Azure-SSIS integrációs modul törlése
1. Először listázza az összes meglévő Azure SSIS integrációs modulokról az adat-előállító alapján.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Ezután állítsa le az összes meglévő Azure SSIS integrációs modulokról az adat-előállítóban.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ezután távolítsa el minden meglévő Azure SSIS integrációs modulokról az adat-előállító egyenként.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Végezetül távolítsa el az adat-előállítóhoz.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Ha korábban létrehozott egy új erőforráscsoportot, távolítsa el az erőforráscsoportot.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>További lépések
További információ az Azure-SSIS modult tekintse meg a következő témaköröket: 

- [Az Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást integrációs modulok általában többek között az Azure-SSIS integrációs modult. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatás az Azure SQL Database felügyelt példány használatával és az integrációs modul csatlakoztatása virtuális hálózathoz. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
 
