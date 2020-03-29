---
title: A szolgáltatás megszakításának elkerülése az Azure Stream Analytics-feladatokban
description: Ez a cikk útmutatást nyújt a Stream Analytics-feladatok frissítésének rugalmassá tételéhez.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425993"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>A Stream Analytics-feladat megbízhatóságának garantálása a szolgáltatásfrissítések során

A teljes körűen felügyelt szolgáltatás része az a képesség, hogy gyors ütemben új szolgáltatási funkciókat és fejlesztéseket vezessenek be. Ennek eredményeképpen a Stream Analytics heti (vagy gyakoribb) szolgáltatásfrissítés-telepítéssel rendelkezhet. Nem számít, hogy mennyi tesztelést végeznek, még mindig fennáll annak a veszélye, hogy egy meglévő, futó feladat megszakadhat egy hiba bevezetése miatt. Ha kritikus feladatokat végez, ezeket a kockázatokat el kell kerülni. Ezt a kockázatot csökkentheti az Azure **[párosított régiómodelljének](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** követésével. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hogyan kezelik az Azure párosított régióit ez az aggodalom?

A Stream Analytics garantálja, hogy a párosított régiókban lévő feladatok külön kötegekben frissülnek. Ennek eredményeképpen elegendő időeltolódás van a frissítések között a lehetséges problémák azonosításához és kiújításához.

_Közép-India kivételével_ (amelynek párosított régiója, Dél-India nem rendelkezik Stream Analytics jelenléttel), a Stream Analytics frissítésének telepítése nem történik meg egyidejűleg egy párosított régiókészletben. **A csoport** több régiójában egyszerre több helyen is **előfordulhatnak**központi telepítések.

A **[rendelkezésre állási és párosított régiókról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** szóló cikk a legfrissebb információkat tartalmazza a párosított régiókról.

Mindkét párosított régióban ajánlott azonos feladatokat telepíteni. Ezután [figyelnie kell ezeket a feladatokat,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) hogy értesítést kapjon, ha valami váratlan történik. Ha a Stream Analytics szolgáltatás frissítése után a feladatok valamelyike [sikertelen állapotba](https://docs.microsoft.com/azure/stream-analytics/job-states) kerül, forduljon az ügyfélszolgálathoz, hogy azonosítsa a kiváltó okot. Azt is át kell adnia a feldolgozó felhasználók számára az egészséges feladat kimenet.

## <a name="next-steps"></a>További lépések

* [Bevezetés a Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Ismerkedés a Stream Analytics szolgáltatással](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok skálázása](stream-analytics-scale-jobs.md)
* [A Stream Analytics lekérdezési nyelvének hivatkozása](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics felügyeleti REST API-hivatkozás](https://msdn.microsoft.com/library/azure/dn835031.aspx)
