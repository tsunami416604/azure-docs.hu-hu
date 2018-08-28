---
title: Feladatok figyelése az Azure Data Lake Analytics az Azure portal használatával
description: Ez a cikk ismerteti, hogyan használhatja az Azure Portalt Azure Data Lake Analytics-feladatok hibaelhárításához.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 1e18addc43e53cb45e92966607ad5d1db2b42c3c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046719"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Az Azure Portal használata az Azure Data Lake Analytics-feladatok figyelése

**Az összes feladat megtekintéséhez**

1. Az Azure Portalon kattintson **Microsoft Azure** a bal felső sarokban.
2. Kattintson a Data Lake Analytics-fiókja nevével ellátott csempére.  A feladat összegzésében jelenik meg a **feladatkezelés** csempére.

    ![Az Azure Data Lake Analytics-feladatok kezelése](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    A felügyeleti feladatot a feladat állapotának gyors biztosítja. Figyelje meg, hogy a sikertelen feladat.
3. Kattintson a **feladatkezelés** csempére a feladatok megtekintéséhez. A feladat szerint vannak kategóriába sorolva **futó**, **várólistán**, és **állapotig**. Meg kell jelennie a sikertelen feladat a **állapotig** szakaszban. A lista első egy kell lennie. Amikor sok feladatok, kattintson **szűrő** annak érdekében, hogy keresse meg a feladatok.

    ![Az Azure Data Lake Analytics-feladatok szűrése](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kattintson a sikertelen feladatot a listából, nyissa meg a feladat részletei:

    ![Az Azure Data Lake Analytics feladat sikertelen volt.](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Figyelje meg a **küldje el újra** gombra. Miután a problémán, lehetősége van újraküldeni a feladatot.
5. Kattintson a kiemelt rész az előző képernyőképen látható, nyissa meg a hiba részletes adatait.  Meg kell jelennie hasonló:

    ![Az Azure Data Lake Analytics nem sikerült a feladat részletei](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Megadja, hogy nem található a megadott forrásmappához.
6. Kattintson a **parancsfájl ismétlődő**.
7. Frissítés a **FROM** elérési útja:

    "/ Samples/Data/SearchLog.tsv"
8. Kattintson a **Feladat elküldése** elemre.

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Ismerkedés az Azure Data Lake Analytics az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
