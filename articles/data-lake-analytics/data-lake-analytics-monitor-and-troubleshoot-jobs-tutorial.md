---
title: Az Azure Data Lake Analytics figyelése – Azure-portál
description: Ez a cikk ismerteti, hogyan használhatja az Azure Portalon az Azure Data Lake Analytics-feladatok hibaelhárításához.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316586"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Feladatok monitorozása az Azure Data Lake Analyticsben az Azure Portallal

**Az összes feladat megtekintése**

1. Az Azure Portalon kattintson a **Microsoft Azure** a bal felső sarokban.
2. Kattintson a Data Lake Analytics-fiókja nevével ellátott csempére.  A feladat összegzése a **Feladatkezelés** csempén látható.

    ![Azure Data Lake Analytics feladatkezelés](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    A feladat menedzsment ad egy pillantást a feladat állapotát. Figyelje meg, hogy van egy sikertelen feladat.
3. A feladatok megtekintéséhez kattintson a **Feladatkezelés** csempére. A feladatok a **Futó**, **A Várakozási sorban**és a **Befejezve**kategóriába vannak kategorizálva. A sikertelen feladat a Befejezve szakaszban jelenik **meg.** Ez az első a listán. Ha sok feladata van, a **Szűrő** gombra kattintva könnyebben megtalálhatja a feladatokat.

    ![Azure Data Lake Analytics szűrőfeladatok](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kattintson a sikertelen feladatra a listából a feladat részleteinek megnyitásához:

    ![Az Azure Data Lake Analytics sikertelen feladat](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Figyelje meg az **Újraküldés** gombot. A probléma megoldása után újra elküldheti a feladatot.
5. Kattintson a kiemelt rész az előző screenshot megnyitni a hiba részleteit.  Látni fog valami ilyesmit:

    ![Az Azure Data Lake Analytics nem felelt meg a feladat részleteinek](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Azt mondja, hogy a forrásmappa nem található.
6. Kattintson **a Parancsfájl másolása gombra.**
7. A **FROM** elérési út frissítése:

    "/Samples/Data/SearchLog.tsv"
8. Kattintson a **Feladat elküldése** elemre.

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
