---
title: PowerShell-parancsmagok hivatkozása – Azure Scheduler
description: Tudnivalók az Azure Scheduler PowerShell-parancsmagjai
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 1ad1d6b9f59fa51b9e27fe5b70ce4e2a5d36f3ad
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300878"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>PowerShell-parancsmagok referenciája az Azure Scheduler szolgáltatáshoz

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Scheduler-feladatok és-webhelycsoportok létrehozására és kezelésére szolgáló parancsfájlok létrehozásához PowerShell-parancsmagokat használhat. Ez a cikk az Azure Scheduler fő PowerShell-parancsmagait sorolja fel a hivatkozási cikkeire mutató hivatkozásokkal. Az Azure-előfizetéshez Azure PowerShell telepítéséhez tekintse meg a [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakört. [Azure Resource Manager parancsmagokkal](/powershell/azure/overview)kapcsolatos további információkért lásd: [Azure PowerShell használata a Azure Resource Manager használatával](../powershell-azure-resource-manager.md).

| A parancsmag | Leírás |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |A feladatütemezés letiltása. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Lehetővé teszi a feladatok gyűjtését. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Ütemező feladatok beolvasása. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Beolvassa a feladatok gyűjteményeit. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Beolvassa a feladatok előzményeit. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Létrehoz egy HTTP-feladatot. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Létrehoz egy feladatot gyűjteményt. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Létrehoz egy Service Bus üzenetsor-feladatot. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Létrehoz egy Service Bus témakör-feladatot. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Létrehoz egy tárolási várólista-feladatot. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Eltávolít egy Feladatütemező feladatot. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Eltávolít egy feladatot. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Módosítja egy ütemező HTTP-feladatot. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |A feladatütemezés módosítása. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Módosítja egy Service Bus üzenetsor-feladatot. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Módosítja egy Service Bus témakör-feladatot. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Módosít egy tárolási várólista-feladatot. |
||| 

További részletekért futtassa a következő parancsmagok bármelyikét: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Alapfogalmak, terminológia és entitáshierarchia](scheduler-concepts-terms.md)
* [Az első feladatok létrehozása és beütemezés – Azure Portal](scheduler-get-started-portal.md)
* [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)
