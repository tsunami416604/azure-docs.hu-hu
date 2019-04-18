---
title: Azure Analysis Services-kiszolgáló metrikáinak monitorozása |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti az Analysis Services kiszolgálói metrikák az Azure-portálon.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893180"
---
# <a name="monitor-server-metrics"></a>A kiszolgáló metrikáinak monitorozása

Analysis Services mérőszámait megfigyelheti a teljesítmény és a kiszolgálók állapotát. Például figyelésére, a memória és CPU-használat, a kapcsolatok és lekérdezési erőforrás-használat. Analysis Services figyelési keretrendszert használja, mint a legtöbb más Azure-szolgáltatásokat. További tudnivalókért lásd: [Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

További részletes diagnosztikai végrehajtásához követheti nyomon, és egy erőforráscsoport vagy előfizetés több szolgáltatási erőforrások azonosíthatja a trendeket, használja [Azure Monitor](https://azure.microsoft.com/services/monitor/). Az Azure Monitor (szolgáltatás) egy számlázható szolgáltatás létrejöttét eredményezheti.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Az Analysis Services-kiszolgáló metrikáinak monitorozása

1. Az Azure Portalon, válassza ki a **metrikák**.

    ![Monitorozás az Azure Portalon](./media/analysis-services-monitor/aas-monitor-portal.png)

2. A **rendelkezésre álló metrikák**, válassza ki a mérőszámok közé tartozik a diagramon. 

    ![A figyelő diagram](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Kiszolgáló-metrikák

Ez a táblázat segítségével meghatározhatja, pontosan mely mérőszámokat a legjobbak a figyelési forgatókönyv. Csak ugyanazon mértékegység mérőszámait ugyanezen a diagramon megjeleníthető.

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|
|---|---|---|---|---|
|CommandPoolJobQueueLength|A parancs feladat-várólistájának hossza|Darabszám|Átlag|Az a parancs szálkészlet üzenetsorában található feladatok száma.|
|CurrentConnections|Kapcsolat: Jelenlegi kapcsolatok száma|Darabszám|Átlag|A létrehozott ügyfélkapcsolatok aktuális száma.|
|CurrentUserSessions|Jelenlegi felhasználói munkamenetek|Darabszám|Átlag|A létrehozott felhasználói munkamenetek aktuális száma.|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Átlag|Az adategyesítési motor folyamatainak memóriafelhasználása|
|mashup_engine_qpu_metric|M motor qpu-ja|Darabszám|Átlag|Az adategyesítési motor folyamatainak QPU használatáról|
|memory_metric|Memory (Memória)|Bájt|Átlag|A memória. Tartomány 0 – 25 GB – S1, 0-50 GB – S2 és 0 – 100 GB – S4|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Átlagos memóriaakadozás.|
|CleanerCurrentPrice|Memória: Tisztító – aktuális ár|Darabszám|Átlag|Aktuális ára $/ bájt/idő, a memória, 1000-re normalizálva.|
|CleanerMemoryNonshrinkable|Memória: Tisztább memória – nem zsugorítható|Bájt|Átlag|Memória (bájt) nem vonatkoznak a háttérben futó tisztító kiürítése mennyisége.|
|CleanerMemoryShrinkable|Memória: Tisztító – zsugorítható memória|Bájt|Átlag|Memória (bájt) vonatkoznak a háttérben futó tisztító kiürítése mennyisége.|
|MemoryLimitHard|Memória: Szigorú Memóriakorlát|Bájt|Átlag|Szigorú Memóriakorlát a konfigurációs fájlból.|
|MemoryLimitHigh|Memória: Felső Memóriakorlát|Bájt|Átlag|Felső Memóriakorlát a konfigurációs fájlból.|
|MemoryLimitLow|Memória: Alsó Memóriakorlát|Bájt|Átlag|Alsó Memóriakorlát a konfigurációs fájlból.|
|MemoryLimitVertiPaq|Memória: VertiPaq-Memóriakorlát|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|
|MemoryUsage|Memória: Memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat tisztító memória ár kiszámításakor memóriahasználat. Megegyezik a számláló Process\PrivateBytes plusz a memória által leképezett adatok figyelmen kívül hagyva, amely a leképezett vagy lefoglalt biztosította a motor memória felső korlátja a memóriabeli elemzési motor (VertiPaq) által méretét.|
|Kvóta|Memória: Kvóta|Bájt|Átlag|Aktuális memóriakvóta bájtban. A memóriakvóta memória grant vagy a memória foglalás is nevezik.|
|QuotaBlocked|Memória: Blokkolt kvóta|Darabszám|Átlag|Aktuális száma, amelyek le vannak tiltva, amíg a további Memóriakvóták felszabadítását.|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bájt|Átlag|Bájt memóriát a memóriabeli motor általi használatra munkakészletében lévő zárolva van.|
|VertiPaqPaged|Memória: VertiPaq Paged|Bájt|Átlag|Lapozható memória, a memóriában lévő adatok bájt.|
|ProcessingPoolJobQueueLength|Feldolgozási készlet feladat-várólistájának hossza|Darabszám|Átlag|Az a feldolgozási szálkészlet üzenetsorában lévő nem I/o feladatok száma.|
|RowsConvertedPerSec|Feldolgozás: Másodpercenként konvertált sorok|Egység/s|Átlag|Sorok konvertálásának sebessége a feldolgozás során.|
|RowsReadPerSec|Feldolgozás: Másodpercenként beolvasott sorok száma|Egység/s|Átlag|Sorok beolvasásának sebessége az összes relációs adatbázisból.|
|RowsWrittenPerSec|Feldolgozás: Másodpercenként írt sorok|Egység/s|Átlag|Sorok írásának sebessége a feldolgozás során.|
|qpu_metric|QPU|Darabszám|Átlag|QPU. Tartomány 0 és 100 s1, 0 – 200 – S2 és 0 – 400 – S4|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szálkészletben lévő foglalt szálak száma.|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|Egység/s|Átlag|A sikeres kapcsolat befejezésekből sebessége.|
|CommandPoolBusyThreads|Szálak: A parancs parancskészlet – foglalt szálak|Darabszám|Átlag|A parancs szálkészletben lévő foglalt szálak száma.|
|CommandPoolIdleThreads|Szálak: A parancs készlet – üresjárati szálak|Darabszám|Átlag|A parancsszálkészletben az üresjárati szálak száma.|
|LongParsingBusyThreads|Szálak: Tartós folyamatok elemzési foglalt szálak|Darabszám|Átlag|A tartós folyamatok elemzési szálkészletben lévő foglalt szálak száma.|
|LongParsingIdleThreads|Szálak: Tartós folyamatok elemzési üresjárati szálak|Darabszám|Átlag|A tartós folyamatok elemzési szálkészletében az üresjárati szálak száma.|
|LongParsingJobQueueLength|Szálak: Tartós folyamatok elemzési feladat üzenetsorának hossza|Darabszám|Átlag|A tartós folyamatok elemzési szálkészlet üzenetsorában található feladatok száma.|
|ProcessingPoolIOJobQueueLength|Szálak: Feldolgozási készlet – I/O feladat üzenetsorának hossza|Darabszám|Átlag|Az a feldolgozási szálkészlet üzenetsorában található I/O feladatok száma.|
|ProcessingPoolBusyIOJobThreads|Szálak: Készlet foglalt i/o-feladat szálak feldolgozása|Darabszám|Átlag|A feldolgozási szálkészletben I/O feladatokat futtató szálak száma.|
|ProcessingPoolBusyNonIOThreads|Szálak: Feldolgozási készlet a nem I/o feladat foglalt szálai|Darabszám|Átlag|A feldolgozási szálkészletben nem I/o feladatokat futtató szálak száma.|
|ProcessingPoolIdleIOJobThreads|Szálak: Feldolgozási készlet – üresjárati i/o feladat szálak|Darabszám|Átlag|A feldolgozási szálkészletben I/O feladatok üresjárati szálak száma.|
|ProcessingPoolIdleNonIOThreads|Szálak: Feldolgozási készlet a nem I/o feladat üresjárati szálai|Darabszám|Átlag|A feldolgozási szálkészletben nem I/o feladatok számára kijelölt üresjárati szálak száma.|
|QueryPoolIdleThreads|Szálak: Lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|A feldolgozási szálkészletben I/O feladatok üresjárati szálak száma.|
|QueryPoolJobQueueLength|Szálak: Lekérdezési készlet feladat-várólistájának hossza|Darabszám|Átlag|Az a lekérdezési szálkészlet üzenetsorában található feladatok száma.|
|ShortParsingBusyThreads|Szálak: Rövid elemzés a foglalt szálak|Darabszám|Átlag|A rövid elemzési szálkészletben lévő foglalt szálak száma.|
|ShortParsingIdleThreads|Szálak: Rövid elemzési üresjárati szálak|Darabszám|Átlag|A rövid elemzési szálkészletben az üresjárati szálak száma.|
|ShortParsingJobQueueLength|Szálak: Rövid elemzési feladat üzenetsorának hossza|Darabszám|Átlag|A a rövid elemzési szálkészlet üzenetsorában található feladatok száma.|
|TotalConnectionFailures|Csatlakozási hibák teljes száma|Darabszám|Átlag|A sikertelen csatlakozási kísérletek összesen.|
|TotalConnectionRequests|Csatlakozási kérelmek teljes száma|Darabszám|Átlag|Csatlakozási kérelmek teljes száma. |

## <a name="next-steps"></a>További lépések
[Figyelés a Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[A Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Mérőszámok az Azure Monitor REST API-val](/rest/api/monitor/metrics)
