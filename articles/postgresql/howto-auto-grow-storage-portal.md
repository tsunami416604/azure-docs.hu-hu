---
title: Automatikus növekedés az Azure Portalon az Azure Database for PostgreSQL - kiszolgáló egyetlen tároló
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés az Azure Portalon az Azure Database for PostegreSQL – egyetlen kiszolgáló storage
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676758"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatikus növekedés az Azure Portalon az Azure Database for PostgreSQL - kiszolgáló egyetlen tároló
Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Database for PostgreSQL-kiszolgáló tár növelheti a számítási feladatok befolyásolása nélkül.

Amikor egy kiszolgáló eléri a lefoglalt tárolási kapacitása, a kiszolgáló csak olvashatóként van megjelölve. Azonban ha engedélyezte a storage automatikus növekedés, a tárolást növeli az egyre növekvő adatok befogadásához. Kevesebb mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete eléri 5 GB-tal, amint a szabad tárhely nem éri el a nagyobb, mint 1 GB vagy 10 %-a felhasznált tárterület. Több mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete növekszik 5 % 5 %-a kiépített tárhely méretére alatt rendelkezésre álló szabad tárhely esetén. Maximális tárhely korlátozza a megadott [Itt](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Storage automatikus növekedés 

Kövesse az alábbi lépéseket a PostgreSQL-kiszolgáló tárolási automatikus növekedés beállításához:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki az Azure Database for PostgreSQL-kiszolgálóhoz.

2. A PostgreSQL-kiszolgáló oldalán a **beállítások**, kattintson a **tarifacsomag** árképzési szint lap megnyitásához.

3. Az a **Auto-növekedés** szakaszban jelölje be **Igen** storage automatikus növekedés engedélyezéséhez.

    ![Azure Database for PostgreSQL - Settings_Pricing_tier - Auto-növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A változtatások mentéséhez kattintson az **OK** gombra.

5. Értesítés megerősíti, hogy az automatikus növekedés engedélyezése sikerült.

    ![Azure Database for PostgreSQL - auto-növekedés sikeres](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>További lépések

Ismerje meg [riasztások létrehozása metrikákhoz](howto-alert-on-metric.md).
