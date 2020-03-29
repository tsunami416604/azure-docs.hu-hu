---
title: Az Azure Stream Analytics-feladat állapotai
description: Ez a cikk a Stream Analytics-feladat négy különböző állapotát ismerteti; futás, leáll, leromlott, és nem sikerült.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75359766"
---
# <a name="azure-stream-analytics-job-states"></a>Az Azure Stream Analytics-feladat állapotai

A Stream Analytics-feladat lehet egy négy állapot oka egy adott időpontban: futás, leállított, leromlott, vagy sikertelen. A feladat állapotát a Stream Analytics-feladat áttekintése lapon, az Azure Portalon találhatja meg. 

| Állapot | Leírás | Ajánlott műveletek |
| --- | --- | --- |
| **Fut** | A feladat fut az Azure olvasási események a megadott bemeneti forrásokból érkező, feldolgozva őket, és az eredmények írása a konfigurált kimeneti fogadók. | A [legfontosabb mutatók](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)figyelésével ajánlott nyomon követni a munka teljesítményét. |
| **Leállítva** | A feladat leáll, és nem dolgozza fel az eseményeket. | NA | 
| **Csökkentett teljesítményű** | Előfordulhat, hogy a bemeneti és kimeneti kapcsolatokkal időszakos problémák merülnek fel. Ezeket a hibákat átmeneti hibáknak nevezzük, amelyek miatt a feladat degradálódott állapotba léphet. A Stream Analytics azonnal megpróbálja helyreállítani az ilyen hibákat, és néhány percen belül visszatér a futó állapotba. Ezek a hibák hálózati problémák, más Azure-erőforrások rendelkezésre állása, deszerializálási hibák stb. A munka teljesítménye hatással lehet, ha a munka leromlott állapotban van.| Tekintse meg a [diagnosztikai vagy tevékenységnaplókat,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) ha többet szeretne megtudni az átmeneti hibák okairól. Olyan esetekben, mint például a deszerializálási hibák, ajánlott, hogy korrekciós intézkedéseket annak érdekében, hogy az események nem hibásan formázott. Ha a feladat továbbra is eléri az erőforrás-kihasználtsági korlátot, próbálja meg növelni az SU számát, vagy [párhuzamosítani a feladatot.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization) Más esetekben, amikor nem tud semmilyen műveletet végrehajtani, a Stream Analytics megpróbálja visszaállítani *a futó* állapotba. <br> [A vízjel késleltetési](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) metrikája segítségével megértheti, hogy ezek az átmeneti hibák hatással vannak-e a munka teljesítményére.|
| **Nem sikerült** | A feladat kritikus hibát észlelt, ami hibás állapotot eredményezett. Az események nem olvashatók és dolgoznak fel. A futásidejű hibák gyakori okai a sikertelen állapotba került feladatoknak. | [Beállíthatja a riasztásokat,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) hogy értesítést kapjon, ha a feladat sikertelen állapotba kerül. <br> <br>A [hibakeresés során tevékenység- és diagnosztikai naplók segítségével azonosíthatja](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) a kiváltó okot, és megoldhatja a problémát.|

## <a name="next-steps"></a>További lépések
* [Telepítési riasztások az Azure Stream Analytics-feladatokhoz](stream-analytics-set-up-alerts.md)
* [A Stream Analytics ben elérhető mérőszámok](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Tevékenység- és diagnosztikai naplók – problémamegoldás](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
