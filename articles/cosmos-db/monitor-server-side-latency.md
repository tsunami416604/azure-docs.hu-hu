---
title: Az Azure Cosmos DB-ben végzett műveletek kiszolgálóoldali késésének figyelése
description: Megtudhatja, hogyan figyelheti a kiszolgáló késése műveletek az Azure Cosmos DB-fiók vagy egy tároló. Az Azure Cosmos DB-fiók tulajdonosai megismerhetik az Azure Cosmos-fiókok kiszolgálóoldali késésével kapcsolatos problémákat.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 38f5778e363a16117bab482a758666ec81352afd
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887566"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Az Azure Cosmos DB-tárolóban vagy -fiókban lévő műveletek kiszolgálóoldali késésének figyelése

Az Azure Cosmos DB Azure Monitor egy metrikanézetet biztosít a fiók figyeléséhez és irányítópultok létrehozásához. Az Azure Cosmos DB metrikák gyűjtik alapértelmezés szerint, ez a funkció nem igényel semmit explicit módon engedélyezni vagy konfigurálni. A kiszolgálóoldali késésmetria egy művelet kiszolgálóoldali késésének megtekintéséhez szolgál. Az Azure Cosmos DB 10 ms-nál kisebb SLA-t biztosít a közvetlen kapcsolattal rendelkező pontolvasási/írási műveletekhez. Pontolvasási és írási műveletek esetén az SLA-k kiszámítása az [SLA-dokumentumban](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)részletezett módon történik.

Ha szokatlanul nagy késleltetést lát a pontműveleteknél, például:

* A get vagy set művelet partíciókulccsal és azonosítóval közvetlen módban
* Olvasási vagy írási művelet vagy
* Lekérdezés

Megkeresheti a diagnosztikai naplót a visszaadott adatok méretének megtekintéséhez. Ha tartósan nagy késést lát a lekérdezési műveletekhez, megkeresheti a diagnosztikai naplót a visszaadott adatok, [átviteli vagy RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) használt mérete, vagy az ilyen műveletek száma egy adott időszakban. Ily módon a kiszolgálóoldali késési problémák hibakeresése.

## <a name="view-server-side-latency-metric"></a>Kiszolgálóoldali késésmérő nézetének megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a Bal oldali navigációs sáv **Monitor** elemét, majd a **Metrikák lehetőséget.**

   ![Metrikák ablaktábla az Azure Monitorban](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. A **Metrikák** ablaktáblán > **Válassza ki az erőforrást,** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrás típusához**válassza az **Azure Cosmos DB-fiókok at,** válassza ki a meglévő Azure Cosmos-fiókok egyikét, és válassza az **Alkalmaz lehetőséget.**
   
   ![A metrikák megtekintéséhez válasszon egy Cosmos DB-fiókot](./media/monitor-server-side-latency/select-cosmosdb-account.png)

1. Ezután válassza ki a **kiszolgálóoldali késés** metrika az elérhető metrikák listájából. Ha részletesen szeretne tudni a listában elérhető összes metrikáról, tekintse meg a [Mutatók kategória szerint](monitor-cosmos-db-reference.md) cikket. Ebben a példában válasszuk **a kiszolgálóoldali késés** és **az átlag** az összesítési érték. Ezeken a részleteken kívül kiválaszthatja a mérőszámok **időtartományát** és **időrészletességét** is. Max, megtekintheti az elmúlt 30 nap metrikák.  A szűrő alkalmazása után a szűrő alapján megjelenik egy diagram. Megtekintheti a kiválasztott időszakban percenként felhasznált kérelemegységek átlagos számát.  

   ![Válassza ki a kiszolgálóoldali késés metrikát az Azure Portalon](./media/monitor-server-side-latency/serverside-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Szűrők a kiszolgálóoldali késéshez

A mutatókat és a diagramot egy adott **CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**és **PublicAPIType**szerint is szűrheti. 

A mérőszámok szűréséhez válassza a **Szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot, például a **PublicAPIType tulajdonságot,** és válassza ki az **sql**értéket. Adjon hozzá egy másik szűrőt az **OperationType programhoz.** A diagram ezután megjeleníti a kiszolgálóoldali késést a kiválasztott időszak különböző műveleteihez. A tárolt eljárással végrehajtott műveletek nincsenek naplózva, így nem érhetők el az OperationType metrika alatt.

A **kiszolgálóoldali késés** metrikák az egyes műveletek jelennek meg az alábbi képen látható módon:

![Szűrők a kiszolgálóoldali késési metrikákhoz](./media/monitor-server-side-latency/serverside-latency-filters.png)

A mutatókat a **Felosztás alkalmazása** beállítással is csoportosíthatja.  

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB-adatok figyelése az Azure [diagnosztikai beállításainak](cosmosdb-monitor-resource-logs.md) használatával
* [Az Azure Cosmos DB vezérlősík-műveleteinek naplózása](audit-control-plane-logs.md)