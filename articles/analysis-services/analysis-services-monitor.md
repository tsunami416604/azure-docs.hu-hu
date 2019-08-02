---
title: Azure Analysis Services kiszolgáló metrikáinak monitorozása | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Analysis Services kiszolgáló metrikáit Azure Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5431dd74629b9ed76a6a072d8ada286ce71a7633
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596091"
---
# <a name="monitor-server-metrics"></a>A kiszolgáló metrikáinak monitorozása

A Analysis Services metrikákat biztosít az Azure Metrikaböngészőban, amely egy ingyenes eszköz a portálon, amely segít a kiszolgálók teljesítményének és állapotának figyelésében. Figyelje például a memória és a processzor kihasználtságát, az ügyfélkapcsolatok számát és a lekérdezési erőforrások felhasználását. Analysis Services ugyanazt a figyelési keretrendszert használja, mint a legtöbb más Azure-szolgáltatás. További információ: [Az Azure Metrikaböngésző első lépései](../azure-monitor/platform/metrics-getting-started.md).

A részletesebb diagnosztika végrehajtásához, a teljesítmény nyomon követéséhez és az erőforráscsoport vagy előfizetés több szolgáltatási erőforrása közötti trendek azonosításához használja a [Azure monitor](../azure-monitor/overview.md). A Azure Monitor (szolgáltatás) számlázható szolgáltatást eredményezhet.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Analysis Services-kiszolgáló metrikáinak figyelése

1. A Azure Portal területen válasszaa metrikák lehetőséget.

    ![Monitorozás az Azure Portalon](./media/analysis-services-monitor/aas-monitor-portal.png)

2. A **rendelkezésre álló metrikák**területen válassza ki a diagramba felvenni kívánt metrikákat. 

    ![Diagram figyelése](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Kiszolgálói metrikák

A táblázat segítségével meghatározhatja, hogy mely mérőszámok a legmegfelelőbbek a figyelési forgatókönyvekhez. Ugyanazon a diagramon csak az azonos egység mérőszámai jeleníthetők meg.

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Parancssori feladatok várólistájának hossza|Count|Average|A parancsfájl-készlet várólistájában lévő feladatok száma.|
|Összege|Kapcsolat: Aktuális kapcsolatok|Count|Average|A létesített ügyfélkapcsolatok aktuális száma.|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Count|Average|A létrejött felhasználói munkamenetek aktuális száma.|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Average|Memóriahasználat az adategyesítési motor folyamatai szerint|
|mashup_engine_qpu_metric|M motor QPU|Count|Average|QPU-használat az adategyesítési motor folyamatai szerint|
|memory_metric|Memory (Memória)|Bájt|Average|Memória. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|
|memory_thrashing_metric|Memória-Kiverés|Percent|Average|Memória átlagos kiverése.|
|CleanerCurrentPrice|Memória: Tisztító aktuális díja|Count|Average|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|
|CleanerMemoryNonshrinkable|Memória: Nem zsugorodó tisztító memória|Bájt|Average|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|
|CleanerMemoryShrinkable|Memória: Tisztító memória zsugorodó|Bájt|Average|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|
|MemoryLimitHard|Memória: Memória korlátozása – kemény|Bájt|Average|Rögzített memória korlátja a konfigurációs fájlból.|
|MemoryLimitHigh|Memória: Memória korlátja magas|Bájt|Average|Magas memória korlátja a konfigurációs fájlból.|
|MemoryLimitLow|Memória: Kevés a memória korlátja|Bájt|Average|Kevés a memória korlátja a konfigurációs fájlból.|
|MemoryLimitVertiPaq|Memória: Memória korlátja VertiPaq|Bájt|Average|Memóriabeli korlát a konfigurációs fájlból.|
|MemoryUsage|Memória: Memóriahasználat|Bájt|Average|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memóriához rendelt adatmennyiséggel, figyelmen kívül hagyva a memóriát, amelyet a memória-elemzési motor (VertiPaq) leképezett vagy lefoglalt a memória korlátja felett.|
|Kvóta|Memória: Kvóta|Bájt|Average|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|
|QuotaBlocked|Memória: Kvóta letiltva|Count|Average|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|
|VertiPaqNonpaged|Memória: VertiPaq nem lapozható|Bájt|Average|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|
|VertiPaqPaged|Memória: VertiPaq lapozható|Bájt|Average|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|
|ProcessingPoolJobQueueLength|Feldolgozási készlet nyomtatási várólistájának hossza|Count|Average|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|
|RowsConvertedPerSec|Feldolgozás: Másodpercenként konvertált sorok száma|Egység/s|Average|A sorok konvertálásának sebessége a feldolgozás során.|
|RowsReadPerSec|Feldolgozás: Olvasott sorok száma másodpercenként|Egység/s|Average|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|
|RowsWrittenPerSec|Feldolgozás: Másodpercenként írt sorok száma|Egység/s|Average|A sorok írásának sebessége a feldolgozás során.|
|qpu_metric|QPU|Count|Average|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Count|Average|A lekérdezési szál készletében lévő foglalt szálak száma.|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|Egység/s|Average|A sikeres kapcsolatok gyakorisága.|
|CommandPoolBusyThreads|Szálak A parancssori készlet foglalt szálai|Count|Average|A parancsfájl-készletben lévő foglalt szálak száma.|
|CommandPoolIdleThreads|Szálak Parancs-készlet üresjárati szálai|Count|Average|Az üresjárati szálak száma a parancs szálának készletében.|
|LongParsingBusyThreads|Szálak A foglalt szálak hosszú elemzése|Count|Average|A foglalt szálak száma a hosszú elemzési szál készletében.|
|LongParsingIdleThreads|Szálak Hosszú távú elemzés – üresjárati szálak|Count|Average|Az üresjárati szálak száma a hosszú elemzési szál készletében.|
|LongParsingJobQueueLength|Szálak Hosszú elemzési feladatok várólistájának hossza|Count|Average|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|
|ProcessingPoolIOJobQueueLength|Szálak Feldolgozási készlet I/O-feladatok várólistájának hossza|Count|Average|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|
|ProcessingPoolBusyIOJobThreads|Szálak Feldolgozási készlet foglalt I/O-feladatok szálai|Count|Average|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|
|ProcessingPoolBusyNonIOThreads|Szálak Feldolgozási készlet elfoglalatlan nem I/O-szálai|Count|Average|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|
|ProcessingPoolIdleIOJobThreads|Szálak Feldolgozási készlet – üresjárati I/O-feladatok szálai|Count|Average|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|
|ProcessingPoolIdleNonIOThreads|Szálak Feldolgozási készlet üresjáratban lévő nem I/O-szálai|Count|Average|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|
|QueryPoolIdleThreads|Szálak Lekérdezési készlet üresjárati szálai|Count|Average|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|
|QueryPoolJobQueueLength|Szálak Lekérdezési készlet feladatok várólistájának hossza|Count|Average|A lekérdezési szál készletének várólistájában lévő feladatok száma.|
|ShortParsingBusyThreads|Szálak Foglalt szálak rövid elemzése|Count|Average|A foglalt szálak száma a rövid elemzési szál készletében.|
|ShortParsingIdleThreads|Szálak Rövid elemzési üresjárati szálak|Count|Average|Az üresjárati szálak száma a rövid elemzési szál készletében.|
|ShortParsingJobQueueLength|Szálak Rövid elemzési feladatok várólistájának hossza|Count|Average|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|
|TotalConnectionFailures|Összes sikertelen Kapcsolatfelvétel|Count|Average|A sikertelen csatlakozási kísérletek teljes száma.|
|TotalConnectionRequests|Kapcsolatkérelmek teljes száma|Count|Average|A kapcsolatkérelmek teljes száma. |

## <a name="next-steps"></a>További lépések
[Azure Monitor áttekintése](../azure-monitor/overview.md)      
[Első lépések az Azure Metrikaböngésző](../azure-monitor/platform/metrics-getting-started.md)      
[Metrikák a Azure Monitor REST API](/rest/api/monitor/metrics)
