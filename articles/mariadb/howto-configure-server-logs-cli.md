---
title: Hozzáférési kiszolgáló naplófájljai Azure Database for MariaDB az Azure CLI használatával
description: Ez a cikk bemutatja, hogyan érheti el a kiszolgálói naplókat Azure Database for MariaDB az Azure CLI parancssori segédprogram használatával.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ffc724ef5133ee25643a966d2b6d8448a4c3a920
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023615"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>A kiszolgálói naplók konfigurálása és elérése az Azure CLI használatával
A Azure Database for MariaDB-kiszolgáló naplóit az Azure CLI, az Azure parancssori segédprogram használatával töltheti le.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Azure Database for MariaDB naplózásának konfigurálása
A kiszolgálót úgy is beállíthatja, hogy a következő lépések végrehajtásával hozzáférhessen a MariaDB lassú lekérdezési naplóhoz:
1. A naplózás bekapcsolásához állítsa be a **lassú @ no__t-1query @ no__t-2log** PARAMÉTERt a következőre:.
2. Módosítsa a többi paramétert, például a **Long @ no__t-1query @ no__t-2time** és a **log @ no__t-4slow @ no__t-5admin @ no__t-6statements**.

Ha meg szeretné tudni, hogyan állíthatja be a paraméterek értékét az Azure CLI-n keresztül, tekintse meg a [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-cli.md)című témakört.

A következő CLI-parancs például bekapcsolja a lassú lekérdezési naplót, beállítja a hosszú lekérdezési időt 10 másodpercre, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az Áttekintés konfigurációs beállításait.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló naplófájljainak listázása
A kiszolgáló rendelkezésre álló lassú lekérdezési naplófájljainak listázásához futtassa az az [MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) parancsot.

A kiszolgálói **mydemoserver.MariaDB.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup**lehet kilistázni. Ezután irányítsa a naplófájlok listáját egy **log @ no__t-1files\_list.txt**nevű szövegfájlba.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az az [MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) paranccsal letöltheti a kiszolgáló egyes naplófájljait.

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.MariaDB.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- További információ [a Azure Database for MariaDB lassú lekérdezési naplóiról](concepts-server-logs.md).
