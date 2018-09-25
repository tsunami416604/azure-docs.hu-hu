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
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991096"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Az Azure Scheduler PowerShell-parancsmagok leírása

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) kivezetjük, az Azure Scheduler lecseréli. Feladatok ütemezése [helyette próbálkozzon az Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Hozzon létre-parancsprogramok a Scheduler-feladatok és feladatgyűjtemények létrehozását és kezelését, használhatja a PowerShell-parancsmagokat. Ez a cikk felsorolja a fő [az Azure Scheduler PowerShell-parancsmagok](/powershell/module/azurerm.scheduler) referencia cikkeik mutató hivatkozásokkal. Azure PowerShell telepítése az Azure-előfizetés: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview). További információ [Azure Resource Manager parancsmagjainak](/powershell/azure/overview), lásd: [az Azure PowerShell az Azure Resource Manager](../powershell-azure-resource-manager.md).

| Parancsmag | Leírás |
|--------|-------------|
| [Disable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |A feladatgyűjtemény letiltása. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Lehetővé teszi a feladatgyűjteményeket. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Scheduler-feladatok beolvasása. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Feladatgyűjtemények beolvasása |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |A feladatelőzmények beolvasása. |
| [Új AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Létrehoz egy HTTP-feladatot. |
| [Új AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Egy feladat gyűjteményt hoz létre. |
| [Új AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Létrehoz egy Service Bus-üzenetsor feladatot. |
| [Új AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Létrehoz egy Service Bus-témakör feladatot. |
| [Új AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tárolási üzenetsor feladatot hoz létre. |
| [Remove-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Eltávolít egy Scheduler-feladat. |
| [Remove-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |A feladatgyűjtemény eltávolítja. |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Módosítja egy Scheduler HTTP-feladatot. |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |A feladatgyűjtemény módosítja. |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Módosítja egy Service Bus-üzenetsor feladat. |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Módosítja egy Service Bus-témakör feladatot. |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Módosítja a tárolási üzenetsor feladat. |
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
