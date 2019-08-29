---
title: Azure Database for PostgreSQL Storage – egyetlen kiszolgáló automatikus növelése az Azure CLI-vel
description: Ez a cikk azt ismerteti, hogyan engedélyezheti az automatikus növekedés tárterületét az Azure CLI-vel Azure Database for PostgreSQL – egyetlen kiszolgálón.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b51158ca9ca54f5157e47e2fcbbada8890104105
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143751"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Azure Database for PostgreSQL Storage – egyetlen kiszolgáló automatikus növelése az Azure CLI használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for PostgreSQL-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A [tárolási korlátot elérő](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)kiszolgáló csak olvasható értékre van állítva. Ha a Storage automatikus növekedése engedélyezve van, akkor a 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. Az [itt](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) megadott maximális tárolási korlátozások érvényesek.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ez az útmutató az Azure CLI 2.0-s vagy újabb verzió használatát igényli. Adja meg, hogy ellenőrizze a verziót, az Azure CLI-vel parancssorban `az --version`. Telepítéshez vagy frissítéshez olvassa [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>A PostgreSQL-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növekedésének engedélyezése egy meglévő kiszolgálón a következő paranccsal:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

## <a name="next-steps"></a>További lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).