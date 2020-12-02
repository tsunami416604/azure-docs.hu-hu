---
title: Naplók – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Adatbázis-naplók elérése Azure Database for PostgreSQL-nagy kapacitású (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: f8840d5115cb552ed203705d37f8c692b3418947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492335"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Naplók Azure Database for PostgreSQL-nagy kapacitású (Citus)

A PostgreSQL-naplók a nagy kapacitású (Citus) kiszolgálói csoport minden csomópontján elérhetők. A naplókat egy Storage-kiszolgálóra vagy egy elemzési szolgáltatásba is elküldheti. A naplók használatával azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt.

## <a name="accessing-logs"></a>Naplók elérése

A nagy kapacitású (Citus) koordinátor vagy munkavégző csomóponthoz tartozó PostgreSQL-naplók eléréséhez nyissa meg a csomópontot a Azure Portalban:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="csomópontok listája":::

A kiválasztott csomóponthoz nyissa meg a **diagnosztikai beállításokat**, és kattintson a **+ diagnosztikai beállítás hozzáadása** elemre.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Diagnosztikai beállítások hozzáadása gomb":::

Válassza ki az új diagnosztikai beállítások nevét, és jelölje be a **PostgreSQLLogs** jelölőnégyzetet.  Válassza ki, hogy mely célhelyek kapják meg a naplókat.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL-naplók kiválasztása":::

## <a name="next-steps"></a>További lépések

- [Ismerkedés a log Analytics-lekérdezésekkel](../azure-monitor/log-query/log-analytics-tutorial.md)
- További tudnivalók az [Azure Event hub](../event-hubs/event-hubs-about.md) -ról