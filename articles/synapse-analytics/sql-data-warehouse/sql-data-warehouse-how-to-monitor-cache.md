---
title: A Gen2 gyorsítótár optimalizálása
description: Ismerje meg, hogyan figyelheti a Gen2 gyorsítótárát az Azure Portalon.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bb5560164af2b573e6aaffd4e4c62bbe0dc24a51
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350425"
---
# <a name="how-to-monitor-the-gen2-cache"></a>A Gen2 gyorsítótár figyelése

Ez a cikk ismerteti, hogyan figyelheti és háríthatja el a lassú lekérdezési teljesítményt annak meghatározásával, hogy a számítási feladatok optimálisan használják-e ki a Gen2 gyorsítótárat.

A Gen2 tárolási architektúra automatikusan rétegezi a leggyakrabban lekérdezett oszlopcentrikus szegmenseket egy, a Gen2 adatraktárakhoz tervezett NVMe alapú SSD-k gyorsítótárában. Nagyobb teljesítmény akkor valósul meg, ha a lekérdezések a gyorsítótárban található szegmenseket kérik le.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Hibaelhárítás az Azure Portal használatával

Az Azure Monitor segítségével megtekintheti a Gen2 gyorsítótár-metrikákat a lekérdezési teljesítmény hibaelhárításához. Először lépjen az Azure Portalra, és kattintson a **Figyelő**, **Metrikák** és **+ Hatókör kiválasztása elemre:**

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Az adattárház megkereséséhez használja a keresési és legördülő sávokat. Ezután válassza az alkalmaz lehetőséget.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

A Gen2 gyorsítótár hibaelhárításának legfontosabb mutatói a **gyorsítótár találati százaléka** és **a gyorsítótár által használt százalékos arány.** Válassza a **Gyorsítótár beolvasási százalékának kiválasztását,** majd a **metrikus hozzáadása** gombbal adja hozzá a használt gyorsítótár **százalékát.** 

![Gyorsítótár-mutatók](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Gyorsítótár találata és a felhasznált százalék

Az alábbi mátrix a gyorsítótár-metrikák értékein alapuló forgatókönyveket ismerteti:

|                                | **Magas gyorsítótár-találati arány** | **Alacsony gyorsítótár-találati arány** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Magas gyorsítótár használt százalék** |          1. példa           |          2. példa          |
| **Alacsony gyorsítótár használt százalék**  |          3. példa           |          4. példa          |

**1. forgatókönyv:** Optimálisan használja a gyorsítótárat. A lekérdezéseket lassító egyéb területek [hibáinak elhárítása.](sql-data-warehouse-manage-monitor.md)

**2. forgatókönyv:** Az aktuális munkaadatkészlet nem fér el a gyorsítótárba, ami a fizikai olvasások miatt alacsony gyorsítótár-leolvasási százalékot okoz. Fontolja meg a teljesítményszint növelése és a számítási feladatok újrafuttatását a gyorsítótár feltöltéséhez.

**3. forgatókönyv:** Valószínű, hogy a lekérdezés lassú, mert a gyorsítótárhoz nem kapcsolódó okok miatt. A lekérdezéseket lassító egyéb területek [hibáinak elhárítása.](sql-data-warehouse-manage-monitor.md) A költségek [csökkentése érdekében a példány leskálázását](sql-data-warehouse-manage-monitor.md) is fontolóra veheti a gyorsítótár méretének csökkentése érdekében. 

**4. forgatókönyv:** Volt egy hideg cache, amely lehet az oka annak, hogy a lekérdezés lassú volt. Fontolja meg a lekérdezés újrafuttatását, mivel a működő adatkészletnek most a gyorsítótárban kell lennie. 

> [!IMPORTANT]
> Ha a gyorsítótár-leolvasási százalék vagy a használt gyorsítótár százaléka nem frissül a számítási feladatok újrafuttatása után, a munkakészlet már a memóriában található. A rendszer csak a fürtözött oszlopcentrikus táblákat gyorsítótárazza.

## <a name="next-steps"></a>További lépések
Az általános lekérdezési teljesítményhangolásról a [Lekérdezés végrehajtásának figyelése](sql-data-warehouse-manage-monitor.md#monitor-query-execution)című témakörben talál további információt.
