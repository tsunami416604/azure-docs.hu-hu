---
title: "Egy rendellenes ismétlődő feladat hibaelhárítása |} Microsoft Docs"
description: "Útmutató: Azure Data Lake Tools for Visual Studio használatát olyan rendellenes ismétlődő feladat."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>Egy rendellenes ismétlődő feladat hibaelhárítása

Ez a dokumentum azt bevezet használata [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) ismétlődő feladat problémák megoldásához. További információ a feldolgozási sorban lévő és az ismétlődő feladatok [Itt](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).
Ismétlődő feladatok általában megosztják az logikák lekérdezés és hasonló bemeneti adatok. Például hogy minden reggel 8 hétfő reggel futó ismétlődő feladat múlt héten heti aktív felhasználók száma, a parancsfájlok ezeket a feladatokat a megosztások a lekérdezés logikát tartalmaz egy parancsfájl-sablon, és a bemenetek ezeket a feladatokat a rendszer a használati adatok az előző hét. Megosztása azonos lekérdezni a logikai és hasonló bemeneti általában azt jelenti, hogy ezek a feladatok teljesítményének hasonló és stabil, ha hirtelen az ismétlődő feladatok egyikét hajtja végre, rendellenes, sikertelen vagy lassú le sokkal, akkor előfordulhat, hogy:

1.  Tekintse meg az előzetes verziójú funkciók fut, az ismétlődő feladat megtekintéséhez, mi történt a statisztikákat a jelentésekre.
2.  Hasonlítsa össze a rendellenes feladat, hogy megtudja, mi normál egy megváltozott.

**Kapcsolódó feladatok** az Azure Data Lake Tools for Visual Studio segítségével mindkét esetben a hibaelhárítási folyamatban érdekében.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>1. lépés: Található ismétlődő feladat, és nyissa meg a kapcsolódó feladat megtekintése

Kapcsolódó feladat használandó nézet ismétlődő feladat a probléma elhárításával kapcsolatos tudnivalókat kell először keresse meg az ismétlődő feladat a Visual Studio, és nyissa meg a kapcsolódó feladat megtekintése.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>1. eset: Rendelkezik az ismétlődő feladat URL-címe

Keresztül **eszközök > Data Lake > feladatok**, illessze be a feladat URL-cím a Visual Studio projekt nézet megnyitásához, és a nézet megnyitásához kapcsolódó feladatok kapcsolódó feladat megtekintése.

![A Data Lake Analytics Tools kapcsolódó feladatok megtekintése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>2. eset: Rendelkezik a folyamatot az ismétlődő feladat, de nem az URL-cím

A Visual Studio keresztül a feldolgozási sor böngésző megnyithat **Server Explorer > a Data Lake Analytics-fiók > folyamatok** (Ha ez a csomópont nem található a Server Explorer, kérje le a legfrissebb eszköz [Itt](http://aka.ms/adltoolsvs)). Adatcsatorna böngészőben ADLA fiók összes folyamatok leírása megtalálható a bal oldali, bővítheti a folyamatok található minden ismétlődő feladat, kattintson a problémák vannak, a kapcsolódó feladat nézet jobb megnyílik.

![A Data Lake Analytics Tools kapcsolódó feladatok megtekintése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![A Data Lake Analytics Tools kapcsolódó feladatok megtekintése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>2. lépés: Statisztikák jelentés elemzése

Összegzését és szinkronizálásistatisztika-jelentésről is látható tetején található kapcsolódó feladat megtekintése, amelyen keresztül lehetséges oka az a rendellenes kaphat. 

1.  Először meg kell a rendellenes feladat keresése a jelentésben. Az X tengelyen jeleníti meg a feladat küldésének időpontja, amelyen keresztül keresse meg a rendellenes feladat.
2.  Kövesse az alábbi folyamat, ellenőrizze a statisztikai adatokat, és a rendellenes az elemzések és a lehetséges megoldásokat.

![A Data Lake Analytics Tools kapcsolódó feladatok megtekintése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>3. lépés:, Hasonlítsa össze a rendellenes ismétlődő feladat a normál feladatok

Összes küldött ismétlődő feladatokat keresztül kapcsolódó feladat megtekintése alján a feladatlista találja. Kattintson a jobb gombbal a összehasonlíthatja a rendellenes feladat normál egy korábbi feladat különbözeti nézetben további elemzések és a lehetséges megoldások kereséséhez.

![A Data Lake Analytics Tools kapcsolódó feladatok megtekintése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Általában szükség nagy figyelmet fordítani az ilyen 2 feladatok nagy különbségei, lehet, hogy a teljesítményproblémákat okozó okok, és a következő lépések elvégzéséhez további ellenőrzés is követheti.

![A Data Lake Analytics Tools kapcsolódó feladatok megtekintése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Következő lépések

* [Hibakeresés és adatok eltérésére kapcsolatos problémák megoldásához](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [U-SQL a feladat sikertelen a felhasználói kód hiba hibakeresése](data-lake-analytics-debug-u-sql-jobs.md)