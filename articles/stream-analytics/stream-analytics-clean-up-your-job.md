---
title: Az Azure Stream Analytics-feladat tisztítása
description: Ez a cikk útmutatást adnak Azure Stream Analytics-feladatok törlése.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 4773f542f12ae1773e881106bc8948c663bfd1e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660923"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Az Azure Stream Analytics-feladat tisztítása

Az Azure Stream Analytics-feladatok egyszerűen törölheti az Azure portálon, az Azure PowerShell Azure SDK for .net vagy a REST API-n keresztül.

>[!NOTE] 
>Ha kikapcsolja a Stream Analytics-feladat, az adatok csak a bemeneti és kimeneti tárolására, például az Event Hubs vagy az Azure SQL Database tartja fenn. Ha meg kell adatokat távolítja el az Azure-ból, ügyeljen arra, hogy hajtsa végre az eltávolítási folyamat a bemeneti és kimeneti erőforrásokhoz a Stream Analytics-feladat.

## <a name="stop-a-job-in-azure-portal"></a>Egy Azure-portálon feladat leállítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Keresse meg a futó Stream Analytics-feladat, és válassza ki azt.

3. A Stream Analytics-feladat lapon válassza az **leállítása** leállítja a feladatot. 

   ![Feladat leállítása](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Törli a feladatot az Azure portálon

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics-feladat, és válassza ki azt.

3. A Stream Analytics-feladat lapon válassza az **törlése** törli a feladatot. 

   ![Feladat törlése](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Állítsa le vagy a PowerShell használatával feladat törlése

A PowerShell használatával feladat leállításához használja a [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) parancsmag. A PowerShell használatával feladat törléséhez használja a [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) parancsmag.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Állítsa le vagy Azure SDK for .NET használatával feladat törlése

Azure SDK for .NET használatával egy feladat leállításához használja a [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metódust. Azure SDK for .NET használatával feladat törlése [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metódust.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Állítsa le vagy a REST API használatával feladat törlése

Leállítja a REST API használatával feladatot, tekintse meg a [leállítása](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#stop) metódust. Egy feladat REST API használatával törli, tekintse meg a [törlése](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#delete) metódust.