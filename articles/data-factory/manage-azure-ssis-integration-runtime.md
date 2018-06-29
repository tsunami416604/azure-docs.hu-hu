---
title: Konfigurálja újra az Azure-SSIS-integrációs futásidejű |} Microsoft Docs
description: Ismerje meg, egy Azure-SSIS-integráció futtatókörnyezetet, az Azure Data Factory újrakonfigurálása után már létrehozta.
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
ms.openlocfilehash: 248967f736fcd10cf398917d3cd1e2760537df7c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051984"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Konfigurálja újra az Azure-SSIS-integrációs futásidejű
Ez a cikk ismerteti az egy meglévő Azure-SSIS-integrációs futásidejű újrakonfigurálása. Egy Azure-SSIS-integrációs futásidejű (IR) az Azure Data Factory létrehozásához lásd: [hozzon létre egy Azure-SSIS-integrációs futásidejű](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete 
Data Factory felhasználói felület segítségével leállítása, szerkesztése vagy újrakonfigurálási vagy egy Azure-SSIS infravörös törlése 

1. Az a **Data Factory felhasználói felület**, váltson a **szerkesztése** fülre. Indítsa el a Data Factory felhasználói felület, kattintson a **Szerző & figyelő** a data factory kezdőlapján.
2. Kattintson a bal oldali ablaktáblában **kapcsolatok**.
3. A jobb oldali ablaktáblában váltani a **integrációs futtatókörnyezetek**. 
4. Gombok használhatja a műveletek oszlopbeli **leállítása**, **szerkesztése**, vagy **törlése** integrációs futásidejű. A **kód** gombra a **műveletek** oszlop lehetővé teszi a JSON-definícióból integrációs futásidejű társított megtekintését.  
    
    ![Az Azure SSIS-IR műveletek](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Egy Azure-SSIS-IR újrakonfigurálása
1. Állítsa le a integrációs futásidejű kattintva **leállítása** a a **műveletek** oszlop. A lista nézet frissítéséhez kattintson **frissítése** az eszköztáron. Az IR leállítása után látja, hogy az első művelet lehetővé teszi, hogy az infravörös indítása 

    ![Az Azure SSIS IR - leállítása után műveletek](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Szerkesztés/reconfigure IR kattintva **szerkesztése** gombra a **műveletek** oszlop. Az a **integrációs futásidejű telepítő** ablakban, a beállítások módosítása (például mérete a csomópont, a csomópontok és a csomópontonkénti maximális párhuzamos végrehajtások száma). 
3. Az IR újraindításához kattintson **Start** gombra a **műveletek** oszlop.     

## <a name="azure-powershell"></a>Azure PowerShell
Miután kiépítése, és indítsa el az Azure-SSIS integrációs futásidejű példánya, konfigurálhatja újra sorozatát futtatja `Stop`  -  `Set`  -  `Start` PowerShell-parancsmagok egymás után. Például a következő PowerShell-parancsfájl módosítja, az Azure-SSIS-integrációs futásidejű öt példánya számára lefoglalt csomópontok száma.

### <a name="reconfigure-an-azure-ssis-ir"></a>Egy Azure-SSIS-IR újrakonfigurálása

1. Először állítsa le az Azure-SSIS-integrációs futásidejű használatával a [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmag. Ez a parancs kiadott összes csomópontját, és leállítja a számlázási.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Ezután konfigurálja újra az Azure-SSIS-IR segítségével a [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmag. A következő minta parancs arányosan ki egy Azure-SSIS integrációs futásidejű öt csomópontokra.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Ezt követően indítsa el az Azure-SSIS-integrációs futásidejű segítségével a [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmag. Ez a parancs foglal le, minden csomópontja SSIS-csomagok futtatásához.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Egy Azure-SSIS-IR törlése
1. A data factory az összes meglévő Azure SSIS IRs először felsorolása

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Ezt követően állítsa le az összes meglévő Azure SSIS IRs az adat-előállítóban.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ezután távolítsa el a meglévő Azure SSIS IRs az adat-előállítóban egyenként.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Végezetül távolítsa el a data factory.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Ha létrehozta az új erőforráscsoportot, távolítsa el az erőforráscsoportot.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>További lépések
Azure-SSIS futásidejű kapcsolatos további információkért lásd a következő témaköröket: 

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál az Azure SQL felügyelt példányának (előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
 
