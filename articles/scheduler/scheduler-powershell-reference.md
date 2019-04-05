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
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044997"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Az Azure Scheduler PowerShell-parancsmagok leírása

> [!IMPORTANT]
> A kivezetésre kerülő Azure Scheduler helyébe az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) lép. Feladatok ütemezéséhez [próbálja ki inkább az Azure Logic Apps szolgáltatást](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hozzon létre-parancsprogramok a Scheduler-feladatok és feladatgyűjtemények létrehozását és kezelését, használhatja a PowerShell-parancsmagokat. Ez a cikk felsorolja a fő [az Azure Scheduler PowerShell-parancsmagok](/powershell/module/azurerm.scheduler) referencia cikkeik mutató hivatkozásokkal. Azure PowerShell telepítése az Azure-előfizetés: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview). További információ [Azure Resource Manager parancsmagjainak](/powershell/azure/overview), lásd: [az Azure PowerShell az Azure Resource Manager](../powershell-azure-resource-manager.md).

| Parancsmag | Leírás |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |A feladatgyűjtemény letiltása. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |Lehetővé teszi a feladatgyűjteményeket. |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |Scheduler-feladatok beolvasása. |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |Feladatgyűjtemények beolvasása |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |A feladatelőzmények beolvasása. |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |Létrehoz egy HTTP-feladatot. |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |Egy feladat gyűjteményt hoz létre. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | Létrehoz egy Service Bus-üzenetsor feladatot. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |Létrehoz egy Service Bus-témakör feladatot. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |Tárolási üzenetsor feladatot hoz létre. |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |Eltávolít egy Scheduler-feladat. |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |A feladatgyűjtemény eltávolítja. |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |Módosítja egy Scheduler HTTP-feladatot. |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |A feladatgyűjtemény módosítja. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |Módosítja egy Service Bus-üzenetsor feladat. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |Módosítja egy Service Bus-témakör feladatot. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |Módosítja a tárolási üzenetsor feladat. |
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
