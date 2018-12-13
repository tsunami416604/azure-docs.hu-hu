---
title: Az Azure Stream Analytics-feladat törlése
description: Ez a cikk bemutatja az Azure Stream Analytics-feladat törlésének különböző módszereket.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 85db38fef5e69c4de855f8cb6d54151496faebbe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090244"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Az Azure Stream Analytics-feladat törlése

Az Azure Stream Analytics-feladatok az Azure portal, Azure PowerShell vagy az Azure SDK for .net vagy REST API segítségével egyszerűen lehet törölni.

>[!NOTE] 
>Amikor leállítja a Stream Analytics-feladatot, az adatok továbbra is fennáll csak a bemeneti és kimeneti tárolására, például az Event Hubs vagy Azure SQL Database. Adatok eltávolítása az Azure-ból szükségesek, ha mindenképpen hajtsa végre az eltávolítási folyamat bemeneti és kimeneti erőforrások a Stream Analytics-feladat.

## <a name="stop-a-job-in-azure-portal"></a>Állítsa le a feladatot az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Keresse meg a futó Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics-feladat oldalán válassza **leállítása** leállítani a feladatot. 

   ![Az Azure Stream Analytics-feladat leállítása](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Az Azure Portalon egy feladat törlése

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics-feladat oldalán válassza **törlése** törli a feladatot. 

   ![Az Azure Stream Analytics-feladat törlése](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Állítsa le vagy a PowerShell használatával egy feladat törlése

Egy PowerShell-lel feladat leállításához használja a [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) parancsmagot. Egy PowerShell-lel feladat törléséhez használja a [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) parancsmagot.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Állítsa le vagy az Azure SDK for .NET feladat törlése

Az Azure SDK for .NET feladat leállításához használja a [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metódust. Az Azure SDK for .NET-feladat törlése [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metódust.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Állítsa le vagy a REST API használatával egy feladat törlése

Le szeretné állítani egy feladatot, a REST API használatával, tekintse meg a [leállítása](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metódust. Egy feladat, a REST API használatával törli, tekintse meg a [törlése](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metódust.
