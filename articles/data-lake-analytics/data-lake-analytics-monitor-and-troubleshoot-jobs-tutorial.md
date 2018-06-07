---
title: Az Azure portál használatával Azure Data Lake Analytics-feladatok figyelése
description: Ez a cikk ismerteti az Azure Data Lake Analytics-feladatok hibaelhárítása az Azure-portál használatával.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8c61b8736dfb13f0c2c2520f22979ac51646e05f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623655"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Az Azure portál használata Azure Data Lake Analytics-feladatok figyelése

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
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
