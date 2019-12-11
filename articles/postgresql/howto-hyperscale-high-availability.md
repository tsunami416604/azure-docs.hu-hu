---
title: Magas rendelkezésre állás konfigurálása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Magas rendelkezésre állás engedélyezése vagy letiltása
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977657"
---
# <a name="configure-hyperscale-citus-high-availability"></a>A nagy kapacitású (Citus) magas rendelkezésre állásának konfigurálása

A Azure Database for PostgreSQL-nagy kapacitású (Citus) magas rendelkezésre állást biztosít az adatbázis leállásának elkerülése érdekében. HA engedélyezve van, a kiszolgálócsoport minden csomópontja készenléti állapotba kerül. Ha az eredeti csomópont nem Kifogástalan állapotba kerül, a rendszer lecseréli a készenléti állapotot.

> [!IMPORTANT]
> Mivel a HA megduplázza a csoportba tartozó kiszolgálók számát, a rendszer a költségeket is megduplázza.

HA engedélyezi a lehetőséget a kiszolgálócsoport létrehozásakor, vagy később, a Azure Portalban lévő kiszolgálócsoport **Konfigurálás** lapján. A felhasználói felület mindkét esetben hasonló. Húzza a csúszkát a **magas rendelkezésre álláshoz** az Igen értékre:

![Ha csúszka](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Kattintson a **Save (Mentés** ) gombra a kijelölés alkalmazásához. HA engedélyezi, hogy a kiszolgálócsoport kiépítse a készenléti és adatstream-adatforgalmat, eltarthat egy ideig.

A kiszolgálócsoport **Áttekintés** lapja felsorolja az összes csomópontot és a készenléti állapotot, valamint egy **magas rendelkezésre állású** oszlopot, amely azt jelzi, hogy az összes csomóponton sikeresen engedélyezve van-e a ha.

![a kiszolgáló csoportjában található ha oszlop – áttekintés](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Következő lépések

További információ a [magas rendelkezésre állásról](concepts-hyperscale-high-availability.md).
