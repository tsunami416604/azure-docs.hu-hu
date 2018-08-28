---
title: Ismétlődő az Azure Data Lake Analytics-feladatok hibakereséséhez
description: Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio hibakeresése rendellenes ismétlődő feladat.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 33c3b91e7bf9fa64e3ba3f98a9396045753d0c2a
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045694"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Rendellenes ismétlődő feladat hibaelhárítása

Ez a cikk bemutatja, hogyan [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) ismétlődő feladatok kapcsolatos problémák megoldásához. További információ a folyamatok és az ismétlődő feladatok a [az Azure Data Lake és Azure HDInsight blog](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Ismétlődő feladatok ugyanazon lekérdezés logikája és a hasonló bemeneti adatokat általában megosztott. Képzeljünk el például, hogy rendelkezik-e ismétlődő feladat fut minden hétfő reggel 8 órakor a múlt héten heti aktív felhasználók száma. Ezeket a feladatokat a parancsfájlok megosztani egy parancsfájl-sablont, amely tartalmazza a lekérdezés logikáját. Ezek a feladatok bemenetei a használati adatok az elmúlt hét vonatkoznak. A lekérdezés ugyanazt a logikát, és a hasonló bemeneti megosztása általában azt jelenti, hogy ezek a feladatok teljesítményét, hasonló és stabil. Ha az ismétlődő feladatok egyik hirtelen végez rendellenesen, sikertelen, vagy sok lelassul, előfordulhat, hogy szeretné:

- Tekintse meg az előző fut, tekintse meg az ismétlődő feladat statisztika jelentéseiben.
- Hasonlítsa össze a rendellenes feladat egy normál, döntse el, mi változott.

**Kapcsolódó feladat nézet** az Azure Data Lake Tools for Visual Studio segítségével felgyorsíthatja a hibaelhárítási folyamat mindkét esetben a.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>1. lépés: Ismétlődő feladatok keresése, és nyissa meg a kapcsolódó feladat megtekintése

Ismétlődő feladat probléma hibaelhárításához kapcsolódó feladat nézet használatához meg kell először keresse meg az ismétlődő feladat a Visual Studióban, és nyissa meg a kapcsolódó feladat megtekintése.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>1. eset: Rendelkezik az ismétlődő feladat URL-címe

Keresztül **eszközök** > **Data Lake** > **Feladatnézetben**, beillesztheti a feladat nézet megnyitásához a Visual Studióban a feladat URL-CÍMÉT. Válassza ki **kapcsolódó feladatok megtekintése** kapcsolódó feladat nézet megnyitásához.

![A Data Lake Analytics Tools nézethivatkozás kapcsolódó feladatok](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>2. eset: A folyamat az az ismétlődő feladat, de nem az URL-cím van

A Visual Studio Server Explorer eszközével folyamat böngészőben megnyithatja > az Azure Data Lake Analytics-fiók > **folyamatok**. (Ha ez a csomópont nem találja a Server Explorerben [töltse le a legújabb beépülő modul](http://aka.ms/adltoolsvs).) 

![A folyamatok csomópont kiválasztása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

Folyamat böngészőben a bal oldalon felsorolt összes folyamatokat a Data Lake Analytics-fiók. Bontsa ki a folyamatok minden ismétlődő feladatok keresése, és válassza ki azt, amelynek a problémákat. Kapcsolódó feladat megjelenítése a jobb oldalon nyílik meg.

![Jelölje ki a folyamatot, és a kapcsolódó feladat nézet megnyitása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>2. lépés: A statisztika jelentés elemzése

Összegzése és a egy szinkronizálásistatisztika-jelentésről kapcsolódó feladat nézet tetején jelennek meg. Itt megtalálhatja a probléma lehetséges kiváltó okát. 

1.  A jelentést az x tengelyen jeleníti meg a feladat küldésének ideje. A rendellenes feladat kereséséhez használja azt.
2.  Az alábbi ábrán a folyamat használatával ellenőrizze a statisztikákat, és nyerjen a problémát, és a lehetséges megoldások tudnivalók.

![Folyamat diagramja statisztikai adatainak ellenőrzése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>3. lépés: Hasonlítsa össze a rendellenes feladat egy normál feladathoz

Megtalálhatja az összes elküldött, ismétlődő feladatokat a feladatlistában kapcsolódó feladat nézet alján keresztül. További elemzések és a lehetséges megoldások kereséséhez kattintson a jobb gombbal a rendellenes feladat. A feladat Diff nézet segítségével összehasonlíthatja a rendellenes normál egy korábbi feladatban.

![Místní nabídka feladatok összehasonlítása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Ezek a feladatok két big Data típusú különbségeit figyelmet fordítania. Ezek a különbségek valószínűleg a teljesítménybeli problémákat okoz. További ellenőrzéséhez használja az alábbi ábrán a lépéseket:

![Feladatok közötti különbség-ellenőrzési folyamat ábrája](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>További lépések

* [Adateltérés problémák megoldása](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felhasználó által definiált C#-kód hibaelhárítása U-SQL-feladatok sikertelen](data-lake-analytics-debug-u-sql-jobs.md)
