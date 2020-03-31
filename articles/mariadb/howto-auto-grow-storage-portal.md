---
title: Automatikus tárhelynövelés - Azure portal – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogy miként engedélyezheti az Azure Database for MariaDB automatikus tárhelyének bővítését az Azure Portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528897"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatikus tárhely ekként növekszik a MariaDB Azure Database-ben az Azure Portal használatával
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Database for MariaDB kiszolgálói tároló növekedését anélkül, hogy befolyásolná a számítási feladatokat.

Amikor egy kiszolgáló eléri a lefoglalt tárolási korlátot, a kiszolgáló írásvédettként lesz megjelölve. Ha azonban engedélyezi a tároló automatikus növekedését, a kiszolgáló tárhelye növekszik a növekvő adatok befogadására. A 100 GB-nál kevesebb üzembe foglalt tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-tal nő, amint az ingyenes tárhely a kiépített tár terület 1 GB-nál vagy a kiosztott tárterület 10%-ánál alacsonyabb. A 100 GB-nál több kiosztott tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület 5%-kal nő, ha a szabad tárhely a kiosztott tárterület 5%-a alatt van. Az [itt](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) megadott maximális tárolási korlátok érvényesek.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Azure-adatbázis a MariaDB-kiszolgálóhoz](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Automatikus tárhelynövekedés engedélyezése 

A MariaDB kiszolgáló automatikus növekedésének beállításához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a meglévő Azure-adatbázis MariaDB-kiszolgáló.

2. A MariaDB kiszolgálólapján a **Beállítások** fejlécben kattintson **a Tarifacsomag** elemre a tarifacsomag lap megnyitásához.

3. Az Automatikus növekedés szakaszban válassza az **Igen** lehetőséget a tárhely automatikus növekedésének engedélyezéséhez.

    ![Azure-adatbázis a MariaDB-hez - Settings_Pricing_tier - Automatikus növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve volt.

    ![Azure Database for MariaDB – automatikus növekedés sikere](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>További lépések

További információ [arról, hogyan hozhat létre riasztásokat a mérőszámokon.](howto-alert-metric.md)
