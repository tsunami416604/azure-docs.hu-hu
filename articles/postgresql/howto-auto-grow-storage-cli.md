---
title: Automatikus tárhely - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti, hogyan konfigurálhatja a storage automatikus növekedés az Azure Database for PostgreSQL - Single Server használatával.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74767942"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Az Azure-adatbázis automatikus növekedése a PostgreSQL-tárhelyhez – Egyetlen kiszolgáló az Azure CLI használatával
Ez a cikk azt ismerteti, hogyan konfigurálhatja a PostgreSQL-kiszolgálói tárolóazure-adatbázisának növekedését a munkaterhelés befolyásolása nélkül.

A [kiszolgáló eléri a tárolási korlátot](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit), írásvédettre van állítva. Ha a tárterület automatikus növekedése engedélyezve van, majd a kiszolgálók kevesebb, mint 100 GB-os kiépített tároló, a kiosztott tárterület mérete 5 GB-tal nő, amint az ingyenes tárhely alatt van a nagyobb 1 GB vagy 10%-a kiosztott tárterület. A 100 GB-nál több kiosztott tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület 5%-kal nő, ha a szabad tárhely a kiosztott tárterület 5%-a alatt van. Az [itt](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) megadott maximális tárolási korlátok érvényesek.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Egy Azure-adatbázis a PostgreSQL-kiszolgálóhoz](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ez az útmutató megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját használja. A verzió megerősítéséhez írja be az Azure `az --version`CLI parancssorába a . A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése]( /cli/azure/install-azure-cli)című témakört.

## <a name="enable-postgresql-server-storage-auto-grow"></a>A PostgreSQL szervertárolás automatikus növekedésének engedélyezése

A kiszolgáló automatikusan növekszik tárolóhelyének engedélyezése meglévő kiszolgálón a következő paranccsal:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

A kiszolgáló automatikusan növekszik a tárolás engedélyezéséhez új kiszolgáló létrehozása közben a következő paranccsal:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>További lépések

További információ [arról, hogyan hozhat létre riasztásokat a mérőszámokon.](howto-alert-on-metric.md)