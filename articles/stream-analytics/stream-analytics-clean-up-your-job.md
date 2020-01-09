---
title: Azure Stream Analytics feladatainak törlése
description: Ez a cikk a Azure Stream Analytics feladatok törlésének különböző módszereit mutatja be.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426486"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Azure Stream Analytics-feladatok leállítása vagy törlése

Azure Stream Analytics feladatok egyszerűen leállíthatók vagy törölhetők a Azure Portal, a Azure PowerShell, az Azure SDK for .net vagy a REST API használatával. Egy Stream Analytics feladatot nem lehet helyreállítani, ha törölték.

>[!NOTE] 
>Ha leállítja a Stream Analytics feladatot, az adatok csak a bemeneti és a kimeneti tárolóban maradnak, például Event Hubs vagy Azure SQL Database. Ha el kell távolítania az adatokat az Azure-ból, kövesse a Stream Analytics feladat bemeneti és kimeneti erőforrásainak eltávolítási folyamatát.

## <a name="stop-a-job-in-azure-portal"></a>Feladatok leállítása Azure Portal

Ha leállítja a feladatot, az erőforrások kiépítése megszűnik, és leállítja az események feldolgozását. A feladathoz kapcsolódó díjak is leállnak. Az összes konfiguráció azonban megmarad, és később újraindíthatja a feladatot 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

2. Keresse meg a futó Stream Analytics feladatot, és válassza ki.

3. A Stream Analytics feladatok lapon válassza a **Leállítás** lehetőséget a feladatok leállításához. 

   ![Azure Stream Analytics feladatok leállítása](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Feladatok törlése Azure Portal

>[!WARNING] 
>Egy Stream Analytics feladatot nem lehet helyreállítani, ha törölték.

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics feladatot, és válassza ki.

3. A Stream Analytics feladatok lapon válassza a **Törlés** lehetőséget a feladatok törléséhez. 

   ![Azure Stream Analytics-feladatok törlése](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Feladatok leállítása vagy törlése a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha a PowerShell használatával szeretné leállítani a feladatot, használja a [stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) parancsmagot. Ha egy feladatot a PowerShell használatával szeretne törölni, használja a [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) parancsmagot.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Feladatok leállítása vagy törlése a .NET-hez készült Azure SDK-val

Ha a .NET-hez készült Azure SDK-val szeretné leállítani a feladatot, használja a [StreamingJobsOperationsExtensions. BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metódust. Feladatok törlése a .NET-hez készült Azure SDK-val, a [StreamingJobsOperationsExtensions. BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metódussal.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Feladatok leállítása vagy törlése REST API használatával

Ha REST APIt használó feladatot szeretne leállítani, tekintse meg a [Leállítás](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metódust. Ha REST API használatával szeretne feladatot törölni, tekintse meg a [delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metódust.
