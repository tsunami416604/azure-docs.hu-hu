---
title: Azure Stream Analytics-feladat indítása
description: Ez a cikk ismerteti a Stream Analytics-feladat elindítása.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886847"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat indítása

Az Azure Stream Analytics-feladat az Azure Portalon, a Visual Studio és a PowerShell használatával elindíthatja. Amikor elindít egy feladatot, ki kell választania egy idő, amíg a feladat kimeneti létrehozásának megkezdéséhez. Az Azure portal, a Visual Studio és a PowerShell is állítja a kezdési időpont különböző módszereket. Azokat a módszereket az alábbiakban tekintheti át.

## <a name="azure-portal"></a>Azure Portal

Keresse meg a feladatot az Azure Portalon, és válassza a **Start** az Áttekintés oldalon. Válassza ki a **feladatkimenet kezdési idő** majd **Start**.

A három lehetőség áll rendelkezésre **feladatkimenet kezdési idő**: *Most már*, *egyéni*, és *utolsó leállítás időpontjában*. Kiválasztásával *most* elindítja a feladatot az aktuális időpontban. Kiválasztásával *egyéni* lehetővé teszi, hogy egy egyéni idő beállítása a múltban vagy a jövőben a feladat elindításához. Adatok elvesztése nélkül egy leállított feladat folytatásához válasszon *utolsó leállítás időpontjában*.

## <a name="visual-studio"></a>Visual Studio

A feladatok nézetben válassza ki a zöld nyílra a feladat indításához. Állítsa be a **feladat kimeneti mód indítása** válassza **Start**. A feladat állapota változik **futó**.

A három lehetőség áll rendelkezésre **feladat kimeneti mód indítása**: *JobStartTime*, *CustomTime*, és *LastOutputEventTime*. Ez a tulajdonság hiányzik, ha az alapértelmezett érték *JobStartTime*.

*JobStartTime* lehetővé teszi a kiindulási pont, a kimeneti esemény streamelése ugyanaz, mint amikor a feladat elindult.

*CustomTime* elindítja a kimenet van megadva az egyéni egyszerre a *OutputStartTime* paraméter.

*LastOutputEventTime* lehetővé teszi a kiindulási pont, a kimeneti eseménystream ugyanaz, mint az utolsó esemény kimeneti idő.

## <a name="powershell"></a>PowerShell

A PowerShell-lel feladat indításához használja a következő parancsmagot:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

A három lehetőség áll rendelkezésre **OutputStartMode**: *JobStartTime*, *CustomTime*, és *LastOutputEventTime*. Ez a tulajdonság hiányzik, ha az alapértelmezett érték *JobStartTime*.

*JobStartTime* lehetővé teszi a kiindulási pont, a kimeneti esemény streamelése ugyanaz, mint amikor a feladat elindult.

*CustomTime* elindítja a kimenet van megadva az egyéni egyszerre a *OutputStartTime* paraméter.

*LastOutputEventTime* lehetővé teszi a kiindulási pont, a kimeneti eseménystream ugyanaz, mint az utolsó esemény kimeneti idő.

További információ a `Start-AzStreamAnalyitcsJob` parancsmagot, a nézet a [Start-AzStreamAnalyticsJob referencia](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)
* [Rövid útmutató: Azure PowerShell-lel a Stream Analytics-feladat létrehozása](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Stream Analytics-feladat létrehozása az Azure Stream Analytics tools for Visual Studio használatával](stream-analytics-quick-create-vs.md)
