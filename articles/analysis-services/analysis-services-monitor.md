---
title: "Azure Analysis Services-kiszolgáló metrikát |} Microsoft Docs"
description: "Útmutató: az Analysis Services-kiszolgáló metrikát az Azure-portálon."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/14/2017
ms.author: owend
ms.openlocfilehash: 8d19f2d29673364c50cb481e375f2c1b5e027467
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="monitor-server-metrics"></a>A figyelő kiszolgálói metrikák

Analysis Services biztosít a mérni kívánt a teljesítmény és a kiszolgálók állapotának figyeléséhez nyújt segítséget. Például figyelheti a memória és CPU-használat, ügyfélkapcsolatokat, és a lekérdezés hálózatierőforrás-fogyasztás száma. Analysis Services figyelési kerete használja, mint a más Azure-szolgáltatásokkal. További tudnivalókért lásd: [a Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

További részletes diagnosztikai végrehajtásához nyomon követése teljesítményét, és azonosíthatja a trendeket egy erőforráscsoportba vagy előfizetésbe erőforrásairól szolgáltatás között, használjon [Azure figyelő](https://azure.microsoft.com/services/monitor/). Azure Monitor (szolgáltatás) egy számlázható szolgáltatás létrejöttét eredményezheti.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Egy Analysis Services-kiszolgáló metrikáját figyelése

1. Azure-portálon, válassza ki a **metrikák**.

    ![Azure-portálon figyelése](./media/analysis-services-monitor/aas-monitor-portal.png)

2. A **elérhető**, válassza ki a mérni kívánt a diagramban fel. 

    ![A figyelő diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>
## <a name="server-metrics"></a>Az állapotmonitor metrikákat
A táblázat segítségével határozhatja meg, mely adatok gyűjtése le a figyelési forgatókönyvben ajánlott. Csak az azonos egység metrikák is megjeleníthetők ugyanabban a diagramban.

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|
|---|---|---|---|---|
|CommandPoolJobQueueLength|A parancs készlet feladat várólistájának hossza|Darabszám|Átlag|A parancs szálkészlet sorban lévő feladatok száma.|
|CurrentConnections|Kapcsolat: A jelenlegi kapcsolatok száma|Darabszám|Átlag|Ügyfél fennálló kapcsolatok aktuális száma.|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Darabszám|Átlag|Létrehozott felhasználói munkamenetek jelenlegi száma.|
|mashup_engine_memory_metric|M-motor memória|Bájt|Átlag|Adategyesítési motor folyamatok memóriahasználata|
|mashup_engine_qpu_metric|M-motor QPU|Darabszám|Átlag|Adategyesítési motor folyamatok QPU kihasználtsága|
|memory_metric|Memory (Memória)|Bájt|Átlag|A memória. S1 0-25 GB-ot, S2 0-50 GB-ot és 0 – 100 GB-ot S4|
|memory_thrashing_metric|Memória Akadozások|Százalék|Átlag|Átlagos memória thrashing.|
|CleanerCurrentPrice|Memória: Tisztító aktuális árlista|Darabszám|Átlag|Aktuális árlista memória $/ bájt/idő normalizálva 1000.|
|CleanerMemoryNonshrinkable|Memória: Tisztító memória nonshrinkable|Bájt|Átlag|Memória mérete, nem vonatkoznak a háttérben tisztító kiürítése mennyisége.|
|CleanerMemoryShrinkable|Memória: Zsugoríthatósági tisztító memória|Bájt|Átlag|Memória mérete, a háttérben tisztító kiürítése függvényében mennyisége.|
|MemoryLimitHard|Memória: Memóriakorlátot rögzített|Bájt|Átlag|Rögzített memóriakorlátot, konfigurációs fájlból.|
|MemoryLimitHigh|Memória: Memória korlátozása magas|Bájt|Átlag|Magas memóriakorlátot, konfigurációs fájlból.|
|MemoryLimitLow|Memória: Memória korlátja alacsony|Bájt|Átlag|Kevés a memória a határt, a konfigurációs fájlból.|
|MemoryLimitVertiPaq|Memória: Memória korlátja VertiPaq|Bájt|Átlag|A memória maximális mennyiségét, a konfigurációs fájlból.|
|MemoryUsage|Memória: Memória használata|Bájt|Átlag|A kiszolgálói folyamat, tisztító memória ár kiszámításakor felhasznált memória mennyisége Process\PrivateBytes plusz memórialeképezési-adatok mérete, a rendszer figyelmen kívül hagyja a memória, amely lett leképezve, vagy le van foglalva a memórián belüli analytics motor (VertiPaq) meghaladja a memóriakorlátot motor számláló értéke.|
|Kvóta|Memória: kvóta|Bájt|Átlag|Aktuális memóriakvótáját, bájtban. Memóriakvótáját memória grant vagy memória foglalás is nevezik.|
|QuotaBlocked|Memória: Kvóta letiltva|Darabszám|Átlag|Le vannak tiltva, amíg más memória kvóták felszabadítását kvóta kérelmek jelenlegi száma.|
|VertiPaqNonpaged|Memória: a nem lapozható VertiPaq|Bájt|Átlag|A memórián belüli motor általi használatra munkakészletében bájt memóriát zárolva van.|
|VertiPaqPaged|Memória: A VertiPaq lapozható|Bájt|Átlag|A memóriában levő használt lapozható memória bájt.|
|ProcessingPoolJobQueueLength|Feldolgozási készlet feladat várólistájának hossza|Darabszám|Átlag|A várólistában a feldolgozási szálkészletben lévő nem-I/O-feladatok száma.|
|RowsConvertedPerSec|Feldolgozási: Sorok konvertálni másodpercenkénti száma|Egység/s|Átlag|Sorok aránya alakítja a feldolgozás során.|
|RowsReadPerSec|Feldolgozási: Sor olvasása másodpercenkénti száma|Egység/s|Átlag|Sorok aránya az összes relációs adatbázisok olvasni.|
|RowsWrittenPerSec|Feldolgozási: Másodpercenként írt sorok|Egység/s|Átlag|Feldolgozás során írt sorok száma.|
|qpu_metric|QPU|Darabszám|Átlag|QPU. Tartomány 0 – 100 s1, 0-200 S2 és 0 – 400 S4|
|QueryPoolBusyThreads|Lekérdezés készlet foglalt szálak|Darabszám|Átlag|A lekérdezés szálkészlet foglalt szálak számát.|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenkénti száma|Egység/s|Átlag|Sikeres csatlakozás befejezésekhez aránya.|
|CommandPoolBusyThreads|Szálak: Parancs készlet foglalt szálak|Darabszám|Átlag|A parancs szálkészlet foglalt szálak számát.|
|CommandPoolIdleThreads|Szálak: Parancs készlet üresjárati szálak|Darabszám|Átlag|A parancs szálkészlet üresjárati szálak számát.|
|LongParsingBusyThreads|Szálak: Hosszú foglalt szálak elemzése|Darabszám|Átlag|A hosszú elemzési szálkészlet foglalt szálak számát.|
|LongParsingIdleThreads|Szálak: Hosszú üresjárati szálak elemzése|Darabszám|Átlag|A hosszú elemzési szálkészlet üresjárati szálak számát.|
|LongParsingJobQueueLength|Szálak: Hosszú elemzése feladat várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szálkészlet sorban lévő feladatok száma.|
|ProcessingPoolIOJobQueueLength|Szálak: Feldolgozása készlet i/o-feldolgozás várólistájának hossza|Darabszám|Átlag|A várólistában a feldolgozási szálkészletben lévő i/o-feladatok száma.|
|ProcessingPoolBusyIOJobThreads|Szálak: Készlet foglalt i/o-feladat szálak feldolgozása|Darabszám|Átlag|I/o-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|
|ProcessingPoolBusyNonIOThreads|Szálak: Készlet foglalt nem-I/O-szálak feldolgozása|Darabszám|Átlag|Nem-I/O-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|
|ProcessingPoolIdleIOJobThreads|Szálak: Készlet üresjárati i/o-feladat szálak feldolgozása|Darabszám|Átlag|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|
|ProcessingPoolIdleNonIOThreads|Szálak: Készlet üresjárati nem-I/O-szálak feldolgozása|Darabszám|Átlag|Nem-I/O-feladatok hozzárendelve a feldolgozási szálkészletben üresjárati szálak számát.|
|QueryPoolIdleThreads|Szálak: Lekérdezés készlet üresjárati szálak|Darabszám|Átlag|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|
|QueryPoolJobQueueLength|Szálak: Lekérdezés készlet feladat várólistájának hossza|Darabszám|Átlag|A lekérdezés szálkészlet sorban lévő feladatok száma.|
|ShortParsingBusyThreads|Szálak: Rövid foglalt szálak elemzése|Darabszám|Átlag|A rövid elemzési szálkészlet foglalt szálak számát.|
|ShortParsingIdleThreads|Szálak: Rövid üresjárati szálak elemzése|Darabszám|Átlag|A rövid elemzési szálkészlet üresjárati szálak számát.|
|ShortParsingJobQueueLength|Szálak: Rövid elemzése feladat várólistájának hossza|Darabszám|Átlag|A rövid elemzési szálkészlet sorban lévő feladatok száma.|
|TotalConnectionFailures|Teljes kapcsolódási hibák|Darabszám|Átlag|Összes sikertelen csatlakozási kísérletek.|
|TotalConnectionRequests|Teljes csatlakozási kérések|Darabszám|Átlag|Teljes csatlakozási kérések. |

## <a name="next-steps"></a>További lépések
[Figyelés a Microsoft Azure-ban](../monitoring-and-diagnostics/monitoring-overview.md)   
[A Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[A REST API-t Azure figyelő metrikák](https://msdn.microsoft.com/library/azure/dn931930.aspx)