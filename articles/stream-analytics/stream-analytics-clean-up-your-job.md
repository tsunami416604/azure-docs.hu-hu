---
title: Az Azure Stream Analytics-feladat törlése
description: Ez a cikk bemutatja az Azure Stream Analytics-feladat törlésének különböző módszereket.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 093ada2b2751540b986154be3a2f333784c1259f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173285"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Azure Stream Analytics-feladatok leállítása vagy törlése

Azure Stream Analytics feladatok egyszerűen leállíthatók vagy törölhetők a Azure Portal, a Azure PowerShell, az Azure SDK for .net vagy a REST API használatával. Egy Stream Analytics feladatot nem lehet helyreállítani, ha törölték.

>[!NOTE] 
>Amikor leállítja a Stream Analytics-feladatot, az adatok továbbra is fennáll csak a bemeneti és kimeneti tárolására, például az Event Hubs vagy Azure SQL Database. Adatok eltávolítása az Azure-ból szükségesek, ha mindenképpen hajtsa végre az eltávolítási folyamat bemeneti és kimeneti erőforrások a Stream Analytics-feladat.

## <a name="stop-a-job-in-azure-portal"></a>Állítsa le a feladatot az Azure Portalon

Ha leállítja a feladatot, az erőforrások kiépítése megszűnik, és leállítja az események feldolgozását. A feladathoz kapcsolódó díjak is leállnak. Az összes konfiguráció azonban megmarad, és később újraindíthatja a feladatot 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Keresse meg a futó Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics-feladat oldalán válassza **leállítása** leállítani a feladatot. 

   ![Az Azure Stream Analytics-feladat leállítása](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Az Azure Portalon egy feladat törlése

>[!WARNING] 
>Egy Stream Analytics feladatot nem lehet helyreállítani, ha törölték.

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics-feladat oldalán válassza **törlése** törli a feladatot. 

   ![Az Azure Stream Analytics-feladat törlése](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Állítsa le vagy a PowerShell használatával egy feladat törlése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha a PowerShell használatával szeretné leállítani a feladatot, használja a [stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) parancsmagot. Ha egy feladatot a PowerShell használatával szeretne törölni, használja a [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) parancsmagot.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Állítsa le vagy az Azure SDK for .NET feladat törlése

Az Azure SDK for .NET feladat leállításához használja a [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metódust. Az Azure SDK for .NET-feladat törlése [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metódust.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Állítsa le vagy a REST API használatával egy feladat törlése

Le szeretné állítani egy feladatot, a REST API használatával, tekintse meg a [leállítása](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metódust. Egy feladat, a REST API használatával törli, tekintse meg a [törlése](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metódust.
