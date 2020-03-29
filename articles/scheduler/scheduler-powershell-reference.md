---
title: PowerShell-parancsmagok – referencia
description: Tudnivalók az Azure Scheduler PowerShell-parancsmagjairól
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898484"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>PowerShell-parancsmagok hivatkozása az Azure Scheduler számára

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb [telepítse át az Azure Logic Apps-alkalmazásokba.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

Az Ütemező feladatok és feladatgyűjtemények létrehozásához és kezeléséhez parancsfájlokat hozhat létre, használhatja a PowerShell-parancsmagokat. Ez a cikk felsorolja az Azure Scheduler fő PowerShell-parancsmagjait a referenciacikkeikre mutató hivatkozásokkal. Az Azure PowerShell Azure-előfizetéshez való telepítéséről az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben szól. Az Azure [Resource Manager-parancsmagjairól](/powershell/azure/overview)az [Azure PowerShell használata az Azure Resource Manager rel](../powershell-azure-resource-manager.md)című témakörben talál további információt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Parancsmag | Leírás |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Letiltja a feladatgyűjteményt. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Lehetővé teszi a feladatgyűjteményt. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Beszerzi az ütemezői feladatokat. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Állásgyűjteményeket kap. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Megszerzi a munka történetét. |
| [New-AzSchedulerhttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |HTTP-feladat létrehozása. |
| [Új-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Feladatgyűjtemény létrehozása. |
| [Új-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Létrehoz egy Service Bus várólista-feladatot. |
| [Új-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Létrehoz egy Service Bus témakör feladatot. |
| [Új-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tárolási várólista-feladatot hoz létre. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Egy ütemezőfeladat eltávolítása. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Eltávolítja a feladatgyűjteményt. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Módosítja az ütemező HTTP-feladatát. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Módosítja a feladatgyűjteményt. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Módosítja a Service Bus várólista-feladat. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Módosítja a Service Bus témakör feladat. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Módosítja a tárolási várólista-feladatot. |
||| 

További részletekért futtathatja a következő parancsmagokat: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)