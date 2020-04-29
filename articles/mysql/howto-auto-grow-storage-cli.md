---
title: Tárterület automatikus növekedése – Azure CLI – Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés tárolását a Azure Database for MySQL Azure CLI használatával.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 44ce852aaf2ed5839650132c6eae95728c27dc5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062627"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Azure Database for MySQL tárterület automatikus növelése az Azure CLI használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A [tárolási korlátot elérő](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)kiszolgáló csak olvasható értékre van állítva. Ha a Storage automatikus növekedése engedélyezve van, akkor a 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. Az [itt](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) megadott maximális tárolási korlátozások érvényesek.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ehhez az útmutatóhoz az Azure CLI 2,0-es vagy újabb verzióját kell használnia. A verzió megerősítéséhez az Azure CLI parancssorában adja meg `az --version`a következőt:. A telepítéshez vagy a frissítéshez lásd: az [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>A MySQL-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növekedésének engedélyezése egy meglévő kiszolgálón a következő paranccsal:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>További lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).