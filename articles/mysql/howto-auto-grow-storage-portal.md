---
title: Automatikus növekedés storage, Azure database for MySQL-hez az Azure portal használatával
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus tárolási növelheti az Azure Database for MySQL-hez az Azure portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5343475f38dd5389d6b0e266ff7167925cd38d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676788"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatikus növekedés storage, Azure database for MySQL-hez az Azure portal használatával
Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Database for MySQL server storage növelheti a számítási feladatok befolyásolása nélkül.

Amikor egy kiszolgáló eléri a lefoglalt tárolási kapacitása, a kiszolgáló csak olvashatóként van megjelölve. Azonban ha engedélyezte a storage automatikus növekedés, a tárolást növeli az egyre növekvő adatok befogadásához. Kevesebb mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete eléri 5 GB-tal, amint a szabad tárhely nem éri el a nagyobb, mint 1 GB vagy 10 %-a felhasznált tárterület. Több mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete növekszik 5 % 5 %-a kiépített tárhely méretére alatt rendelkezésre álló szabad tárhely esetén. Maximális tárhely korlátozza a megadott [Itt](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Storage automatikus növekedés 

Kövesse az alábbi lépéseket az MySQL server storage automatikus növekedés beállításához:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki az Azure Database for MySQL-kiszolgálóhoz.

2. A MySQL-kiszolgáló oldalán a **beállítások** szakaszban kattintson **tarifacsomag** nyissa meg a díjszabási szint.

3. Az Auto-növekedés szakaszban válassza ki a **Igen** storage automatikus növekedés engedélyezéséhez.

    ![Azure Database for MySQL - Settings_Pricing_tier - Auto-növekedés](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. A változtatások mentéséhez kattintson az **OK** gombra.

5. Értesítés megerősíti, hogy az automatikus növekedés engedélyezése sikerült.

    ![Azure Database for MySQL - auto-növekedés sikeres](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>További lépések

Ismerje meg [riasztások létrehozása metrikákhoz](howto-alert-on-metric.md).
