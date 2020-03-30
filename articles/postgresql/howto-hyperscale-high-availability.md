---
title: Magas rendelkezésre állás konfigurálása - Nagy kapacitású (Citus) - Azure Database for PostgreSQL
description: A magas rendelkezésre állás engedélyezése vagy letiltása
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977657"
---
# <a name="configure-hyperscale-citus-high-availability"></a>A nagy kapacitású (Citus) magas rendelkezésre állásának konfigurálása

Azure Database for PostgreSQL – Hyperscale (Citus) magas rendelkezésre állást (HA) biztosít az adatbázis állásidejének elkerülése érdekében. Ha ha engedélyezve van, a kiszolgálócsoport minden csomópontja készenléti állapotban lesz. Ha az eredeti csomópont nem kifogástalanná válik, a készenléti állapot lép elő a helyére.

> [!IMPORTANT]
> Mivel a HA megduplázza a csoportkiszolgálóinak számát, a költségek is megduplázzák.

Ha engedélyezése a kiszolgálócsoport létrehozása során lehetséges, vagy ezt követően a **Konfigurálás** lapon a kiszolgálócsoport hoz az Azure Portalon. A felhasználói felület mindkét esetben hasonló. Húzza a csúszkát a **Magas rendelkezésre állás lehetőséget** IGEN állásra:

![ha csúszka](./media/howto-hyperscale-high-availability/01-ha-slider.png)

A **kijelölés** alkalmazásához kattintson a Mentés gombra. A HA engedélyezése eltarthat egy ideig, mivel a kiszolgálócsoport készenléti állapotba kerül, és adatokat továbbít hozzájuk.

A kiszolgálócsoport **Áttekintés** lapja felsorolja az összes csomópontot és azok készenléti állapotát, valamint egy **Magas rendelkezésre állás oszlopot,** amely jelzi, hogy a HA sikeresen engedélyezve van-e az egyes csomópontokhoz.

![a ha oszlop a kiszolgálócsoportban – áttekintés](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>További lépések

További információ a [magas rendelkezésre állásról.](concepts-hyperscale-high-availability.md)
