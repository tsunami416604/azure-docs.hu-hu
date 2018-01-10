---
title: "Rendellenes ismétlődő-feladat hibaelhárítása |} Microsoft Docs"
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
ms.openlocfilehash: 9b60c861810d6577b33aa0cdf14f26dc2cfc0e4d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Rendellenes ismétlődő-feladat hibaelhárítása

Ez a cikk bemutatja, hogyan használható [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) és ismétlődő feladatokkal kapcsolatos problémák elhárítása érdekében. További információ a feldolgozási sorban lévő és az ismétlődő feladatok a [Azure Data Lake és az Azure HDInsight blog](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Ismétlődő feladatok általában ossza meg a lekérdezés logikák és hasonló bemeneti adatok. Tegyük fel, hogy rendelkezik-e minden hétfő reggel futtat a reggel 8 ismétlődő feladat a megszámlálandó múlt héten heti aktív felhasználónként. Ezeket a feladatokat a parancsfájlok ossza meg a lekérdezés logikát tartalmaz egy parancsfájl-sablon. A bemenetek ezeket a feladatokat a múlt héten a használati adatok szolgálnak. A lekérdezés logikák és a hasonló bemeneti megosztása általában azt jelenti, hogy ezek a feladatok teljesítményének hasonló és állandó. Ha az ismétlődő feladatok egyikét hirtelen végez rendellenesen, sikertelen, vagy sok lelassul, érdemes:

- Tekintse meg az előző fut, hogy mi történt ismétlődő feladat statisztika jelentéseiben.
- Hasonlítsa össze a rendellenes feladat, hogy megtudja, mi normál egy megváltozott.

**Kapcsolódó feladatok** az Azure Data Lake Tools for Visual Studio segítségével mindkét esetben a hibaelhárítási folyamatban érdekében.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>1. lépés: Található ismétlődő feladat, és nyissa meg a kapcsolódó feladat megtekintése

Ismétlődő feladat probléma hibaelhárításához kapcsolódó feladat nézet használatához meg kell először keresse meg az ismétlődő feladat a Visual Studio, és nyissa meg a kapcsolódó feladat megtekintése.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>1. eset: Rendelkezik az ismétlődő feladat URL-címe

Keresztül **eszközök** > **Data Lake** > **feladatok**, illessze be a feladat URL-cím a Visual Studio projekt nézet megnyitásához. Válassza ki **kapcsolódó feladatok megtekintése** kapcsolódó feladat nézet megnyitásához.

![A Data Lake Analytics Tools megtekintési kapcsolódó feladatok hivatkozása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>2. eset: Rendelkezik a folyamatot az ismétlődő feladat, de nem az URL-cím

Visual Studio Server Explorer keresztül csővezeték böngésző megnyithatja > az Azure Data Lake Analytics-fiók > **folyamatok**. (Ha ez a csomópont nem találja a Server Explorer eszközben [töltse le a legújabb beépülő modul](http://aka.ms/adltoolsvs).) 

![A folyamatok csomópont kiválasztása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

Adatcsatorna böngészőben a Data Lake Analytics-fiók összes folyamatok leírása megtalálható a balra. Bontsa ki a kimenetátirányítási található minden ismétlődő feladat, és válassza ki azt, amelyik problémái vannak. Kapcsolódó feladat megtekintése jobb nyílik meg.

![Folyamat kiválasztása és a kapcsolódó feladat való megnyitásakor](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>2. lépés: Elemzése a szinkronizálásistatisztika-jelentésről

Összegzés és a statisztika jelentés tetején található kapcsolódó feladat megtekintése jelennek meg. A probléma lehetséges kiváltó okát itt találja. 

1.  A jelentés x tengelyéhez a feladat küldésének időt jeleníti meg. Ezzel a rendellenes feladat keresése.
2.  Az alábbi ábra a folyamat segítségével ellenőrizze a statisztika, és mélyebb betekintés a problémát, és a lehetséges megoldásokat.

![Folyamat diagramja statisztikai adatainak ellenőrzése](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>3. lépés:, Hasonlítsa össze a rendellenes feladat a normál feladatok

Minden elküldött, ismétlődő feladatokat keresztül a feladatlista kapcsolódó feladat megtekintése alján találja. További elemzések és a lehetséges megoldások kereséséhez kattintson a jobb gombbal a rendellenes feladat. A feladat különbözeti nézet segítségével összehasonlíthatja a rendellenes feladat normál egy korábbi.

![Helyi menü feladatok összehasonlítása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Ezek a feladatok két nagy különbségei figyelmet fordítania. Ezek a különbségek valószínűleg a teljesítményproblémákat okozó. További ellenőrzéséhez használja az alábbi diagram szemlélteti a lépéseket:

![Feladatok közötti különbségek ellenőrzése a következő folyamatábra](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>További lépések

* [Adatok-döntés teljesítése](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felhasználó által definiált C# programkódja sikertelen U-SQL feladatok hibakeresése](data-lake-analytics-debug-u-sql-jobs.md)