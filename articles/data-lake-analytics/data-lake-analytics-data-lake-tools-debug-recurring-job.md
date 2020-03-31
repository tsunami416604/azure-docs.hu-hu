---
title: Ismétlődő feladatok hibakeresése az Azure Data Lake Analytics szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure Data Lake Tools for Visual Studio-t egy rendellenes ismétlődő feladat hibakereséséhez.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629757"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Rendellenes ismétlődő feladat hibaelhárítása

Ez a cikk bemutatja, hogyan használhatja az [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) az ismétlődő feladatokkal kapcsolatos problémák elhárításához. További információ a folyamat- és ismétlődő feladatokról az Azure Data Lake és az [Azure HDInsight blogon.](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)

Az ismétlődő feladatok általában ugyanazt a lekérdezési logikát és hasonló bemeneti adatokat rendelkeznek. Tegyük fel például, hogy minden hétfő reggel 8-kor ismétlődő állás fut. a múlt heti heti aktív felhasználó számlálásához. Ezeknek a feladatoknak a parancsfájljai egy parancsfájlsablont osztanak meg, amely a lekérdezési logikát tartalmazza. Ezek a feladatok bemenetek a használati adatok a múlt héten. Ugyanazt a lekérdezési logikát és hasonló bemenetet általában azt jelenti, hogy ezek a feladatok teljesítménye hasonló és stabil. Ha az ismétlődő feladatok egyike hirtelen rendellenesen, sikertelenül vagy sokat lelassul, a következőket teheti:

- Tekintse meg az ismétlődő feladat előző futtatásaistatisztikai jelentéseit, hogy lássa, mi történt.
- Hasonlítsa össze a rendellenes munkát egy normális, hogy kitaláljuk, mi változott.

**Kapcsolódó feladatnézet** az Azure Data Lake Tools for Visual Studio segítségével felgyorsíthatja a hibaelhárítási folyamat mindkét esetben.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>1. lépés: Ismétlődő feladatok keresése és a Kapcsolódó feladatnézet megnyitása

Ha a Kapcsolódó feladatnézet et szeretné használni egy ismétlődő feladatprobléma elhárításához, először meg kell találnia az ismétlődő feladatot a Visual Studióban, majd meg kell nyitnia a Kapcsolódó feladatnézetet.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>1. eset: Rendelkezik az ismétlődő feladat URL-címére

Az **Eszközök:** > **Data Lake** > **feladatnézet segítségével**beillesztheti a feladat URL-címét a Feladatnézet visual studio-ban való megnyitásához. Válassza **a Kapcsolódó feladatok megtekintése** lehetőséget a Kapcsolódó feladatnézet megnyitásához.

![Kapcsolódó feladatok megtekintése hivatkozás a Data Lake Analytics Tools szolgáltatásban](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>2. eset: Rendelkezik az ismétlődő feladat folyamatával, de az URL-címmel nem.

A Visual Studióban megnyithatja a Folyamatböngészőt a Kiszolgálókezelőn keresztül, > az Azure Data Lake Analytics-fiókját > **folyamatokhoz.** (Ha nem találja ezt a csomópontot a Kiszolgálókezelőben, [töltse le a legújabb beépülő modult.)](https://aka.ms/adltoolsvs) 

![A folyamatok csomópontjának kiválasztása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

A Pipeline Browserben a Data Lake Analytics-fiók összes folyamata a bal oldalon található. A folyamatok kibontásával megkeresheti az összes ismétlődő feladatot, majd kiválaszthatja a problémákkal kapcsolatos at. A kapcsolódó feladatnézet a jobb oldalon nyílik meg.

![Folyamat kiválasztása és kapcsolódó feladatnézet megnyitása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>2. lépés: Statisztikai jelentés elemzése

Az összefoglaló és a statisztikai jelentés a Kapcsolódó feladatnézet tetején jelenik meg. Itt megtalálhatja a probléma lehetséges kiváltó okát. 

1.  A jelentésben az X tengely a feladat beküldési idejét jeleníti meg. Használd, hogy megtaláld a rendellenes munkát.
2.  Az alábbi ábrán látható folyamat segítségével ellenőrizheti a statisztikákat, és betekintést nyerhet a problémába és a lehetséges megoldásokba.

![Folyamatábra a statisztikák ellenőrzéséhez](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>3. lépés: Hasonlítsa össze a rendellenes munkát egy normál feladat

Az összes beküldött ismétlődő feladatot a Kapcsolódó feladatnézet alján található feladatlistában találja. Ha további elemzéseket és lehetséges megoldásokat szeretne megtudni, kattintson a jobb gombbal a rendellenes feladatra. A Feladat különbsége nézetben összehasonlíthatja a rendellenes feladatot egy korábbi normál feladattal.

![A feladatok összehasonlítására szolgáló helyi menü](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Ügyeljen arra, hogy a nagy különbség a két munkahely. Ezek a különbségek valószínűleg okozza a teljesítmény problémákat. A további ellenőrzéshez kövesse az alábbi ábrán található lépéseket:

![Folyamatábra a feladatok közötti különbségek ellenőrzéséhez](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>További lépések

* [Adatdöntési problémák megoldása](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Hibakeresés, felhasználó által definiált C# kód a sikertelen U-SQL-feladatokhoz](data-lake-analytics-debug-u-sql-jobs.md)
