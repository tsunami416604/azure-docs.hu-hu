---
title: Bevezetés az Azure Stream Analytics Windowing functions használatába
description: Ez a cikk az Azure Stream Analytics-feladatokban használt négy ablakkezelő függvényt (kihúzást, átugrást, lecsúszást, munkamenetet) ismerteti.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: fd741a9401a3936ec02939562e8e85046e829d31
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075911"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Bevezetés az Stream Analytics Windowing functions használatába

A folyamatos átvitelt lehetővé tévő forgatókönyvek esetében az időbeli Windowsban tárolt adatokon végrehajtott műveletek egy gyakori minta. Stream Analytics natív módon támogatja az ablakkezelő függvényeket, így a fejlesztők a lehető legkevesebb erőfeszítéssel hozhatnak létre összetett adatfolyam-feldolgozási feladatokat.

Négy különböző időbeli időszak közül választhat: a [**kiesés**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), a [**hopping**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), a [**csúszó**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)és a [**munkamenet**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) -ablakok.  A Stream Analytics-feladatok lekérdezési szintaxisának [**Group By**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) záradékában található Window functions kifejezés használható. Az eseményeket a [ **Windows ()** függvénnyel](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)több Windows rendszeren is összesítheti.

Az összes [ablakos](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) művelet kimenete az ablak **végén** jelenik meg. Az ablak kimenete egyetlen esemény lesz a használt összesítő függvény alapján. A kimeneti esemény az ablak végének időbélyegzője lesz, és az összes ablak függvény rögzített hosszúságú. 

![Stream Analytics Window functions – fogalmak](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Ablak kiesése
A kieséses ablak függvények az adatfolyamok különböző időszegmensekre való szegmentálására szolgálnak, és a rajtuk végrehajtott függvényeket, például az alábbi példát használják. Az átfedésmentes ablak fő sajátossága, hogy ismétlődik, nincs átfedésben, és egy esemény csak egy átfedésmentes ablakhoz tartozhat.

![Stream Analytics ablak kiesése](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hopping-ablak
Az ugróablak típusú függvények egy adott időtartamot ugranak előre az időben. Tulajdonképpen olyan átfedésmentes ablakok, amelyek lehetnek átfedésben, az események így több ugróablak eredményhalmazához is tartozhatnak. Az ablak méretével megegyező ugrások méretének megadásához a beugró ablaknak meg kell egyeznie. 

![Stream Analytics hopping-ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Ablak csúsztatása

A Windows csúszó ablakai, a felhúzással vagy a beugró ablakokkal ellentétben, a kimeneti események csak az adott időszakra vonatkozó tartalommal kapcsolatos időpontokban jelennek meg. Más szóval, amikor egy esemény belép vagy kilép az ablakból. Minden ablaknak van legalább egy eseménye, például a Windows-hopping esetében, az események több csúszó ablakhoz is tartozhatnak.

![Stream Analytics csúszó ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Munkamenet-ablak
A munkamenet-ablak a hasonló időpontokban érkező eseményeket csoportosítja, és kiszűri azokat az időszakokat, ahol nincs adat. Három fő paraméterrel rendelkezik: időtúllépés, maximális időtartam és particionálókulcs (nem kötelező).

![Stream Analytics munkamenet ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

A munkamenet-ablak az első esemény bekövetkezésekor kezdődik. Ha egy másik esemény kerül be a megadott időkorláton belül az utolsó betöltött eseménytől, akkor az ablak kiterjeszthető az új eseményre. Ellenkező esetben, ha az időkorláton belül nem következnek be események, az ablak bezáródik az időtúllépésnél.

Ha az események a megadott időkorláton belül maradnak, a munkamenet ablaka továbbra is meghosszabbítja a korlátot, amíg el nem éri a maximális időtartamot. A maximális időtartam-ellenőrzési intervallumok a megadott maximális időtartammal megegyező méretre vannak beállítva. Ha például a maximális időtartam 10, akkor a ellenőrzi, hogy az ablak túllépi-e a maximális időtartamot t = 0, 10, 20, 30 stb.

A partíciós kulcs megadásakor az eseményeket a kulcs és a munkamenet ablak együttesen csoportosítja az egyes csoportokra egymástól függetlenül. Ez a particionálás olyan esetekben hasznos, amikor különböző felhasználókhoz vagy eszközökhöz eltérő munkamenet-ablakok szükségesek.

## <a name="snapshot-window"></a>Pillanatkép-ablak

Pillanatkép Windows-csoportok eseményei, amelyek ugyanazzal az időbélyegzővel rendelkeznek. A más ablakos típusoktól eltérően, amelyek egy adott ablak függvényt igényelnek (például [SessionWindow ()](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics), a pillanatkép-ablakokat a System. timestamp () a Group By záradékhoz való hozzáadásával is alkalmazhatja.

![Pillanatkép-ablak Stream Analytics](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>További lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

