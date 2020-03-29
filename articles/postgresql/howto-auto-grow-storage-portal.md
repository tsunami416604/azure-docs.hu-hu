---
title: Automatikus tárhelynövelés - Azure portal - Azure Database for PostgreSQL - Single Server
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a tárhely automatikus növekedését az Azure Database for PostgreSQL – Single Server Azure-portálhasználatával.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769234"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatikus tárhelynövelés az Azure-portál használatával a PostgreSQL Azure Database for PostgreSQL - Single Server szolgáltatásban
Ez a cikk azt ismerteti, hogyan konfigurálhatja a PostgreSQL-kiszolgálói tárolóazure-adatbázisának növekedését a munkaterhelés befolyásolása nélkül.

Amikor egy kiszolgáló eléri a lefoglalt tárolási korlátot, a kiszolgáló írásvédettként lesz megjelölve. Ha azonban engedélyezi a tároló automatikus növekedését, a kiszolgáló tárhelye növekszik a növekvő adatok befogadására. A 100 GB-nál kevesebb üzembe foglalt tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-tal nő, amint az ingyenes tárhely a kiépített tár terület 1 GB-nál vagy a kiosztott tárterület 10%-ánál alacsonyabb. A 100 GB-nál több kiosztott tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület 5%-kal nő, ha a szabad tárhely a kiosztott tárterület 5%-a alatt van. Az [itt](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) megadott maximális tárolási korlátok érvényesek.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Egy Azure-adatbázis a PostgreSQL-kiszolgálóhoz](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Automatikus tárhelynövekedés engedélyezése 

A PostgreSQL kiszolgálói tárhely automatikus növekedésének beállításához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL-kiszolgáló.

2. A PostgreSQL kiszolgálólapján a **Beállítások**csoportban kattintson **a Tarifacsomag** elemre a tarifacsomag-lap megnyitásához.

3. Az **Automatikus növekedés** szakaszban válassza az **Igen** lehetőséget a tárhely automatikus növekedésének engedélyezéséhez.

    ![Azure-adatbázis a PostgreSQL-hez - Settings_Pricing_tier - Automatikus növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A módosítások mentéséhez kattintson az **OK** gombra.

5. Egy értesítés megerősíti, hogy az automatikus növekedés sikeresen engedélyezve volt.

    ![Azure Database for PostgreSQL - automatikus növekedés sikere](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>További lépések

További információ [arról, hogyan hozhat létre riasztásokat a mérőszámokon.](howto-alert-on-metric.md)
