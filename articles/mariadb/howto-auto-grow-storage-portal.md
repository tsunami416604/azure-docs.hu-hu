---
title: A tároló automatikus növekedése Azure Database for MariaDB a Azure Portal használatával
description: Ez a cikk azt ismerteti, hogyan engedélyezheti az automatikus növekedés tárolását Azure Database for MariaDB használatával Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9d7b126b5c07cc8661cf50b4c4e13f78592b118e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136696"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>A tároló automatikus növekedése Azure Database for MariaDB a Azure Portal használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

Ha egy kiszolgáló eléri a lefoglalt tárterület korlátját, a kiszolgáló csak olvashatóként van megjelölve. Ha azonban engedélyezi a tárterület automatikus növekedését, a kiszolgáló tárterülete megnöveli a növekvő adatmennyiséget. A 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiépített tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. Az [itt](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) megadott maximális tárolási korlátozások érvényesek.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [Azure Database for MariaDB-kiszolgáló](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Tárterület automatikus növekedésének engedélyezése 

Az alábbi lépéseket követve állíthatja be a MariaDB-kiszolgáló tárterületének automatikus növekedését:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MariaDB-kiszolgálót.

2. A MariaDB-kiszolgáló lapon, a **Beállítások** fejléc alatt kattintson a díjszabási csomag elemre a díjszabási szintek lap megnyitásához.

3. Az automatikus növekedés szakaszban válassza az **Igen** lehetőséget a tárterület automatikus növekedésének engedélyezéséhez.

    ![Azure Database for MariaDB-Settings_Pricing_tier – automatikus növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A változtatások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve lett.

    ![Azure Database for MariaDB – az automatikus növekedés sikere](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>További lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-metric.md).
