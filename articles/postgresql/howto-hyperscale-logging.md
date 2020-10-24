---
title: Naplók – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Adatbázis-naplók elérése Azure Database for PostgreSQL-nagy kapacitású (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: fac0db7f8c94113ff87d1c0a3091e4c0cbd5ed2b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489846"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Naplók Azure Database for PostgreSQL-nagy kapacitású (Citus)

A PostgreSQL-naplók a nagy kapacitású (Citus) kiszolgálói csoport minden csomópontján elérhetők. A naplókat egy Storage-kiszolgálóra vagy egy elemzési szolgáltatásba is elküldheti. A naplók használatával azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt.

## <a name="accessing-logs"></a>Naplók elérése

A nagy kapacitású (Citus) koordinátor vagy munkavégző csomóponthoz tartozó PostgreSQL-naplók eléréséhez nyissa meg a csomópontot a Azure Portalban:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="csomópontok listája":::

A kiválasztott csomóponthoz nyissa meg a **diagnosztikai beállításokat**, és kattintson a **+ diagnosztikai beállítás hozzáadása**elemre.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="csomópontok listája":::

Válassza ki az új diagnosztikai beállítások nevét, és jelölje be a **PostgreSQLLogs** jelölőnégyzetet.  Válassza ki, hogy mely célhelyek kapják meg a naplókat.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="csomópontok listája":::

## <a name="next-steps"></a>Következő lépések

- [Ismerkedés a log Analytics-lekérdezésekkel](../azure-monitor/log-query/get-started-portal.md)
- További tudnivalók az [Azure Event hub](../event-hubs/event-hubs-about.md) -ról