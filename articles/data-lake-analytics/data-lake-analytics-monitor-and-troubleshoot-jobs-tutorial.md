---
title: "Azure portál használata az Azure Data Lake Analytics-feladatok hibaelhárítása |} Microsoft Docs"
description: "Útmutató a Data Lake Analytics-feladatok hibaelhárítása az Azure portál használatával. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: f6168997c449be5354bd223c516d4f929a1bf894
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Az Azure portál használata az Azure Data Lake Analytics-feladatok hibaelhárítása
Útmutató a Data Lake Analytics-feladatok hibaelhárítása az Azure-portál használatával.

Ebben az oktatóanyagban fog beállításában hiányzó forrás fájl problémát, és az Azure portál segítségével a probléma elhárításához.

## <a name="submit-a-data-lake-analytics-job"></a>Data Lake Analytics-feladat küldése

Küldje el a következő U-SQL-feladatot:

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
A forrásfájl, a parancsfájl definiált **/Samples/Data/SearchLog.tsv1**, ahol meg kell **/Samples/Data/SearchLog.tsv**.


## <a name="troubleshoot-the-job"></a>A feladat hibaelhárítása

**A feladatok megtekintéséhez**

1. Az Azure-portálon kattintson **Microsoft Azure** bal felső sarokban.
2. Kattintson a Data Lake Analytics-fiókja nevével ellátott csempére.  Összefoglaló feladat jelenik meg a **feladatkezelés** csempére.

    ![Azure Data Lake Analytics-feladatok kezelése](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    A felügyeleti feladat lehetővé teszi egy pillanat alatt, a feladatok állapotát. Figyelje meg, nincs a sikertelen feladatokat.
3. Kattintson a **feladatkezelés** csempe a feladatok megtekintéséhez. A feladat szerint vannak kategóriába sorolva **futtató**, **várakozik**, és **befejezve**. Ekkor megjelenik a sikertelen feladat a **befejezve** szakasz. A lista első tanúsítványt kell lennie. Ha nagy mennyiségű feladatok, rákattinthat **szűrő** segítségével megkeresheti a feladatokat.

    ![Azure Data Lake Analytics szűrheti a feladatokat](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kattintson a sikertelen feladatot a listából a nyissa meg a feladat részletei:

    ![Az Azure Data Lake Analytics feladat sikertelen volt.](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Figyelje meg a **küldje el újra** gombra. A probléma megoldása után újraküldése a a feladatot.
5. Kattintson az előző képernyőképen nyissa meg a hiba részletes adatait a kijelölt része.  Látni fogja hasonlót:

    ![Az Azure Data Lake Analytics nem sikerült a feladat részletei](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Jelzi, hogy a forrás mappa nem található.
6. Kattintson a **parancsfájl ismétlődő**.
7. Frissítés a **FROM** elérési útja:

    "/ Samples/Data/SearchLog.tsv"
8. Kattintson a **Feladat elküldése** elemre.

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Ismerkedés az Azure Data Lake Analytics Azure PowerShell használatával](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés az Azure Data Lake Analytics és a U-SQL Visual Studio használatával](data-lake-analytics-u-sql-get-started.md)
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
