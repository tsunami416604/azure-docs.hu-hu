---
title: Automatikus növekedés storage, Azure Database-ben az Azure portal használatával MariaDB-hez
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus tárolási növekszik az Azure Database for MariaDB Azure portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: bb3291b66776a5f0f6be16069b2d6a999b2d1f32
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676878"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatikus növekedés storage, Azure Database-ben az Azure portal használatával MariaDB-hez
Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Database for MariaDB-kiszolgálótár növelheti a számítási feladatok befolyásolása nélkül.

Amikor egy kiszolgáló eléri a lefoglalt tárolási kapacitása, a kiszolgáló csak olvashatóként van megjelölve. Azonban ha engedélyezte a storage automatikus növekedés, a tárolást növeli az egyre növekvő adatok befogadásához. Kevesebb mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete eléri 5 GB-tal, amint a szabad tárhely nem éri el a nagyobb, mint 1 GB vagy 10 %-a felhasznált tárterület. Több mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete növekszik 5 % 5 %-a kiépített tárhely méretére alatt rendelkezésre álló szabad tárhely esetén. Maximális tárhely korlátozza a megadott [Itt](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for MariaDB-kiszolgáló](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Storage automatikus növekedés 

Kövesse az alábbi lépéseket a kiszolgáló storage automatikus növekedés MariaDB beállításához:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki az Azure Database for MariaDB-kiszolgáló.

2. A MariaDB lapon alatt **beállítások** szakaszban kattintson **tarifacsomag** árképzési szint lap megnyitásához.

3. Az Auto-növekedés szakaszban válassza ki a **Igen** storage automatikus növekedés engedélyezéséhez.

    ![Azure Database for MariaDB - Settings_Pricing_tier - Auto-növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A változtatások mentéséhez kattintson az **OK** gombra.

5. Értesítés megerősíti, hogy az automatikus növekedés engedélyezése sikerült.

    ![Azure Database for MariaDB - auto-növekedés sikeres](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>További lépések

Ismerje meg [riasztások létrehozása metrikákhoz](howto-alert-metric.md).
