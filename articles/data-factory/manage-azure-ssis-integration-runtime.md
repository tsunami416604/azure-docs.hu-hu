---
title: "Konfigurálja újra az Azure-SSIS-integrációs futásidejű |} Microsoft Docs"
description: "Ismerje meg, egy Azure-SSIS-integráció futtatókörnyezetet, az Azure Data Factory újrakonfigurálása után már létrehozta."
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
ms.date: 12/07/2017
ms.author: spelluru
ms.openlocfilehash: 19a81917ade977a0d04934b77e8213ef6d9e0f12
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="reconfigure-an-azure-ssis-integration-runtime"></a>Egy Azure-SSIS-integrációs futásidejű újrakonfigurálása
A [hozzon létre egy Azure-SSIS-integrációs futásidejű](create-azure-ssis-integration-runtime.md) cikkből megtudhatja, hogyan hozhat létre egy Azure-SSIS-integrációs futásidejű Azure Data Factory használatával. Ez a cikk egy meglévő Azure-SSIS-integrációs futásidejű újrakonfigurálása információkat biztosít.  

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

Miután kiépítése, és indítsa el az Azure-SSIS integrációs futásidejű példánya, konfigurálhatja újra sorozatát futtatja `Stop`  -  `Set`  -  `Start` PowerShell-parancsmagok egymás után. Például a következő PowerShell-parancsfájl módosítja, az Azure-SSIS-integrációs futásidejű 5 példánya számára lefoglalt csomópontok száma.

## <a name="stop-azure-ssis-ir"></a>Állítsa le az Azure-SSIS-IR
Először állítsa le az Azure-SSIS-integrációs futásidejű használatával a [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmag. Ez a parancs kiadott összes csomópontját, és leállítja a számlázási.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="reconfigure-azure-ssis-ir"></a>Konfigurálja újra az Azure-SSIS-IR
Konfigurálja újra az Azure-SSIS-IR segítségével a [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmag. A következő minta parancs arányosan ki egy Azure-SSIS integrációs futásidejű öt csomópontokra.

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
```  

## <a name="start-azure-ssis-ir"></a>Indítsa el az Azure-SSIS-IR
Ezt követően indítsa el az Azure-SSIS-integrációs futásidejű segítségével a [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) parancsmag. Ez a parancs foglal le, minden csomópontja SSIS-csomagok futtatásához.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések
Azure-SSIS futásidejű kapcsolatos további információkért lásd a következő témaköröket: 

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-deploy-ssis-packages-azure.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál a felügyelt Azure SQL-példány (privát előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz (VNethez) való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a VNet oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson a virtuális hálózathoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
 
