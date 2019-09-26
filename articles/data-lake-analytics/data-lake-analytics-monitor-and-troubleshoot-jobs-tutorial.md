---
title: Azure Data Lake Analytics figyelése – Azure Portal
description: Ez a cikk azt ismerteti, hogyan használható a Azure Portal a Azure Data Lake Analytics feladatok hibaelhárításához.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316586"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Feladatok figyelése Azure Data Lake Analytics az Azure Portal használatával

**Az összes feladat megjelenítéséhez**

1. A Azure Portal kattintson a bal felső sarokban található **Microsoft Azure** elemre.
2. Kattintson a Data Lake Analytics-fiókja nevével ellátott csempére.  A feladatok összegzése megjelenik a **Feladatkezelő** csempén.

    ![Azure Data Lake Analytics feladatok kezelése](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    A Feladatkezelő áttekintést nyújt a feladatok állapotáról. Figyelje meg, hogy egy sikertelen feladatot észlelt a rendszer.
3. A feladatok megtekintéséhez kattintson a **feladat-kezelés** csempére. A feladatok a **Futtatás**, a **várólista**és a **Befejezés**szerint vannak kategorizálva. A sikertelen feladatot a **befejezett** szakaszban tekintheti meg. A listában elsőként kell megadnia. Ha sok feladattal rendelkezik, a **szűrő** lehetőségre kattintva megkeresheti a feladatokat.

    ![Azure Data Lake Analytics szűrési feladatok](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kattintson a sikertelen feladatra a listából a feladatok részleteinek megnyitásához:

    ![Azure Data Lake Analytics sikertelen feladatot](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Figyelje meg az Újraküldés gombot. A probléma megoldását követően újra elküldheti a feladatot.
5. A hiba részleteinek megnyitásához kattintson a Kiemelt részre az előző képernyőképen.  A következőhöz hasonlót kell látnia:

    ![Azure Data Lake Analytics sikertelen feladatok részletei](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Azt jelzi, hogy a forrás mappája nem található.
6. Kattintson a **duplikált parancsfájl**elemre.
7. Az elérési út frissítése a **következőre** :

    /Samples/Data/SearchLog.TSV
8. Kattintson a **Feladat elküldése** elemre.

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
