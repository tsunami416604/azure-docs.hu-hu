---
title: Geo-redundancia elérése Azure Stream Analytics feladatokhoz
description: Ez a cikk azt ismerteti, hogyan lehet a földrajzi feladatátvétel helyett a Azure Stream Analytics feladatok geo-redundanciát elérni.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 44b0394773485f8054eddc94c7d7e9b91baebc6d
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951092"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Geo-redundancia elérése Azure Stream Analytics feladatokhoz

A Azure Stream Analytics nem biztosít automatikus földrajzi feladatátvételt, de a Geo-redundancia eléréséhez több Azure-régióban is azonos Stream Analytics feladatok helyezhetők üzembe. Minden egyes feladathoz egy helyi bemeneti és egy helyi kimeneti forrás csatlakozik. Az alkalmazás feladata, hogy mindkét helyi bemenetbe küldje a bemeneti adatokat, és összehangolja a két regionális kimenetet. A Stream Analytics feladatok két különálló entitás.

Az alábbi ábrán egy, a Geo-redundáns Stream Analytics feladatok üzembe helyezése látható az Event hub bemenetével és az Azure Database kimenetével.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="Geo-redundáns stream Analytics-feladatok diagramja":::

## <a name="primarysecondary-strategy"></a>Elsődleges/másodlagos stratégia

Az alkalmazásnak kezelnie kell a régió kimeneti adatbázisát, amely az elsődleges, és a másodlagosnak számít. Az elsődleges régió meghibásodásakor az alkalmazás átvált a másodlagos adatbázisra, és elkezdi a frissítések olvasását az adott adatbázisból. Az ismétlődő olvasások minimalizálását lehetővé tevő tényleges mechanizmus az alkalmazástól függ.Ezt a folyamatot leegyszerűsítheti további információk megírásával a kimenetre. Hozzáadhat például egy időbélyeget vagy egy sorszámot az egyes kimenetekhez, így az ismétlődő sorok kihagyása triviális művelet. Az elsődleges régió visszaállítása után a rendszer felveszi a másodlagos adatbázist a hasonló mechanika használatával.

Bár a különböző bemeneti és kimeneti típusok különböző földrajzi replikálási lehetőségeket tesznek lehetővé, javasoljuk, hogy az ebben a cikkben ismertetett mintát használva a Geo-redundancia eléréséhez, mivel rugalmasságot és irányítást biztosít a két esemény gyártója és az esemény felhasználói számára.

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics feladatok figyelése és kezelése a PowerShell-lel](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Adatvezérelt hibakeresés a Azure Stream Analyticsban](stream-analytics-job-diagram-with-metrics.md)