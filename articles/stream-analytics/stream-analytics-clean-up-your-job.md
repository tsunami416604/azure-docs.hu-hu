---
title: Az Azure Stream Analytics-feladat törlése
description: Ez a cikk bemutatja az Azure Stream Analytics-feladat törlésének különböző módszereket.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: cb81c73f7946a10bae0470a55dcf1c0d55c2b847
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330050"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Állítsa le vagy az Azure Stream Analytics-feladat törlése

Az Azure Stream Analytics-feladatok egyszerűen leállítható vagy az Azure portal, Azure PowerShell vagy az Azure SDK for .net vagy REST API-n keresztül törölték. Stream Analytics-feladat nem állítható helyre, ha törölve lett.

>[!NOTE] 
>Amikor leállítja a Stream Analytics-feladatot, az adatok továbbra is fennáll csak a bemeneti és kimeneti tárolására, például az Event Hubs vagy Azure SQL Database. Adatok eltávolítása az Azure-ból szükségesek, ha mindenképpen hajtsa végre az eltávolítási folyamat bemeneti és kimeneti erőforrások a Stream Analytics-feladat.

## <a name="stop-a-job-in-azure-portal"></a>Állítsa le a feladatot az Azure Portalon

Amikor egy feladatot leállítja, az erőforrások deprovisionned, és leállítja az események feldolgozását. Ez a feladat kapcsolódó költségek is le lesz állítva. Azonban őrzi meg az összes konfigurációs és, indítsa újra a feladatot 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Keresse meg a futó Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics-feladat oldalán válassza **leállítása** leállítani a feladatot. 

   ![Az Azure Stream Analytics-feladat leállítása](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Az Azure Portalon egy feladat törlése

>[!WARNING] 
>Stream Analytics-feladat nem állítható helyre, ha törölve lett.

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics-feladat oldalán válassza **törlése** törli a feladatot. 

   ![Az Azure Stream Analytics-feladat törlése](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Állítsa le vagy a PowerShell használatával egy feladat törlése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy PowerShell-lel feladat leállításához használja a [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) parancsmagot. Egy PowerShell-lel feladat törléséhez használja a [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) parancsmagot.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Állítsa le vagy az Azure SDK for .NET feladat törlése

Az Azure SDK for .NET feladat leállításához használja a [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metódust. Az Azure SDK for .NET-feladat törlése [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metódust.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Állítsa le vagy a REST API használatával egy feladat törlése

Le szeretné állítani egy feladatot, a REST API használatával, tekintse meg a [leállítása](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metódust. Egy feladat, a REST API használatával törli, tekintse meg a [törlése](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metódust.
