---
title: Kiszolgáló paramétereinek konfigurálása az Azure Database for postgresql-hez Azure-portálon
description: Ez a cikk ismerteti, hogyan lehet a kiszolgáló paramétereinek konfigurálása az Azure Database for postgresql-hez az Azure Portalon keresztül.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0d0626c48ecebdead604aab93ab0602c698d0d77
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540537"
---
# <a name="configure-server-parameters-in-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása az Azure Portalon
Lista, megjelenítése és konfigurációs paraméter frissítése egy Azure Database for PostgreSQL-kiszolgálóhoz az Azure Portalon keresztül.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek van szükség:
- [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Megtekintését és szerkesztését paraméterek
1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Alatt a **beállítások** szakaszban jelölje be **kiszolgáló paramétereinek**. Az oldal paraméterek, értékek és leírások listáját jeleníti meg.
![Paraméterek áttekintő oldala](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Válassza ki a **legördülő lista** gombra például client_min_messages számba típusú paraméterek a lehetséges értékek megtekintéséhez.
![Lefelé eldobási számbavétele](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Válassza ki, vagy a kurzort a **i** (információ) gombra a cpu_index_tuple_cost numerikus paraméterek a lehetséges értékek megtekintéséhez.
![információ gombjára kattintva](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Ha szükséges, használja a **keresőmezőbe** szűkítéséhez egy bizonyos paraméterét. A Keresés a nevét és leírását, a paraméterek van.
![Keresési eredmények](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Módosítsa a paraméterértékeket, amelyet módosítani szeretne. Munkamenet összes módosítás lila szín jelöli ki vannak emelve. Ha módosította az értékeket, kiválaszthatja **mentése**. Vagy beállíthatja a **elveti** a módosításokat.
![Mentés vagy a módosítások elvetése](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Ha mentette az új értékeket a paraméterekhez, bármikor visszatérhet az alapértelmezett értékekre mindent kiválasztásával **összes alaphelyzetbe állítása**.
![Összes alaphelyzetbe állítása](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>További lépések
További információ:
- [Az Azure Database for PostgreSQL kiszolgáló paramétereinek áttekintése](concepts-servers.md)
- [Az Azure CLI-vel paraméterek konfigurálása](howto-configure-server-parameters-using-cli.md)
