---
title: Automatikus tárhelynövelés - Azure portal – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogy miként engedélyezheti az Azure Database for MySQL automatikus bővítését az Azure Portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ddbcb0143a4f84d9e0f3dc2fedf780fca6637692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062520"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatikus tárhelyek az Azure Database for MySQL-ben az Azure Portal használatával
Ez a cikk azt ismerteti, hogyan konfigurálhatja az Azure Database for MySQL kiszolgálói tároló növekedését anélkül, hogy befolyásolná a számítási feladatokat.

Amikor egy kiszolgáló eléri a lefoglalt tárolási korlátot, a kiszolgáló írásvédettként lesz megjelölve. Ha azonban engedélyezi a tároló automatikus növekedését, a kiszolgáló tárhelye növekszik a növekvő adatok befogadására. A 100 GB-nál kevesebb üzembe foglalt tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-tal nő, amint az ingyenes tárhely a kiépített tár terület 1 GB-nál vagy a kiosztott tárterület 10%-ánál alacsonyabb. A 100 GB-nál több kiosztott tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület 5%-kal nő, ha a szabad tárhely a kiosztott tárterület 5%-a alatt van. Az [itt](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) megadott maximális tárolási korlátok érvényesek.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Azure-adatbázis a MySQL-kiszolgálóhoz](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Automatikus tárhelynövekedés engedélyezése 

A MySQL-kiszolgáló automatikus növekedésének beállításához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a meglévő Azure-adatbázis mySQL-kiszolgáló.

2. A MySQL-kiszolgáló lap **Beállítások** fejléce csoportban kattintson **a Tarifacsomag** elemre a Tarifacsomag lap megnyitásához.

3. Az Automatikus növekedés szakaszban válassza az **Igen** lehetőséget a tárhely automatikus növekedésének engedélyezéséhez.

    ![Azure Database for MySQL - Settings_Pricing_tier - Automatikus növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve volt.

    ![Azure Database for MySQL - automatikus növekedés sikere](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>További lépések

További információ [arról, hogyan hozhat létre riasztásokat a mérőszámokon.](howto-alert-on-metric.md)
