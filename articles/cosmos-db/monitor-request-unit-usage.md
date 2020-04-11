---
title: Egy művelet átviteli használatának figyelése az Azure Cosmos DB-ben
description: Megtudhatja, hogyan figyelheti az átviteli vagy kérelem egységhasználat egy művelet az Azure Cosmos DB.Ismerje meg, hogyan figyelheti az átviteli vagy kérelem egységhasználat egy művelet az Azure Cosmos DB.Ismerje meg, hogyan figyelheti az átviteli vagy kérelem egységhasználat egy művelet az Azure Cosmos DB.Ismerje meg Az Azure Cosmos DB-fiók tulajdonosai megérthetik, hogy mely műveletek vesznek több kérelemegységet.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115429"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben egy művelet átviteli vagy egyadott művelet használatának kérése

Az Azure Cosmos DB Azure Monitor egy metrikanézetet biztosít a fiók figyeléséhez és irányítópultok létrehozásához. Az Azure Cosmos DB metrikák gyűjtik alapértelmezés szerint, ez a funkció nem igényel semmit explicit módon engedélyezni vagy konfigurálni. A **teljes kérelemegységek** metrika a különböző típusú műveletek kérésegység-használatának lekéréséhez használatos. Később elemezheti, hogy mely műveletek használták az átviteli teljesítmény nagy részét. Alapértelmezés szerint az átviteli adatok egyperces időközönként összesítve lesznek. Az összesítési egységet azonban módosíthatja az időrészletességi beállítás módosításával.

A kérelemegység használati adatainak elemzése kétféleképpen használható:

* A megadott időintervallumon belül mely műveletek vesznek több kérelemegységet.
* Mely műveletek uralják általában a számítási feladatokat több kérelemegység fogyasztásával.
Ez az elemzés lehetővé teszi, hogy összpontosítson műveletek, mint a beszúrás, upsert és nézd meg az indexelés. Megtudhatja, hogy túllép-e/alul bizonyos mezőket indexelésen, és módosíthatja az [indexelési házirendet,](index-policy.md#include-exclude-paths) hogy tartalmazza vagy kizárja az elérési utakat.

Ha azt veszi észre, hogy bizonyos lekérdezések több kérelemegységet vesznek igénybe, az alábbi műveleteket is végrehajthatja:

* Gondolja át, ha a megfelelő mennyiségű adatot kéri.
* Módosítsa a lekérdezést úgy, hogy az indexszűrő záradékkal együtt szerepeljen.
* Kevésbé költséges UDF-függvényhívások végrehajtása.
* Partíciókulcsok definiálása a lekérdezésből a különböző partíciókra való kis méretűre állításához.
* Használhatja a lekérdezés metrikák visszaadott a hívásválasz, a diagnosztikai napló részleteit, és olvassa el a [lekérdezés teljesítményének hangolása](sql-api-query-metrics.md) cikket, hogy többet tudjon meg a lekérdezés végrehajtását.
* Az összegből kezdhet, majd a megfelelő dimenzióval avg kihasználtságot is megkeresheti.

## <a name="view-the-total-request-unit-usage-metric"></a>A kérelemegység használati mutatójának megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a Bal oldali navigációs sáv **Monitor** elemét, majd a **Metrikák lehetőséget.**

   ![Metrikák ablaktábla az Azure Monitorban](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. A **Metrikák** ablaktáblán > **Válassza ki az erőforrást,** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrás típusához**válassza az **Azure Cosmos DB-fiókok at,** válassza ki a meglévő Azure Cosmos-fiókok egyikét, és válassza az **Alkalmaz lehetőséget.**

   ![Válassza ki az Azure Cosmos DB-fiókot a metrikák megtekintéséhez](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Ezután válassza ki a **Teljes kérelemegység** mutatót az elérhető metrikák listájából. Ha részletesen szeretne tudni a listában elérhető összes metrikáról, tekintse meg a [Mutatók kategória szerint](monitor-cosmos-db-reference.md) cikket. Ebben a példában válasszuk **a Teljes kérelemegységek** és **az Átlag** összesítési értéket. Ezeken a részleteken kívül kiválaszthatja a mérőszámok **időtartományát** és **időrészletességét** is. Max, megtekintheti az elmúlt 30 nap metrikák.  A szűrő alkalmazása után a szűrő alapján megjelenik egy diagram. Megtekintheti a kiválasztott időszakban percenként felhasznált kérelemegységek átlagos számát.  

   ![Válasszon egy metrikát az Azure Portalról](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Szűrők a kérelemegység használatához

Szűrheti a mutatókat, és leírhatja a diagramokat egy adott **Gyűjteménynév**, **DatabaseName**, **OperationType**, **Region**, **Status**és **StatusCode**szerint. A **Szűrő hozzáadása** és felosztás **alkalmazása** beállítás lehetővé teszi a kérelemegység használatának szűrését és a metrikák csoportosítását.

Ha az egyes műveletek kérésegység-használatát összeg(összeg) vagy átlag szerint szeretné lekérni, válassza **a Felosztás alkalmazása lehetőséget,** és válassza a **Művelet típusát** és a szűrőértéket az alábbi képen látható módon:

   ![Cosmos DB kérelem egységek műveletek az Azure monitoron](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Ha gyűjtemény szerint szeretné látni a kérelemegység használatát, válassza **a Felosztás alkalmazása lehetőséget,** és szűrőként válassza ki a gyűjtemény nevét. Az irányítópulton belüli gyűjteményválasztékkal az alábbihoz hasonló csevegést fog látni. Ezután kiválaszthat egy adott gyűjteménynevet a további részletek megtekintéséhez:

   ![Cosmos DB request egységek az Azure-figyelőben lévő gyűjtemény összes műveletéhez](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB-adatok figyelése az Azure [diagnosztikai beállításaival.](cosmosdb-monitor-resource-logs.md)
* [Az Azure Cosmos DB vezérlősík-műveleteinek naplózása](audit-control-plane-logs.md)