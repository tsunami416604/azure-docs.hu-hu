---
title: Naplók – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Adatbázis-naplók elérése Azure Database for PostgreSQL-nagy kapacitású (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 1dc7bc8e119de7c8fdcf09713286be2633457486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895874"
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

- [Ismerkedés a log Analytics-lekérdezésekkel](/azure/azure-monitor/log-query/get-started-portal)
- További tudnivalók az [Azure Event hub](/azure/event-hubs/event-hubs-about) -ról
