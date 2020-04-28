---
title: A szolgáltatások megszakításának elkerülése Azure Stream Analytics feladatokban
description: Ez a cikk útmutatást nyújt a Stream Analytics-feladatok rugalmas frissítésének végrehajtásához.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75425993"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>A szolgáltatás frissítéseinek Stream Analytics a feladatok megbízhatóságának garantálása

Egy teljes körűen felügyelt szolgáltatás része, amely gyors ütemben képes új szolgáltatás-funkcionalitást és-fejlesztési funkciókat bevezetni. Ennek eredményeképpen a Stream Analytics hetente (vagy még gyakoribb) is rendelkezhetnek a szolgáltatás frissítésével. Függetlenül attól, hogy mennyi tesztelés történik, még mindig fennáll annak a kockázata, hogy egy meglévő, futó feladatot egy hiba bevezetése miatt megszakadhat. Ha a feladatok kritikus fontosságú feladatait futtatja, ezeket a kockázatokat el kell kerülni. Ezt a kockázatot az Azure **[párosított régiós](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelljét követve csökkentheti. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hogyan kezeli az Azure párosított régiói ezt a problémát?

Stream Analytics garantálja, hogy a párosított régiókban lévő feladatok külön kötegekben frissüljenek. Ennek eredményeképpen a frissítések között elegendő idő van a lehetséges problémák azonosítására és azok szervizelésére.

Ha _a közép-India_ (amelynek a párosított régiója, Dél-India, nem rendelkezik stream Analytics jelenléttel), akkor a stream Analytics frissítésének telepítése nem fordulhat elő egyszerre a párosított régiókban. Ugyanabban a **csoportban** több régióban üzemelő példányok is előfordulhatnak **egyszerre.**

A **[rendelkezésre állással és a párosított régiókkal](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** kapcsolatos cikk a legfrissebb információkat tartalmaz a régiók párosításáról.

Azt javasoljuk, hogy azonos feladatokat helyezzen üzembe a párosított régiókban. Ezután [Figyelje ezeket a feladatokat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , hogy értesítést kapjon, ha valami váratlan esemény történik. Ha a feladatok egyike [sikertelen állapotba](https://docs.microsoft.com/azure/stream-analytics/job-states) kerül egy stream Analytics szolgáltatás frissítése után, forduljon az ügyfélszolgálathoz, és segít azonosítani a kiváltó okot. Az összes alsóbb rétegbeli fogyasztót is át kell adnia az egészséges feladat kimenetére.

## <a name="next-steps"></a>További lépések

* [Bevezetés a Stream Analyticsba](stream-analytics-introduction.md)
* [Ismerkedés a Stream Analytics szolgáltatással](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok skálázása](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezés nyelvi referenciája](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
