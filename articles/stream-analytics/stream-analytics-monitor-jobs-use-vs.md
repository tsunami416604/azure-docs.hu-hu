---
title: Az Azure Stream Analytics figyelése és kezelése a Visual Studio segítségével
description: Ez a cikk azt ismerteti, hogy miként figyelheti és kezelheti a Visual Studio az Azure Stream Analytics-feladatokat.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e43a8d157baaf7e02ab7f8db4c777009d2a0abe9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431694"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>A Stream Analytics-feladatok figyelése és kezelése a Visual Studióval

Ez a cikk bemutatja, hogyan figyelheti a Stream Analytics-feladatot a Visual Studióban. A Visual Studio Azure Stream Analytics-eszközei az Azure Portalhoz hasonló figyelési élményt nyújt anélkül, hogy el kellene hagynia az IDE-t. Megkezdheti a feladat figyelése, amint **elküldi az Azure-ba** a **Script.asaql,** vagy figyelheti a meglévő feladatokat, függetlenül attól, hogy hogyan jöttek létre. 

## <a name="job-summary"></a>Feladat összegzése

A **feladat összegzése** és **a feladat metrikák** ad egy gyors pillanatképet a feladatról. Egy pillantással meghatározhatja a feladat állapotát és eseményadatait.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Feladatmérőszámok

Összecsukhatja a **Feladat összegzését,** és a **Projekt mutatók** fülre kattintva megtekintheti a fontos mutatókkal rendelkező grafikont. Ellenőrizze és törölje a metrikák típusait, és távolítsa el őket a grafikonról.

![A Stream Analytics mérőszámai a Visual Studióban](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Hibafigyelés

A hibákat a **Hibák** fülre kattintva is figyelheti.

![Stream Analytics-hibák a Visual Studióban](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) 

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Azure Stream Analytics-feladat létrehozása a Visual Studio segítségével](stream-analytics-quick-create-vs.md)
* [Az Azure Stream Analytics-eszközök telepítése a Visual Studio-hoz](stream-analytics-tools-for-visual-studio-install.md) 


