---
title: Az Azure Analysis Services kiszolgálóinak mutatóinak figyelése | Microsoft dokumentumok
description: Ismerje meg, hogyan használja az Analysis Services az Azure Metrics Explorert, amely egy ingyenes eszköz a portálon, amely segít a kiszolgálók teljesítményének és állapotának figyeléséhez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252804"
---
# <a name="monitor-server-metrics"></a>A kiszolgáló metrikáinak monitorozása

Az Analysis Services metrikákat biztosít az Azure Metrics Explorerben, amely egy ingyenes eszköz a portálon, amely segít a kiszolgálók teljesítményének és állapotának figyeléséhez. Például figyelheti a memória- és processzorhasználatot, az ügyfélkapcsolatok számát és az erőforrás-felhasználás lekérdezését. Az Analysis Services ugyanazt a figyelési keretrendszert használja, mint a legtöbb más Azure-szolgáltatás. További információ: [Első lépések az Azure Metrics Explorer használatával című témakörben.](../azure-monitor/platform/metrics-getting-started.md)

Ha részletesebb diagnosztikát szeretne végrehajtani, nyomon szeretné követni a teljesítményt, és azonosítani szeretné a trendeket egy erőforráscsoport ban vagy előfizetésben több szolgáltatáserőforrás között, használja az [Azure Monitort.](../azure-monitor/overview.md) Az Azure Monitor (szolgáltatás) egy számlázható szolgáltatást eredményezhet.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Az Analysis Services-kiszolgáló mutatóinak figyelése

1. Az Azure Portalon válassza **a Metrikák**lehetőséget.

    ![Monitorozás az Azure Portalon](./media/analysis-services-monitor/aas-monitor-portal.png)

2. A **Metrika**alkalmazásban válassza ki a diagramba felvenni kívánt mutatókat. 

    ![Diagram monitora](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Kiszolgálói mutatók

Ebben a táblázatban határozhatja meg, hogy mely metrikák a legjobbak a figyelési forgatókönyvhöz. Ugyanazon az egységmérők jeleníthetők meg ugyanazon a diagramon.

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Parancskészlet feladatvárólistájának hossza|Darabszám|Átlag|A parancsszálkészlet várólistájában lévő feladatok száma.|
|Jelenlegi kapcsolatok|Kapcsolat: Jelenlegi kapcsolatok|Darabszám|Átlag|A létrehozott ügyfélkapcsolatok aktuális száma.|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrehozott felhasználói munkamenetek aktuális száma.|
|mashup_engine_memory_metric|M motor memória|Bájt|Átlag|Memóriahasználat mashup motorfolyamatok által|
|mashup_engine_qpu_metric|M Motor QPU|Darabszám|Átlag|QPU-használat mashup motorfolyamatok által|
|memory_metric|Memory (Memória)|Bájt|Átlag|Memory. 0–25 GB-os tartomány S1 esetén, 0–50 GB S2 esetén és 0–100 GB S4 esetén|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Átlagos memória verés.|
|CleanerCurrentPrice|Memória: Tisztább aktuális ár|Darabszám|Átlag|A memória aktuális ára , $/byte/time, normalizálva 1000-re.|
|CleanerMemoryNem zsugorítható|Memória: A tisztább memória nem zsugorodik|Bájt|Átlag|A memória mennyisége bájtban, nem függ a háttértisztító tisztítását.|
|CleanerMemoryZsugorodikható|Memória: Tisztább memória zsugorodik|Bájt|Átlag|A memória mennyisége bájtban, a háttértisztító általi tisztításfüggvényében.|
|MemoryLimitHard memória|Memória: Memória korlát kemény|Bájt|Átlag|Szigorú memóriakorlát a konfigurációs fájlból.|
|MemoryLimitHigh|Memória: Memória limit magas|Bájt|Átlag|Magas memóriakorlát, konfigurációs fájlból.|
|MemoryLimitLow memória|Memória: Kevés memóriakorlát|Bájt|Átlag|Kevés a memória, a konfigurációs fájlból.|
|MemoryLimitVertiPaq|Memória: Memóriakorlát VertiPaq|Bájt|Átlag|Memóriakorlát, konfigurációs fájlból.|
|Memóriahasználat|Memória: Memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználata a tisztább memória árának kiszámításához használt módon. Egyenlő a counter Process\PrivateBytes és a memórialeképezésű adatok méretével, figyelmen kívül hagyva minden olyan memóriát, amelyet a memórián belüli elemzőmotor (VertiPaq) a memóriakorlátot meghaladóan leképezett vagy lefoglalt.|
|private_bytes_metric|Privát bájtok |Bájt|Átlag|Az Analysis Services motorfolyamata és a Mashup-tároló folyamatai által lefoglalt memória teljes mennyisége, a más folyamatokkal megosztott memóriát nem beleértve.|
|virtual_bytes_metric|Felhasznált virtuális memória jelenlegi mérete (bájt) |Bájt|Átlag|Az Analysis Services motorfolyamata és a Mashup-tárolófolyamatok által használt virtuális címterület aktuális mérete.|
|mashup_engine_private_bytes_metric|M motor privát bájt |Bájt|Átlag|A memória mashup tároló folyamatainak teljes összege, nem beleértve a más folyamatokkal megosztott memóriát.|
|mashup_engine_virtual_bytes_metric|M motor virtuális bájtjai |Bájt|Átlag|A virtuális címtér Mashup tároló folyamatok által használt aktuális mérete.|
|Kvóta|Memória: Kvóta|Bájt|Átlag|Aktuális memóriakvóta bájtban. A memóriakvótát memóriatámogatásnak vagy memóriafoglalásnak is nevezik.|
|Kvóta blokkolva|Memória: Kvóta letiltva|Darabszám|Átlag|A többi memóriakvótáfelszabadításig letiltott kvótakérelmek jelenlegi száma.|
|VertiPaqNonpaged között|Memória: VertiPaq Nonpaged|Bájt|Átlag|A munkakészletben zárolt memóriabájtokat a memóriában lévő motor számára.|
|VertiPaqPaged|Memória: VertiPaq lapozgatott|Bájt|Átlag|A memórián belüli adatokhoz használt lapozható memória bájtjai.|
|ProcessingPoolJobQueueLength|Készletfeladat-várólista hosszának feldolgozása|Darabszám|Átlag|A feldolgozószál-készlet várólistájában lévő nem I/O-feladatok száma.|
|RowsconvertedperSec|Feldolgozás: Másodpercenként konvertált sorok|CountPerSzekszekundum|Átlag|A feldolgozás során konvertált sorok aránya.|
|RowsreadperSec|Feldolgozás: Másodpercenként olvasott sorok|CountPerSzekszekundum|Átlag|Az összes relációs adatbázisból beolvasott sorok aránya.|
|RowsWrittenPerSec|Feldolgozás: Másodpercenként írt sorok|CountPerSzekszekundum|Átlag|A feldolgozás során írt sorok aránya.|
|qpu_metric|QPU|Darabszám|Átlag|QPU. Tartomány 0-100 S1, 0-200 S2 és 0-400 S4 esetén|
|QueryPoolBusyThreads|Lekérdezéskészlet foglalt szálai|Darabszám|Átlag|Foglalt szálak száma a lekérdezési szálkészletben.|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|CountPerSzekszekundum|Átlag|A sikeres kapcsolatbefejezések aránya.|
|CommandPoolBusyThreads|Szálak: Parancskészlet foglalt szálai|Darabszám|Átlag|Foglalt szálak száma a parancsszálkészletben.|
|CommandPoolIdleThreads|Szálak: Parancskészlet tétlen szálai|Darabszám|Átlag|Az alapjárati szálak száma a parancsszálkészletben.|
|LongParsingBusyThreads|Szálak: Hosszú elemzés foglalt szálak|Darabszám|Átlag|Foglalt szálak száma a hosszú elemzési szálkészletben.|
|LongParsingIdleThreads|Szálak: Hosszú elemzés tétlen szálak|Darabszám|Átlag|A hosszú elemzési szálkészletben lévő alapjárati szálak száma.|
|LongParsingJobQueueLength|Szálak: Hosszú elemzési feladat várólista hossza|Darabszám|Átlag|A hosszú elemzési szálkészlet várólistájában lévő feladatok száma.|
|ProcessingPoolIOJobQueueLength|Szálak: A készlet I/O-feladatvárólistájának hosszának feldolgozása|Darabszám|Átlag|A feldolgozószál-készlet várólistájában lévő I/O-feladatok száma.|
|ProcessingPoolBusyIOJobThreads|Szálak: A készlet foglalt I/O-feladatszálainak feldolgozása|Darabszám|Átlag|I/O-feladatokat futtató szálak száma a feldolgozási szálkészletben.|
|ProcessingPoolBusyNonIOThreads|Szálak: A készlet foglalt, nem I/O-szálai|Darabszám|Átlag|Nem I/O-feladatokat futtató szálak száma a feldolgozószál-készletben.|
|ProcessingPoolIdleIOJobThreads|Szálak: A készlet tétlen I/O-feladatszálainak feldolgozása|Darabszám|Átlag|A feldolgozási szálkészletben lévő I/O-feladatok tétlen szálainak száma.|
|ProcessingPoolIdleNonIOThreads|Szálak: A készlet alapjárati nem I/O-szálai|Darabszám|Átlag|A nem I/O-feladatoknak szánt feldolgozószál-készletben lévő tétlen szálak száma.|
|QueryPoolIdleThreads|Szálak: Lekérdezéskészlet tétlen szálai|Darabszám|Átlag|A feldolgozási szálkészletben lévő I/O-feladatok tétlen szálainak száma.|
|QueryPoolJobQueueLength|Szálak: Lekérdezéskészlet-feladat várólista hossza|Darabszám|Átlag|A lekérdezési szálkészlet várólistájában lévő feladatok száma.|
|ShortParsingBusyThreads|Szálak: Rövid elemzés foglalt szálak|Darabszám|Átlag|Foglalt szálak száma a rövid elemzési szálkészletben.|
|ShortParsingIdleThreads|Szálak: Rövid elemzési tétlen szálak|Darabszám|Átlag|A rövid elemzési szálkészletben lévő alapjárati szálak száma.|
|ShortParsingJobQueueLength|Szálak: Rövid elemzési feladat várólista hossza|Darabszám|Átlag|A rövid elemzési szálkészlet várólistájában lévő feladatok száma.|
|Összes kapcsolati hiba|Csatlakozási hibák összesen|Darabszám|Átlag|Összes sikertelen csatlakozási kísérlet.|
|Összes kapcsolatkérése|Csatlakozási kérelmek összesen|Darabszám|Átlag|Összes csatlakozási kérelem. |

## <a name="next-steps"></a>További lépések
[Az Azure Monitor áttekintése](../azure-monitor/overview.md)      
[Az Azure Metrics Explorer – első lépések](../azure-monitor/platform/metrics-getting-started.md)      
[Metrikák az Azure Monitor REST API-ban](/rest/api/monitor/metrics)
