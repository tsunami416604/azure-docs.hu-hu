---
title: Konfigurálja újra az Azure-SSIS integrációs modult
description: Megtudhatja, hogyan konfigurálhat egy Azure-SSIS integrációs modult Azure Data Factory a már üzembe helyezése után.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: fbac52d65433f2137d565ca60fcf754e49199640
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931397"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Konfigurálja újra az Azure-SSIS integrációs modult
Ez a cikk azt ismerteti, hogyan lehet újrakonfigurálni egy meglévő Azure-SSIS integrációs modult. Az Azure-SSIS Integration Runtime (IR) Azure Data Factory-ben való létrehozásáról az [Azure-SSIS Integration Runtime létrehozása](create-azure-ssis-integration-runtime.md)című témakörben olvashat.  

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete 
A Azure-SSIS IR leállításához, szerkesztéséhez/újrakonfigurálásához vagy törléséhez Data Factory felhasználói felületet használhat. 

1. A **Data Factory felhasználói felületen**váltson a **Szerkesztés** lapra. Data Factory felhasználói felület elindításához kattintson a **szerző & figyelő** elemre az adatgyár kezdőlapján.
2. A bal oldali ablaktáblán kattintson a **kapcsolatok**elemre.
3. A jobb oldali ablaktáblában váltson az **Integration Runtimes**elemre. 
4. Az integrációs modul **leállításához**, **szerkesztéséhez**vagy **törléséhez** használhatja a műveletek oszlopban lévő gombokat. A **műveletek** oszlopban található **kód** gomb segítségével megtekintheti az integrációs modulhoz társított JSON-definíciót.  
    
    ![Műveletek az Azure SSIS IR-vel](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR újrakonfigurálása
1. Állítsa le az integrációs modult a **műveletek** oszlop **Leállítás** gombjára kattintva. A listanézet frissítéséhez kattintson a **frissítés** gombra az eszköztáron. Az IR leállítása után láthatja, hogy az első művelet lehetővé teszi az IR elindítását. 

    ![Műveletek az Azure SSIS IR-vel – leállítás után](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Szerkessze/konfigurálja újra az IR-t a **műveletek** oszlop **Szerkesztés** gombjára kattintva. A **Integration Runtime Setup** (beállítások) ablakban módosítsa a beállításokat (például a csomópont méretét, a csomópontok számát vagy a csomópontok maximális, párhuzamos végrehajtását). 
3. Az IR újraindításához kattintson a **műveletek** oszlop **Start** gombjára.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure-SSIS integrációs modul egy példányának üzembe helyezése és elindítása után újrakonfigurálhatja `Stop` - `Set` - `Start` PowerShell-parancsmagok egymás utáni futtatásával. A következő PowerShell-szkript például az Azure-SSIS Integration Runtime-példányhoz lefoglalt csomópontok számát öt értékre módosítja.

### <a name="reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR újrakonfigurálása

1. Először állítsa le az Azure-SSIS integrációs modult a [stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) parancsmag használatával. Ez a parancs kibocsátja az összes csomópontját, és leállítja a számlázást.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Ezután konfigurálja újra a Azure-SSIS IR a [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) parancsmag használatával. Az alábbi példában szereplő parancs egy Azure-SSIS integrációs modult öt csomópontra méretezi.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Ezután indítsa el az Azure-SSIS integrációs modult a [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) parancsmag használatával. Ez a parancs lefoglalja az összes csomópontját a SSIS-csomagok futtatásához.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Azure-SSIS IR törlése
1. Először sorolja fel az összes meglévő Azure SSIS IRs-t az adat-előállítóban.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Ezt követően állítsa le az összes meglévő Azure SSIS IRs-t az adatai-előállítóban.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ezután távolítsa el az összes meglévő Azure SSIS IRs-t az adatai-előállítóban egyenként.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Végül távolítsa el az adatgyárat.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Ha létrehozott egy új erőforráscsoportot, távolítsa el az erőforráscsoportot.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Következő lépések
Az Azure-SSIS futtatókörnyezettel kapcsolatos további információkért tekintse meg a következő témaköröket: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk az integrációs modulokkal kapcsolatos általános információkat tartalmaz, beleértve a Azure-SSIS IR. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt Azure SQL Database felügyelt példányának használatáról és az IR virtuális hálózathoz való csatlakoztatásáról. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
 
