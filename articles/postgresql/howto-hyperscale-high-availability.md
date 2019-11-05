---
title: Magas rendelkezésre állás konfigurálása Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálói csoport számára
description: Magas rendelkezésre állás engedélyezése vagy letiltása
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5ed29be1e890ddf2c4208ce9c03f01ce44f0e0d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515914"
---
# <a name="configure-hyperscale-citus-high-availability"></a>A nagy kapacitású (Citus) magas rendelkezésre állásának konfigurálása

A Azure Database for PostgreSQL-nagy kapacitású (Citus) magas rendelkezésre állást biztosít az adatbázis leállásának elkerülése érdekében. HA engedélyezve van, a kiszolgálócsoport minden csomópontja készenléti állapotba kerül. Ha az eredeti csomópont nem Kifogástalan állapotba kerül, a rendszer lecseréli a készenléti állapotot.

> [!IMPORTANT]
> Mivel a HA megduplázza a csoportba tartozó kiszolgálók számát, a rendszer a költségeket is megduplázza.

HA engedélyezi a lehetőséget a kiszolgálócsoport létrehozásakor, vagy később, a Azure Portalban lévő kiszolgálócsoport **Konfigurálás** lapján. A felhasználói felület mindkét esetben hasonló. Húzza a csúszkát a **magas rendelkezésre álláshoz** a be-és kikapcsoláshoz:

![Ha csúszka](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Kattintson a **Save (Mentés** ) gombra a kijelölés alkalmazásához. HA engedélyezi, hogy a kiszolgálócsoport kiépítse a készenléti és adatstream-adatforgalmat, eltarthat egy ideig.

A kiszolgálócsoport **Áttekintés** lapja felsorolja az összes csomópontot és a készenléti állapotot, valamint egy **magas rendelkezésre állású** oszlopot, amely azt jelzi, hogy az összes csomóponton sikeresen engedélyezve van-e a ha.

### <a name="next-steps"></a>További lépések

További információ a [magas rendelkezésre állásról](concepts-hyperscale-high-availability.md).
