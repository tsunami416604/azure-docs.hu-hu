---
title: 'PowerShell-parancsfájl: Az Azure-beli adatmegosztás szinkronizálási beállításainak megadása és megtekintése | Microsoft Docs'
description: Ez a PowerShell-szkript beállítja és megosztja a szinkronizálási beállításokat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 14f1925649a665b0e63ac37ef9ec7e3469042e3e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242943"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Az elküldett adatmegosztások használatának figyelése a PowerShell használatával

Ez a PowerShell-szkript beállítja és megosztja a szinkronizálási beállításokat.

## <a name="sample-script"></a>Példaszkript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Új – AzDataShareSynchronizationSetting](/powershell/module/az.resources/new-azdatasharesynchronizationsettings) | Hozzon létre egy megosztási szinkronizálást. |
| [Get-AzDataShareSynchronizationSetting](/powershell/module/az.resources/get-azdatasharesynchronizationsetting) | Beolvassa a megosztás szinkronizálásának beállításait. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
