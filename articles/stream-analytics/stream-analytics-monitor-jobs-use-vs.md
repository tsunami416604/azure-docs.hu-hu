---
title: Azure Stream Analytics monitorozása és kezelése a Visual Studióval
description: Ez a cikk azt ismerteti, hogyan használható a Visual Studio Azure Stream Analytics feladatok figyelésére és felügyeletére.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a151d4ce5a2f9860c3b7919496a2edd25cc58ede
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123625"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Stream Analytics feladatok figyelése és kezelése a Visual Studióval

Ez a cikk bemutatja, hogyan figyelheti meg a Stream Analytics-feladatot a Visual Studióban. A Visual studióhoz készült Azure Stream Analytics Tools a Azure Portalhoz hasonló figyelési élményt nyújt, anélkül, hogy el kellene hagynia az IDE-t. Megkezdheti a feladatok figyelését, amint az Azure-ba **küldi** a **szkriptből. asaql** , vagy a meglévő feladatokat a létrehozásuk módjától függetlenül is figyelheti. 

## <a name="job-summary"></a>Feladatok összegzése

A feladatok **összegzése** és a **feladatok mérőszámai** gyors pillanatképet adnak a feladatról. Egy pillantással meghatározhatja a feladatok állapotát és az események adatait.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Feladatok metrikái

Összecsukhatja a **feladatok összegzését** , és a **feladatok mérőszámai** lapra kattintva megtekintheti a fontos metrikákkal rendelkező diagramokat. A mérőszámok típusának bejelölésével és eltávolításával a diagramon adhatja hozzá és távolíthatja el azokat.

![Metrikák Stream Analytics a Visual Studióban](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Hiba figyelése

A hibákat a **hibák** lapra kattintva is figyelheti.

![Hibák Stream Analytics a Visual Studióban](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Azure Stream Analytics-feladatok létrehozása a Visual Studióval](stream-analytics-quick-create-vs.md)
* [A Visual studióhoz készült Azure Stream Analytics-eszközök telepítése](stream-analytics-tools-for-visual-studio-install.md)