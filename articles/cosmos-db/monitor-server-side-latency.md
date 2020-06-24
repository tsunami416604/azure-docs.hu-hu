---
title: A Azure Cosmos DB-műveletek kiszolgálóoldali késésének figyelése
description: Megtudhatja, hogyan figyelheti a kiszolgáló késését Azure Cosmos DB fiókban vagy tárolóban végzett műveletekhez. Egy Azure Cosmos DB fiók tulajdonosai megismerhetik az Azure Cosmos-fiókokkal kapcsolatos kiszolgálóoldali késési problémákat.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262701"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Azure Cosmos DB tárolóban vagy fiókban lévő műveletek kiszolgálóoldali késésének figyelése

A Azure Cosmos DB Azure Monitor metrikai nézetet biztosít a fiók figyeléséhez és az irányítópultok létrehozásához. A rendszer alapértelmezés szerint összegyűjti a Azure Cosmos DB metrikákat, így nem kell explicit módon engedélyeznie vagy konfigurálnia a szolgáltatást. A kiszolgálóoldali késés mérőszáma egy művelet kiszolgálóoldali késésének megtekintésére szolgál. Azure Cosmos DB a közvetlen kapcsolattal rendelkező pont írási/olvasási műveleteinél 10 MS-nál kevesebb SLA-t biztosít. Az olvasási és írási műveletek esetében a SLA-kat az [SLA-dokumentumban](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)részletezett módon számítjuk ki.

Ha szokatlanul nagy késést tapasztal a pontok műveleteinél, például:

* Egy Get vagy set művelet partíciós kulccsal és AZONOSÍTÓval közvetlen módban
* Olvasási vagy írási művelet vagy
* Egy lekérdezés

A visszaadott adatmennyiség megjelenítéséhez megkeresheti a diagnosztikai naplót. Ha a lekérdezési műveletekhez tartósan magas késés tapasztalható, megkeresheti a diagnosztikai naplót a visszaadott adatok méretére, az [átviteli sebességre vagy az ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) értékre, illetve az ilyen műveletek számát egy adott időszakban. Így hibakeresést végezhet a kiszolgálóoldali késéssel kapcsolatos problémákról.

## <a name="view-the-server-side-latency-metric"></a>Kiszolgálóoldali késési metrika megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák**lehetőséget.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Metrikák ablaktábla Azure Monitor":::

1. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrástípus**mezőben válassza a **Azure Cosmos db fiókok**lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz**lehetőséget.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Válassza ki a Azure Cosmos DB fiókot a metrikák megtekintéséhez":::

1. Ezután válassza ki a **kiszolgálóoldali késés** metrikáját az elérhető metrikák listájából. A listán szereplő összes mérőszám részletes megismeréséhez tekintse meg a [metrikák kategória szerint](monitor-cosmos-db-reference.md) című cikket. Ebben a példában a **kiszolgálóoldali késést** és az **átlagot** adja meg az összesítési értékként. Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján. A kiszolgálóoldali késést percenként láthatja a kiválasztott időszakra vonatkozóan.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Válassza ki a kiszolgálóoldali késés metrikáját a Azure Portal":::

## <a name="filters-for-server-side-latency"></a>Kiszolgálóoldali késéssel kapcsolatos szűrők

Emellett szűrheti a metrikákat, és lekérheti az adott **CollectionName**, a **ConnectionMode**, a **databasename**, a **OperationType**, a **régió**és a **PublicAPIType**által megjelenített diagramokat. 

A metrikák szűréséhez válassza a **szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot (például **PublicAPIType** ), és válassza ki az **SQL**értéket. Adjon hozzá egy másik szűrőt a **OperationType**. A gráf ezután megjeleníti a kiszolgálóoldali késést a különböző műveletekhez a kiválasztott időszakban. A tárolt eljáráson keresztül végrehajtott műveletek nincsenek naplózva, így azok nem érhetők el az OperationType metrika alatt.

Az egyes műveletek **kiszolgálóoldali késési** mérőszámai az alábbi képen látható módon jelennek meg:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Kiszolgálóoldali késési metrikák szűrőinek szűrői":::

A metrikákat a **felosztás alkalmazása** lehetőség használatával is csoportosíthatja.  

## <a name="next-steps"></a>További lépések

* Az Azure-ban [diagnosztikai beállításokkal](cosmosdb-monitor-resource-logs.md) figyelheti Azure Cosmos db az adataikat.
* [Azure Cosmos DB vezérlési sík műveleteinek naplózása](audit-control-plane-logs.md)
