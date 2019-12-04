---
title: Lassú lekérdezési naplók elérése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan érheti el a lassú naplókat Azure Database for MariaDB az Azure CLI parancssori segédprogram használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 32e73835732538813f90de5cb737429373c3762a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767381"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával
Az Azure CLI-vel, az Azure parancssori segédprogrammal töltheti le az Azure Database for MariaDB lassú lekérdezési naplókat.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Azure Database for MariaDB naplózásának konfigurálása
A kiszolgálót úgy is beállíthatja, hogy a következő lépések végrehajtásával hozzáférhessen a MariaDB lassú lekérdezési naplóhoz:
1. A naplózás bekapcsolásához állítsa be a **lassú\_lekérdezés\_log** PARAMÉTERt a következőre:.
2. Módosítsa a többi paramétert, például a **hosszú\_lekérdezést\_időt** és a **naplót\_lassú\_rendszergazdai\_utasításait**.

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

A kiszolgálói **mydemoserver.MariaDB.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup**lehet kilistázni. Ezután irányítsa a naplófájlok listáját a **log\_Files nevű szövegfájlba\_Listázás. txt**fájlban.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az az [MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) paranccsal letöltheti a kiszolgáló egyes naplófájljait.

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.MariaDB.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- További információ [a Azure Database for MariaDB lassú lekérdezési naplóiról](concepts-server-logs.md).
