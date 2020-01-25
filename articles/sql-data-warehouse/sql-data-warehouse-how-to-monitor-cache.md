---
title: Optimalizálja a Gen2 cache-t
description: Megtudhatja, hogyan figyelheti a Gen2 cache-gyorsítótárat a Azure Portal használatával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 924705b7ce1d324583077797714491bdf3fc5cc9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721217"
---
# <a name="how-to-monitor-the-gen2-cache"></a>A Gen2 cache figyelése
A Gen2-tároló architektúrája automatikusan lekéri a leggyakrabban lekérdezett oszlopcentrikus-szegmenseket a NVMe-alapú SSD-meghajtókon alapuló, Gen2 adattárházak számára tervezett gyorsítótárban. Nagyobb teljesítmény érhető el, ha a lekérdezések a gyorsítótárban lévő szegmenseket kérik le. Ez a cikk bemutatja, hogyan figyelheti és elháríthatja a lassú lekérdezési teljesítményt, ha meghatározza, hogy a munkaterhelés optimálisan kihasználja-e a Gen2 cache-gyorsítótárat.  
## <a name="troubleshoot-using-the-azure-portal"></a>Hibakeresés a Azure Portal használatával
Az Azure Monitor használatával megtekintheti a Gen2 gyorsítótár-metrikáit a lekérdezés teljesítményének hibakereséséhez. Először lépjen a Azure Portalra, és kattintson a figyelés elemre:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Válassza a metrikák gombot, és adja meg az adattárház **előfizetését**, **erőforráscsoport** **-** **típusát**és **erőforrás-nevét** .

A Gen2 gyorsítótár hibaelhárításának legfontosabb mérőszámai a **gyorsítótár-találatok százalékos aránya** és a **gyorsítótárban használt százalék**. Konfigurálja az Azure metrikus diagramot a két metrika megjelenítéséhez.

![Gyorsítótár Metrikái](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Gyorsítótár-találat és a felhasznált százalék
Az alábbi mátrix a gyorsítótár metrikáinak értékein alapuló forgatókönyveket ismerteti:

|                                | **Nagy gyorsítótárbeli találatok százalékos aránya** | **Alacsony gyorsítótárbeli találatok százalékos aránya** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Nagy gyorsítótár használt százaléka** |          1\. forgatókönyv           |          2\. forgatókönyv          |
| **Alacsony gyorsítótár-használat százalékos aránya**  |          3\. forgatókönyv           |          4\. forgatókönyv          |

**1. forgatókönyv:** Ön optimálisan használja a gyorsítótárat. A lekérdezéseket esetlegesen lelassító egyéb területek [hibáinak megoldása](sql-data-warehouse-manage-monitor.md) .

**2. forgatókönyv:** A jelenlegi munkakészlet nem fér bele a gyorsítótárba, ami a fizikai olvasások miatt alacsony gyorsítótárbeli találati arányt okoz. Érdemes lehet felskálázást végezni a teljesítmény szintjén, és újra kell futtatni a munkaterhelést a gyorsítótár feltöltéséhez.

**3. forgatókönyv:** Valószínű, hogy a lekérdezés lassan fut, mert nem kapcsolódik a gyorsítótárhoz. A lekérdezéseket esetlegesen lelassító egyéb területek [hibáinak megoldása](sql-data-warehouse-manage-monitor.md) . Azt is megteheti, hogy [a példány méretezésével](sql-data-warehouse-manage-monitor.md) csökkenti a gyorsítótár méretét a költségek megtakarítása érdekében. 

**4. forgatókönyv:** A lassú gyorsítótárat használta, ami lehet az oka, hogy a lekérdezés lassú volt. Érdemes lehet újból futtatni a lekérdezést, mert a munkakészlete most már gyorsítótárazva van. 

> [!IMPORTANT]
> Ha a gyorsítótár-találatok százalékos aránya vagy a gyorsítótár kihasználtsági aránya nem frissül a munkaterhelés újbóli futtatása után, a munkakészlete már a memóriában is tartózkodik. A rendszer csak a fürtözött oszlopcentrikus-táblákat gyorsítótárazza.

## <a name="next-steps"></a>Következő lépések
Az általános lekérdezési teljesítmény finomhangolásával kapcsolatos további információkért lásd: a [lekérdezés-végrehajtás figyelése](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution).
