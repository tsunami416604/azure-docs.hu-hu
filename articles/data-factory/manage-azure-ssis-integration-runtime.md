---
title: "Azure-SSIS integrációs futásidejű kezelése |} Microsoft Docs"
description: "Útmutató: Azure-SSIS integrációs futásidejű az Azure Data Factory újrakonfigurálása után már létrehozta."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: spelluru
ms.openlocfilehash: cc0ed958a9e1018ed9f06fdcc94873ae5420ba95
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Egy Azure-SSIS-integrációs futásidejű kezelése
A [hozzon létre egy Azure-SSIS-integrációs futásidejű](create-azure-ssis-integration-runtime.md) cikkből megtudhatja, hogyan hozhat létre egy Azure-SSIS-integrációs futásidejű Azure Data Factory használatával. Ez a cikk kiegészíti leállítása, elindítása, konfigurálja újra, vagy távolítsa el az Azure-SSIS-integrációs futásidejű kapcsolatos információk megadásával.  

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

## <a name="stop"></a>Leállítás 
Állítsa le az Azure-SSIS-integrációs futásidejű. Ez a parancs kiadott összes csomópontját, és leállítja a számlázási.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>Indítás 
Indítsa el az Azure-SSIS-integrációs futásidejű. Ez a parancs osztja ki az összes csomópontját, és elindítja a számlázási.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>Konfigurálja újra
Miután kiépítése, és indítsa el az Azure-SSIS integrációs futásidejű példánya, konfigurálhatja újra sorozatát futtatja `Stop`  -  `Set`  -  `Start` PowerShell-parancsmagok egymás után. Például a következő PowerShell-parancsfájl módosítja, az Azure-SSIS-integrációs futásidejű 5 példánya számára lefoglalt csomópontok száma.

1. Először állítsa le az Azure-SSIS-integrációs futásidejű a következő parancs futtatásával:

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Most kibővítési az Azure-SSIS integrációs futásidejű öt csomópontokra.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Ezt követően indítsa el az Azure-SSIS-integrációs futásidejű. Ez foglal le, minden csomópontja SSIS-csomagok futtatásához.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>Eltávolítás
Egy meglévő Azure-SSIS-integrációs futásidejű eltávolításához futtassa a következő parancsot: 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>Következő lépések
Azure-SSIS futásidejű kapcsolatos további információkért lásd a következő témaköröket: 

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-deploy-ssis-packages-azure.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál a felügyelt Azure SQL-példány (privát előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz (VNethez) való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a VNet oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson a virtuális hálózathoz. 
