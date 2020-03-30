---
title: Lassú lekérdezési naplók elérése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk ismerteti, hogyan érheti el a lassú naplók az Azure Database for MariaDB az Azure CLI parancssori segédprogram használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f33a02ff0e287c135a7d63277cf3d8d3c0cd13d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527656"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával
Letöltheti az Azure Database for MariaDB lassú lekérdezési naplók segítségével Azure CLI, az Azure parancssori segédprogram.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- [Azure-adatbázis A MariaDB-kiszolgálóhoz](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy az Azure Cloud Shell a böngészőben

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Naplózás konfigurálása a MariaDB Azure Database szolgáltatásához
A következő lépésekkel konfigurálhatja a kiszolgálót a MariaDB lassú lekérdezési naplójának elérésére:
1. A naplózás bekapcsolása a **lassú\_lekérdezési\_napló** paraméter be állításával.
2. Módosítsa az egyéb paramétereket, például a **hosszú\_\_lekérdezési időt** és a lassú **\_\_rendszergazdai\_utasítások naplózását.**

A paraméterek azure CLI-n keresztüli értékének beállításáról a [Kiszolgálóparaméterkonfigurálása](howto-configure-server-parameters-cli.md)című témakörből megtudhatja.

A következő CLI parancs például bekapcsolja a lassú lekérdezési naplót, a hosszú lekérdezési időt 10 másodpercre állítja be, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az ellenőrzés konfigurációs beállításait.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>A MariaDB-kiszolgálóhoz való Azure Database naplóinak listázása
A kiszolgálóhoz rendelkezésre álló lassú lekérdezési naplófájlok listázásához futtassa az [az mariadb server-logs list parancsot.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

A kiszolgáló naplófájljait **mydemoserver.mariadb.database.azure.com** a **myresourcegroup**erőforráscsoport alatt listázhatja. Ezután irányítsa a naplófájlok listáját egy **list.txt nevű\_szövegfájlba.\_**
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) paranccsal letöltheti az egyes naplófájlokat a kiszolgálóhoz.

A következő példában letöltheti a kiszolgáló naplófájlját, **mydemoserver.mariadb.database.azure.com** a **myresourcegroup** erőforráscsoport erőforráscsoport alatt a helyi környezetbe.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- Ismerje meg a [lassú lekérdezési naplókat a MariaDB Azure Database szolgáltatásában.](concepts-server-logs.md)
