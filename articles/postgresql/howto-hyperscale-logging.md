---
title: Naplók – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Adatbázis-naplók elérése Azure Database for PostgreSQL-nagy kapacitású (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099043"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Naplók Azure Database for PostgreSQL-nagy kapacitású (Citus)

A PostgreSQL-naplók a nagy kapacitású (Citus) kiszolgálói csoport minden csomópontján elérhetők. A naplókat egy Storage-kiszolgálóra vagy egy elemzési szolgáltatásba is elküldheti. A naplók használatával azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt.

## <a name="accessing-logs"></a>Naplók elérése

A nagy kapacitású (Citus) koordinátor vagy munkavégző csomóponthoz tartozó PostgreSQL-naplók eléréséhez nyissa meg a csomópontot a Azure Portalban:

![csomópontok listája](media/howto-hyperscale-logging/choose-node.png)

A kiválasztott csomóponthoz nyissa meg a **diagnosztikai beállításokat**, és kattintson a **+ diagnosztikai beállítás hozzáadása**elemre.

![Diagnosztikai beállítások hozzáadása gomb](media/howto-hyperscale-logging/diagnostic-settings.png)

Válassza ki az új diagnosztikai beállítások nevét, és jelölje be a **PostgreSQLLogs** jelölőnégyzetet.  Válassza ki, hogy mely célhelyek kapják meg a naplókat.

![PostgreSQL-naplók kiválasztása](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>További lépések

- [Ismerkedés a log Analytics-lekérdezésekkel](/azure/azure-monitor/log-query/get-started-portal)
- További tudnivalók az [Azure Event hub](/azure/event-hubs/event-hubs-about) -ról
