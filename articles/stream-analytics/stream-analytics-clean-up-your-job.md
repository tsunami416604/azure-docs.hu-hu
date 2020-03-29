---
title: Az Azure Stream Analytics-feladat karbantartása
description: Ez a cikk az Azure Stream Analytics-feladatok törlésének különböző módszereit mutatja be.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426486"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Az Azure Stream Analytics-feladat leállítása vagy törlése

Az Azure Stream Analytics-feladatok könnyen leállíthatók vagy törölhetők az Azure Portalon, az Azure PowerShellen, az Azure SDK-n keresztül a .Net-hez vagy a REST API-n keresztül. A Stream Analytics-feladat nem állítható vissza, miután törölték.

>[!NOTE] 
>Amikor leállítja a Stream Analytics-feladat, az adatok csak a bemeneti és kimeneti tároló, például az Event Hubs vagy az Azure SQL Database. Ha adatokat kell eltávolítania az Azure-ból, mindenképpen kövesse a Stream Analytics-feladat bemeneti és kimeneti erőforrásainak eltávolítási folyamatát.

## <a name="stop-a-job-in-azure-portal"></a>Feladat leállítása az Azure Portalon

Amikor leállít egy feladatot, az erőforrások kilesznek építve, és leállítja az események feldolgozását. A feladattal kapcsolatos költségek is levannak állítva. Azonban az összes konfiguráció megmarad, és később újraindíthatja a feladatot 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

2. Keresse meg a futó Stream Analytics-feladatot, és válassza ki.

3. A Stream Analytics feladatlapon válassza a **Leállítás** lehetőséget a feladat leállításához. 

   ![Az Azure Stream Analytics-feladat leállítása](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Feladat törlése az Azure Portalon

>[!WARNING] 
>A Stream Analytics-feladat nem állítható vissza, miután törölték.

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg meglévő Stream Analytics-feladatát, és válassza ki.

3. A Stream Analytics feladat lapon válassza a **Törlés** lehetőséget a feladat törléséhez. 

   ![Az Azure Stream Analytics feladat törlése](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Feladat leállítása vagy törlése a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell használatával végzett feladat leállításához használja a [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) parancsmag. Egy feladat törlése a PowerShell használatával, használja az [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) parancsmag.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Feladat leállítása vagy törlése az Azure SDK for .NET használatával

Ha le szeretne állítani egy feladatot az Azure SDK for .NET használatával, használja a [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metódust. Ha törölni szeretne egy feladatot az Azure SDK használatával a .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metódus.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Feladat leállítása vagy törlése a REST API használatával

A REST API használatával végzett feladat leállításához olvassa el a [Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metódust. Ha törölni szeretne egy feladatot a REST API használatával, olvassa el a [Törlés](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metódust.
