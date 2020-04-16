---
title: Az Azure-SSIS Integration Runtime újrakonfigurálása
description: Ismerje meg, hogyan konfigurálhatja újra az Azure-SSIS-integrációs futásidejűt az Azure Data Factoryban, miután már kiépítette azt.
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
ms.openlocfilehash: ffebb8f82a69f0404974e6c8ea91bec951ca80e1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415770"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS Integration Runtime újrakonfigurálása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk ismerteti, hogyan konfigurálhatja újra a meglévő Azure-SSIS-integrációs futásidejű. Ha Azure-SSIS-integrációs futásidejűt (IR) szeretne létrehozni az Azure Data Factoryban, olvassa [el az Azure-SSIS-integrációs futásidő létrehozása című témakört.](create-azure-ssis-integration-runtime.md)  

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete 
A Data Factory felhasználói felületével leállíthatja, szerkesztheti/újrakonfigurálhatja vagy törölheti az Azure-SSIS ir-t. 

1. A **Data Factory felhasználói felületén**váltson a **Szerkesztés** lapra. A Data Factory felhasználói felületének elindításához kattintson a **& figyelés e-figyelő elemre** az adat-előállító kezdőlapján.
2. A bal oldali ablaktáblában kattintson a **Kapcsolatok gombra.**
3. A jobb oldali ablaktáblában váltson az **Integrációs futtatási futtatásra.** 
4. A Műveletek oszlopban lévő gombok segítségével **leállíthatja,** **szerkesztheti**vagy **törölheti** az integrációs futásidőt. A **Műveletek** oszlop **Kód** gombja lehetővé teszi az integrációs futásidőhöz társított JSON-definíció megtekintését.  
    
    ![Műveletek az Azure SSIS IR-hez](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR újrakonfigurálása
1. Állítsa le az integrációs futásidőt a **Műveletek** oszlop **Leállítás** gombjára kattintva. A listanézet frissítéséhez kattintson az eszköztár **Frissítés** gombjára. Az infravörös szolgáltatás leállítása után láthatja, hogy az első művelet lehetővé teszi az infravörös művelet elindítását. 

    ![Műveletek az Azure SSIS IR-hez – a leállított után](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Az infravörös kapcsolat szerkesztése/újrakonfigurálása a **Műveletek** oszlop **Szerkesztés** gombjára kattintva. Az **Integrációs futásidejű telepítő** ablakban módosítsa a beállításokat (például a csomópont méretét, a csomópontok számát vagy a csomópontonkénti maximális párhuzamos végrehajtást). 
3. Az infravörös szolgáltatás újraindításához kattintson a **Műveletek** oszlop **Start** gombjára.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure-SSIS-integrációs futtatóidő egy példányának kiépítése és indítása után `Stop`  -  `Set`  -  `Start` újrakonfigurálhatja azt a PowerShell-parancsmagok sorozatának egymás után történő futtatásával. Például a következő PowerShell-parancsfájl módosítja az Azure-SSIS-integrációs futásidejű példány számára lefoglalt csomópontok számát ötre.

### <a name="reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR újrakonfigurálása

1. Először állítsa le az Azure-SSIS-integrációs futásidejű a [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) parancsmag használatával. Ez a parancs felszabadítja az összes csomópontját, és leállítja a számlázást.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Ezután konfigurálja újra az Azure-SSIS-integrációs a [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) parancsmag használatával. A következő mintaparancs az Azure-SSIS-integrációs futásidejű öt csomópontra skálázható.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Ezután indítsa el az Azure-SSIS-integrációs futásidejű a [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) parancsmag használatával. Ez a parancs lefoglalja az összes csomópontját az SSIS-csomagok futtatásához.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Azure-SSIS ir törlése
1. Először sorolja fel az összes meglévő Azure SSIS-ibers az adatgyár alatt.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Ezután állítsa le az összes meglévő Azure SSIS-ibers az adat-előállító.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ezután távolítsa el az összes meglévő Azure SSIS-ibers az adat-előállító egyenként.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Végül távolítsa el az adatgyárat.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Ha új erőforráscsoportot hozott létre, távolítsa el az erőforráscsoportot.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>További lépések
Az Azure-SSIS futásidejéről az alábbi témakörökben olvashat bővebben: 

- [Az Azure-SSIS integrációs futásideje](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk általános tájékoztatást nyújt az integrációs futásidőkről általában, beleértve az Azure-SSIS IR-t is. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk kibontja az oktatóanyagot, és útmutatást nyújt az Azure SQL Database felügyelt példány használatával kapcsolatban, és csatlakozik az infravörös egy virtuális hálózathoz. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
 
