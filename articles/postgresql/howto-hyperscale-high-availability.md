---
title: Magas rendelkezésre állás konfigurálása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Magas rendelkezésre állás engedélyezése vagy letiltása
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f08fb01cf26ddf73d31b575242b27d7d8b4017d9
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033275"
---
# <a name="configure-hyperscale-citus-high-availability"></a>A nagy kapacitású (Citus) magas rendelkezésre állásának konfigurálása

A Azure Database for PostgreSQL-nagy kapacitású (Citus) magas rendelkezésre állást biztosít az adatbázis leállásának elkerülése érdekében. HA engedélyezve van, a kiszolgálócsoport minden csomópontja készenléti állapotba kerül. Ha az eredeti csomópont nem Kifogástalan állapotba kerül, a rendszer lecseréli a készenléti állapotot.

> [!IMPORTANT]
> Mivel a HA megduplázza a csoportba tartozó kiszolgálók számát, a rendszer a költségeket is megduplázza.

A beállítás engedélyezése a kiszolgálócsoport létrehozása során lehetséges, vagy később a Azure Portal lévő kiszolgálói csoport **számítási és tárolási** lapján. A felhasználói felület mindkét esetben hasonló. Húzza a csúszkát a **magas rendelkezésre álláshoz** a nem értéket Igen értékre:

![Ha csúszka](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Kattintson a **Save (Mentés** ) gombra a kijelölés alkalmazásához. HA engedélyezi, hogy a kiszolgálócsoport kiépítse a készenléti és adatstream-adatforgalmat, eltarthat egy ideig.

A kiszolgálócsoport **Áttekintés** lapja felsorolja az összes csomópontot és a készenléti állapotot, valamint egy **magas rendelkezésre állású** oszlopot, amely azt jelzi, hogy az összes csomóponton sikeresen engedélyezve van-e a ha.

![a kiszolgáló csoportjában található ha oszlop – áttekintés](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Következő lépések

További információ a [magas rendelkezésre állásról](concepts-hyperscale-high-availability.md).
