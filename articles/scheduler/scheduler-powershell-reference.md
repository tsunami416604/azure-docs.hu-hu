---
title: A PowerShell-parancsmagok leírása – Azure Scheduler szolgáltatásával
description: Az Azure Scheduler PowerShell-parancsmagokkal kapcsolatos további
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 4b179c50af8b1ffc4313a49da978f178915ec9cc
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489896"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Az Azure Scheduler PowerShell-parancsmagok leírása

> [!IMPORTANT]
> A kivezetésre kerülő Azure Scheduler helyébe az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) lép. Feladatok ütemezéséhez [próbálja ki inkább az Azure Logic Apps szolgáltatást](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hozzon létre-parancsprogramok a Scheduler-feladatok és feladatgyűjtemények létrehozását és kezelését, használhatja a PowerShell-parancsmagokat. Ez a cikk a fő PowerShell-parancsmagok felsorolja az Azure Scheduler referencia cikkeik mutató hivatkozásokkal. Azure PowerShell telepítése az Azure-előfizetés: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview). További információ [Azure Resource Manager parancsmagjainak](/powershell/azure/overview), lásd: [az Azure PowerShell az Azure Resource Manager](../powershell-azure-resource-manager.md).

| Parancsmag | Leírás |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |A feladatgyűjtemény letiltása. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Lehetővé teszi a feladatgyűjteményeket. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Scheduler-feladatok beolvasása. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Feladatgyűjtemények beolvasása |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |A feladatelőzmények beolvasása. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Létrehoz egy HTTP-feladatot. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Egy feladat gyűjteményt hoz létre. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Létrehoz egy Service Bus-üzenetsor feladatot. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Létrehoz egy Service Bus-témakör feladatot. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tárolási üzenetsor feladatot hoz létre. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Eltávolít egy Scheduler-feladat. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |A feladatgyűjtemény eltávolítja. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Módosítja egy Scheduler HTTP-feladatot. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |A feladatgyűjtemény módosítja. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Módosítja egy Service Bus-üzenetsor feladat. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Módosítja egy Service Bus-témakör feladatot. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Módosítja a tárolási üzenetsor feladat. |
||| 

További részletekért futtassa ezeket a parancsmagokat: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Létrehozása és ütemezése az első feladat – az Azure Portalon](scheduler-get-started-portal.md)
* [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)
